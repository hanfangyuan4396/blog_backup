---
title: vue脚手架创建项目
date: 2022-11-18 17:57:28
tags: vue
---


### 安装脚手架
```shell
npm config get registry # 查看镜像源
npm config set registry https://registry.npm.taobao.org # 设置成淘宝镜像源
npm install -g @vue/cli
```

### 创建项目
```shell
vue create <project-name>
```

<!-- more -->
### 路由设置
```html
<router-link to="/home">首页</router-link>
<router-view></router-view>
```
`<router-link>`标签会更改路由，`<router-view>`标签会显示出路由对应的组件

```javascript
// router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    redirect: '/index'
  },
  {
    path: '/index',
    name: 'index',
    component: () => import('../views/IndexView.vue') // 懒加载
  }
]

const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})

export default router

```

**路由嵌套**
```javascript
const routes = [
  {
    path: '/foo',
    component: Layout, // App.vue中有<router-view>, Layout.vue中也存在<router-view>
    children: [
      {
        path: 'bar1',
        component: () => import ('xxx.vue'),
      },
      {
        path: 'bar2',
        component: () => import ('xxx.vue'),
      }
    ]
  }
]
```
