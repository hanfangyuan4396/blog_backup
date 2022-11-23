---
title: vue+nginx刷新404问题
date: 2022-11-22 20:49:55
tags:
    - vue
    - nginx
---

先说初步得到的结论，这只是我根据测试结果的推测，并没有阅读源码探究原因。在nginx如下配置中，有'/index'路由匹配规则
```
location /index {
    alias  /home/hfy/dist;
    index  index.html;
}
```
由于'/index'中的**index为关键字，导致路由匹配发生异常**，与预期不符，把'/index'更改为'/home'，恢复正常


## 背景介绍

vue项目只有一个组件，路由模式是history，路由中有一个根路径重定向配置，路由配置如下
```javascript
const routes = [
  {
    path: '/',
    redirect: '/index'
  },
  {
    path: '/index',
    name: 'index',
    component: () => import('../views/IndexView.vue')
  }
]

const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})
```

<!-- more -->
vue项目build之后，使用nginx部署build之后的产物，nginx路由配置如下

```

location / {
    root   /home/hfy/dist;
    index  index.html;
}

location /index {
    alias  /home/hfy/dist;
    index  index.html;
}

```
/home/hfy/dist文件夹中存放的是build产物，包含css、fonts、js文件夹以及favicon.ico、index.html

我添加上述 `location /index`规则是想解决刷新页面后404问题

即通过url: example.com访问时，加载到index.html之后，前端路由会重定向到example.com/index，这时候刷新页面，浏览器会请求example.com/index。如果不添加`location /index`规则，nginx无法匹配到/index，会返回404。

但是我添加该规则后遇到了下面的问题一。

## 问题描述
### 问题一
通过url：example.com 访问该项目，nginx提示404

通过url: example.com/index 访问该项目，可以正常访问

### 问题二
如果把nginx路由配置更改为
```

location / {
    root   /home/hfy/dist;
    index  index.html;
}

#location /index {
#    alias  /home/hfy/dist;
#    index  index.html;
#}

```

通过url: example.com 访问该项目，可以正常访问

通过url: example.com/index 访问该项目，nginx提示404

## 原因分析

### 问题一
问题一我无法理解

- 通过url: example.com/index 访问该项目时，命中规则
```
location /index {
    alias  /home/hfy/dist;
    index  index.html;
}
```

/home/hfy/dist文件夹中存在index.html，所以访问的是/home/hfy/dist/index.html

- 通过url: example.com 访问该项目时，命中规则
```
location / {
    root   /home/hfy/dist;
    index  index.html;
}
```
应该访问到/home/hfy/dist/index.html，**但是却提示404， 无法理解**


### 问题二
问题二我是这样理解的，location /index规则被注释掉不起作用

- 通过url: example.com 访问项目时，命中规则

```
location / {
    root   /home/hfy/dist;
    index  index.html;
}
```
域名会被root替换，访问的是/home/hfy/dist/中的index.html

- 通过url: example.com/index 访问项目时，同样命中上述规则，域名被root替换，访问的是/home/hfy/dist/中的index文件或者index文件夹，但是并不存在index文件或者index文件夹，所以报错404

# 排查原因





