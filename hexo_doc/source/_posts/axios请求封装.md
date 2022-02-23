---
title: axios请求封装
date: 2022-02-10 11:49:28
tags: [javascript,axios]
categories: axios
---

## axios请求封装

### 1. 在项目中下载axios依赖包

```javascript
npm i --save axios
```

### 2.新建一个axios实例，并根据自己的需要自定义配置

```javascript
const axiosInstance = axios.create({
  baseURL: process.env.VUE_APP_BASE_API, // url = base url + request url
  withCredentials: false, // send cookies when cross-domain requests
  headers: {
    "version": "auth-v2.0.0",
    "Content-Type": "application/json;",
    "Pragma": "no-cache"
  },
  timeout: 1000 * 100, // request timeout
  validateStatus: status => {
    // 如果 `validateStatus` 返回 `true`，promise 将被 resolve
    return status >= 200 && status < 300 // default
  }
})
```

### 3.封装请求节流控制方法

```javascript
let usedParams = {}
// 节流控制
const throttleParams = (options) => {
  // 随便一种加密方式都可，推荐加密的字符串不要过长
  const key = SHA256(JSON.stringify(options)).toString()
  const dateNow = Date.now()
  // 大于 50 条数据则清理一次，清除时间较久的数据
  if (Object.keys(usedParams).length > 50) {
    const tempUsedParams = {}
    Object.keys(usedParams).forEach((prop) => {
      if (dateNow - usedParams[prop] < 500) {
        tempUsedParams[prop] = usedParams[prop]
      }
    })
    usedParams = tempUsedParams
  }
  if (usedParams[key] && (dateNow - usedParams[key] < 500)) {
    return false
  }
  usedParams[key] = dateNow
  return true
}
```

### 4.loading加载状态封装

```javascript
let requestCount = 0

// 显示loading
const showLoading = () => {
  if (requestCount === 0) {
    // document.getElementById("loading")!.style.display = "flex"
    const dom = document.createElement("div")
    dom.setAttribute("id", "loading")
    document.body.appendChild(dom)
    // Spin为antd的加载组件
    ReactDOM.render(<Spin tip="" size="large"/>, dom)
  }
  requestCount++
}

// 销毁或隐藏loading
const hideLoading = () => {
  requestCount--
  if (requestCount === 0) {
    // document.getElementById("loading")!.style.display = "none"
    document.body.removeChild(document.getElementById("loading"))
  }
}

```

### 5.错误处理

- 根据返回的status进行不同的处理

```javascript
import { message, Modal } from "antd"

// isModal保证只出现一个弹窗
let isModal = true

function getProcessStatus(response) {
  console.log("status", response)
  switch (response.status) {
    case 100:
      message.error("请继续请求")
      break
    case 101:
      message.error("请升级协议")
      break
    case 200:
      console.log("请求成功")
      break
    case 204:
      message.error("no content没有资源可以返回")
      break
    case 206:
      message.error("partical content进行部分范围请求")
      break
    case 301:
      message.error("永久重定向，请求的页面已经转移到新的url")
      break
    case 302:
      message.error("临时重定向，请求的页面已经临时转移到新的url")
      break
    case 303:
      message.error("临时重定向，请求的页面可以在别的url找到, 必须使用get方法")
      break
    case 304:
      message.error("资源未被修改，缓存文档可以继续使用，主要用于协商缓存")
      break
    case 305:
      message.error("使用代理，所请求的资源必须通过代理访问")
      break
    case 400:
      message.error(response.data?.msg || response.data?.message || "请求错误，服务器未能理解请求")
      break
    case 401:
      if (isModal) {
        isModal = false
        Modal.confirm({
          title: "警告",
          content: "用户没有权限或登陆超时请重新登陆！",
          okText: "确认",
          cancelText: "取消",
          onOk() {
            isModal = true
          },
          onCancel() {
            isModal = true
          }
        })
      }
      break
    case 403:
      message.error(response.data?.msg || response.data?.message || "禁止访问，权限不够")
      break
    case 404:
      message.error("请求的接口不存在")
      break
    case 405:
      message.error("请求指定的方法不被允许")
      break
    case 408:
      message.error("请求超时") // 408
      break
    case 500:
      message.error(response.data?.msg || response.data?.message || "网络错误")
      break
    case 502:
      message.error("网关错误") // 502
      break
    case 503:
      message.error("服务器过载") // unavaliable是有效的意思
      break
    case 504:
      message.error("网关超时") // 504
      break
    default:
      message.error(response.data?.message || "请求错误")
      break
  }
}
```

### 6.axios请求方法封装

```javascript
const requestAsync = async(optionsAxios) => {
  // 节流
  if (throttleParams(optionsAxios)) {
    // 显示loading
    showLoading()
    const responseService = await axiosInstance(optionsAxios).catch((err) => {
      // 隐藏loading
      hideLoading()
      // 错误处理
      getProcessStatus(err.response)
    })
    if (responseService) {
      // 隐藏loading
      hideLoading()
      const { data, status, headers } = responseService
      console.log("responseService::", responseService)
      if (status < 200 && status > 300) {
         // 错误处理
        getProcessStatus(responseService)
        data.code = status
        return data
      }
      let res = {}
      
      // 根据自己的需要处理返回的data
      if (typeof data === "string") {
        res = {
          code: 200,
          data: data
        }
        return res
      }
      if (typeof data === "object" && String(data) !== "null" && data.length) {
        res = {
          code: 200,
          list: data,
          total: Number(headers["x-total-count"])
        }
        return res
      }
      data.code = 200
      return data
    }
  }
  return ({})
}

export default requestAsync
```

### 7.调用封装的axios方法

```javascript
import requestAsync from "@/api/axios"

// params为请求参数
export async function getDetailFn(params) {
  return await requestAsync({
    method: "GET",
    url: `/api/list/detail`,
    params
  })
}

export async function postAddArtifact(params) {
  return await requestAsync({
    method: "POST",
    url: `/api/list/add`,
    data: params
  })
}
```

### 8.调用api方法

```javascript
import { getDetailFn, postAddArtifact } from "@/api/list"

getDetailFn({ id: 1 })
  .then(res => {
  	if (res.code === 200) {
    	console.log(res)
  	}
	})

const func = async () => {
  const res = await postAddArtifact({ name: "123", skuName: "456" })
  if (res.code === 200) {
    console.log(res)
  }
}
```



