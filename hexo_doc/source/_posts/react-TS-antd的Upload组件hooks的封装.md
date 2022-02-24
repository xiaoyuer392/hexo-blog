---
title: react+TS+antd的Upload组件hooks的封装
date: 2022-02-24 10:05:28
tags: [javascript,react,antd]
categories: [react,antd]
---

#### 1、在项目中下载antd依赖包

```bash
# npm下载方式
npm install antd --save
# yarn下载方式
yarn add antd
```

#### 2、封装上传文件接口请求

```javascript
// 前几章封装的axios请求方法
import requestAsync from "@/api/axios"

export async function postFile(params: any) {
  const formFile = new FormData()
  formFile.append("file", params.file)
  return await requestAsync({
    method: "POST",
    url: `/upload/common/file/uploadFile`,
    data: formFile
  })
}
```

#### 3、hooks方法封装

```typescript
import { message, Upload } from "antd"
import { RcFile } from "antd/lib/upload"
import { useCallback, useState } from "react"
import { postFile } from "@/api/common"

/**
 * @param {any} form 表单form实例
 * @param {string} key 表单上传文件字段的key值,也就是Form.Item的name值
 * @param {string} accept 上传文件格式用,号分割
 * @returns Upload常用的公共方法
 */
export const useUploadFileProps = (form: any, key: string, accept = "") => {
  const [fileListSelf, setFileListSelf] = useState<any[]>([])
  const [loading, setLoading] = useState<boolean>(false)
  const beforeUpload = useCallback((file: RcFile) => {
    const fileNameType = file.name.substring(file.name.lastIndexOf("."))
    const isJpgOrPng = accept ? accept.split(",").includes(fileNameType) : (file.type === "image/jpeg" || file.type === "image/png")
    if (!isJpgOrPng) {
      accept ? message.error(`您只能上传${accept}格式的文件`) : message.error("您只能上传JPG/PNG格式文件!")
    }
    const isLt10M = file.size / 1024 / 1024 < 20
    if (!isLt10M) {
      message.error("文件大小不能超过20MB!")
    }
    return (isJpgOrPng && isLt10M) ? true : Upload.LIST_IGNORE
  }, [])
  // 自定义上传
  const customRequest = useCallback(async(options:any) => {
    setLoading(true)
    const { onSuccess, file, onError } = options
    console.log("customRequest", options)
    const res = await postFile({ file: file })
    console.log("res file::", res)
    if (res.code === 200) {
      setLoading(false)
      onSuccess({
        uid: file.uid,
        filename: file?.name,
        status: "done",
        url: res.data?.url,
        thumbUrl: res.data?.url
      })
    } else {
      setLoading(false)
      onError("上传失败！")
    }
  }, [])
  // 预览跳转新页面
  const onPreview = (file:any) => {
    console.log("preview file::", file)
    window.open(file.response?.url || "", "_blank")
  }
  // 文件渲染
  const itemRender = (originNode: any, file: any, fileList: any, actions: any) => {
    console.log(fileList)
    return file.status === "done" && originNode
  }
  const onChange = (info:any) => {
    console.log("info", info)
    const newData = {}
    setFileListSelf(list => {
      return info.fileList.filter((item:any) => {
        return item.status === "done"
      })
    })
    newData[key] = info.fileList.filter((item:any) => {
      return item.status === "done"
    })
    // 给form表单的文件列表赋值
    form.setFieldsValue({ ...newData })
  }
  return { beforeUpload, loading, customRequest, itemRender, fileListSelf, onChange, onPreview }
}

```

#### 4、hooks方法的useUploadFileProps的调用

```tsx
import React from "react"
import { Modal, Form, Input, Upload } from "antd"
import { useUploadFileProps } from "@/hooks/useUploadFileProps"
import { PlusOutlined } from "@ant-design/icons"
function FileModal(props: any): React.ReactElement {
  const [form] = Form.useForm()
  const uploadProps = useUploadFileProps(form, "fileList", ".pdf,.png,.jpeg,.jpg")
  const uploadButton = (
    <div>
      <PlusOutlined />
      <div style={{ marginTop: 8, fontSize: "10px" }}>上传附件</div>
    </div>
  )
  return (
    <Modal
      visible={props.visiable}
      title="上传示例"
      okText="确定"
      cancelText="取消"
      onCancel={props.onCancel}
      onOk={() => {
        form
          .validateFields()
          .then((values) => {
            console.log("value::", values)
            form.resetFields()
          })
          .catch((info) => {
            console.log("Validate Failed:", info)
          })
      }}
    >
      <Form
        form={form}
        name="form_in_modal"
        labelCol={{ span: 4 }}
        wrapperCol={{ span: 18 }}
      >
        <Form.Item
          name="fileList"
          label="添加附件"
          rules={[{ required: true, message: "请上传附件!" }]}
          extra="注：上传清晰完整图片，上传图片格式要求 jpg、jpeg、pdf、png，不超过20M。"
          valuePropName="fileListSelf"
        >
          <Upload
            name="logo2"
            accept=".pdf,.png,.jpeg,.jpg"
            action="#"
            onChange={uploadProps.onChange}
            beforeUpload={uploadProps.beforeUpload}
            customRequest={uploadProps.customRequest}
            itemRender={uploadProps.itemRender}
            onPreview={uploadProps.onPreview}
            listType="picture-card"
          >
            {
              uploadProps.loading ? null : uploadButton
            }
          </Upload>
        </Form.Item>
      </Form>
    </Modal>
  )
}
export default FileModal
```



