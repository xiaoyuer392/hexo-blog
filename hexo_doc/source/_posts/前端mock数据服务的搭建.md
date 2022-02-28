---
title: 前端mock数据服务的搭建
date: 2022-02-28 14:45:38
tags: [javascript,mock]
categories: mock
---

#### 1、用npm下载json-server的插件包

```bash
npm install json-server -D
```

#### 2、在根目录下mock文件夹，然后创建db.json文件

```json
{
  此处省略...,
  "volunteersList": [
    {
      "id": 1,
      "eventId": 9862,
      "activeName": "文明城市创建宣传支援服务活动",
      "userName": "高宁",
      "availableNum": 20,
      "eventStatus": "已完成",
      "startDate": "2021-06-20 10:11:23",
      "doneDate": "2021-06-20 13:12:00"
    },
    {
      "id": 3,
      "eventId": 9862,
      "activeName": "社区卫生清洁志愿者活动",
      "userName": "郄建寒",
      "availableNum": 40,
      "eventStatus": "已完成",
      "startDate": "2021-06-20 10:11:23",
      "doneDate": "2021-06-20 13:32:00"
    },
    {
      "id": 4,
      "eventId": 9862,
      "activeName": "科学普及志愿服务",
      "userName": "姬利学",
      "availableNum": 30,
      "eventStatus": "已完成",
      "startDate": "2021-06-20 15:11:44",
      "doneDate": "2021-06-20 19:12:00"
    },
    ...此处省略
  ],
}
```

#### 3、在根目录下的package.json文件中写入启动服务的脚本命令

```json
{
  ...,
  "scripts": {
    ...
    "mock": "nohup json-server --watch ./mock/db.json --port 9999 &",
    ...
  },
	...,
}
```

#### 4、启动mock服务

```bash
npm run mock
```

#### 5、GET请求方法

- 查询普通列表

  ```http
  http://localhost:9999/volunteersList
  ```

  {% asset_img 1.jpg %}

- 根据`id`查询某条数据

  ```http
  http://localhost:9999/volunteersList/:id
  ```

  {% asset_img 2.jpg %}

- 根据`_page`和`_limit`进行分页查询

  ```http
  http://localhost:9999/volunteersList?_page=2&_limit=3
  ```

  {% asset_img 3.jpg %}

- 根据`[key值]_like`进行模糊查询

  ```http
  http://localhost:9999/volunteersList?activeName_like=科学
  ```

  {% asset_img 4.jpg %}

#### 6、POST请求方法

- 在body中设置参数，新增成功后会返回刚刚新增的数据

- 参数：

  ```json
  {
        "eventId": 9111,
        "activeName": "社区“居民学校”和“会议培训”",
        "userName": "敖友",
        "availableNum": 30,
        "eventStatus": "已完成",
        "startDate": "2021-06-20 10:11:23",
        "doneDate": "2021-07-31 23:12:00"
  }
  ```

- 请求示例：

  ```http
  http://localhost:9999/volunteersList
  ```

  {% asset_img 5.jpg %}

#### 7、PUT请求方法

- 根据`id`进行修改

  ```http
  http://localhost:9999/volunteersList/17
  ```

  {% asset_img 6.jpg %}

#### 8、DELETE请求方法

- 根据`id`进行删除

  ```http
  http://localhost:9999/volunteersList/17
  ```

  {% asset_img 7.jpg %}
