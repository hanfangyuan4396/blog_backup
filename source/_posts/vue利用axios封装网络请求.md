---
title: vue利用axios封装网络请求
date: 2022-11-20 09:53:48
reward: true
tags: 
    - vue
    - axios
---

## 安装axios

```
npm config set registry https://registry.npm.taobao.org # 设置成淘宝镜像源
npm i axios -S # 安装axios
```

## 封装网络请求

### 基础版
参考链接: [axios请求封装](https://blog.csdn.net/m0_61255374/article/details/122295726)
<!-- more -->
```javascript
import axios from "./axios.js";

let server = axios.create({
	// 请求公共地址
    baseURL: "http://localhost:8080", // url = baseURL + request url
    // 超时时间
    timeout: 1000 * 3,
})

// 请求拦截
server.interceptors.request.use(config => {
	// console.log(config); // config是一个包含了所有请求信息的对象 在这里可以修改config对象 修改之后需要返回config对象 请求才会正常进行
    config.headers.token = "asidoaslkd-12301jkwqmwlq-sadjalsmdl2"
    return config
}, err => {
    // throw new Error(err)
    Promise.reject(err)
})


// 响应拦截
server.interceptors.response.use(res => {
    // res 是服务器返回的数据信息
    // console.log(res);
    return res.data
}, err => {
    throw new Error(err)
})

// 封装请求 使用时在文件中直接导入 import { helloWorld } from '/your/path/request'
export function helloWorld() {
    return server({
      url: '/hello',
      method: 'get',
    })
}

```
### 进阶版
参考链接: [vue-admin-template](https://github.com/PanJiaChen/vue-admin-template)
在实际开发中，我们可能需要:
1. 开发环境与生产环境配置不同的base api
2. 在响应拦截器中统一处理异常的响应，并通知用户

#### 配置不同的base api
在vue项目根目录下创建文件 `.env.development`与`.env.production`

在通过`npm run dev`启动项目时，node会读取`.env.development`文件中的环境变量

在通过`npm run build`构建项目时，node会读取`.env.production`文件中的环境变量

```
# .env.development
VUE_APP_BASE_API = 'http://localhost:8000'
VUE_APP_BASE_API2 = 'http://hanfangyuan.cn/api'
```

```
# .env.production
VUE_APP_BASE_API = '/api' # 一般生产环境前后端域名相同，只写/api，发送请求时会使用前端的域名补全请求的url
```
在创建网络请求时，可以通过`process.env.VUE_APP_BASE_API`获取配置的api
```javascript
import axios from 'axios'

// 创建基本的axios网络请求
const server = axios.create({
  // 请求公共地址
  baseURL: process.env.VUE_APP_BASE_API,
  // 超时时间
  timeout: 1000 * 3
})
console.log('env:', process.env.NODE_ENV)
console.log('baseApi:', process.env.VUE_APP_BASE_API)

```
也可以通过`process.env.NODE_ENV`自行判断环境选择使用的api
```javascript
const server = axios.create({
  // 请求公共地址
  baseURL: process.env.NODE_ENV === 'production' ? process.env.VUE_APP_BASE_API : process.env.VUE_APP_BASE_API2,
  // 超时时间
  timeout: 1000 * 3
})
```
#### 拦截器中处理异常
用到了element-ui中的Message组件发送消息，需要提前安装
```
npm i element-ui -S
```

```javascript
// request.js
server.interceptors.response.use(
  response => {
    const res = response.data
    // 可以在后端接口中约定附带 自定义状态码字段 code
    // 如果code不是20000，视为异常响应，利用Message通知用户
    if (res.code !== 20000) {
      Message({
        message: res.message || 'error',
        type: 'error',
        duration: 5 * 1000
      })
      return Promise.reject(res.message || 'error')
    } else {
      return res
    }
  },
  error => {
    console.log('err' + error) // for debug
    Message({
      message: error.message,
      type: 'error',
      duration: 5 * 1000
    })
    return Promise.reject(error)
  }
)
```

#### 完整代码
```javascript
// request.js
import axios from 'axios'
import { Message } from 'element-ui'

// 创建基本的axios网络请求
const server = axios.create({
  // 请求公共地址
  baseURL: process.env.VUE_APP_BASE_API,
  // 超时时间
  timeout: 1000 * 3
})
console.log('env:', process.env.NODE_ENV)
console.log('baseApi:', process.env.VUE_APP_BASE_API)

// 请求拦截
server.interceptors.request.use(config => {
  return config
}, err => {
  // throw new Error(err)
  console.log(err) // for debug
  Promise.reject(err)
})

server.interceptors.response.use(
  /**
     * If you want to get http information such as headers or status
     * Please return  response => response
     */

  /**
     * Determine the request status by custom code
     * Here is just an example
     * You can also judge the status by HTTP Status Code
     */
  response => {
    const res = response.data
    // console.log(res)
    // if the custom code is not 20000, it is judged as an error.
    if (res.code !== 20000) {
      Message({
        message: res.message || 'error',
        type: 'error',
        duration: 5 * 1000
      })
      return Promise.reject(res.message || 'error')
    } else {
      return res
    }
  },
  error => {
    console.log('err' + error) // for debug
    Message({
      message: error.message,
      type: 'error',
      duration: 5 * 1000
    })
    return Promise.reject(error)
  }
)

// 封装请求
export function getCodeRequest () {
  return server({
    url: '/code/list',
    method: 'get'
  })
}

```