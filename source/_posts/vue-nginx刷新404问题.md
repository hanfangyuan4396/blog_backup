---
title: vue+nginx刷新404问题
date: 2022-11-22 20:49:55
reward: true
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


# 1. 背景介绍

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

# 2. 问题描述
## 2.1 问题一
通过url：example.com 访问该项目，nginx提示404

通过url: example.com/index 访问该项目，可以正常访问

## 2.2 问题二
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

# 3. 原因分析

## 3.1 问题一

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
应该能访问到/home/hfy/dist/index.html，**但是却提示404， 无法理解**，在**4.排查问题一原因**中将介绍如何找到该问题原因


## 3.2 问题二
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

# 4. 排查问题一原因

我之前还写过另一个vue项目，跟这个项目类似，前端路由中同样使用了history模式以及重定向，nginx配置也类似，但是却没有出现404问题，那我就把这两个项目做对比，排查原因。为了方便叙述，把出404问题的项目叫做项目一，未出问题的项目称项目二。如下是项目二的配置。
```javascript
// 前端路由配置
const routes = [
  {
    path: '/',
    redirect: '/home'
  },
  {
    path: '/home',
    name: 'home',
    component: () => import('../views/HomeView.vue')
  }
]
```


```
# nginx配置
location / {
    root   /home/hfy/other_dist;
    index  index.html;
}

location /home {
    alias  /home/hfy/other_dist;
    index  index.html;
}

```

我怀疑可能出问题的地方如下
1. 由于项目一与项目二vue-router等依赖的版本不同，项目一的index.html可能有问题
2. 项目一的nginx虚拟主机配置有问题

为了验证1，我把项目二nginx配置`location /` root指向项目一，发现可以通过/ 访问到index.html，说明项目一的index.html文件没问题

为了验证2，我把项目一的`location`规则换成项目二的规则，在更换的过程中，发现只把`location /` root指向项目二，通过/访问不到项目二，但是把`location /index`换成`location /home` alias指向项目二，突然可以访问到项目二了。说明项目一的虚拟主机配置没问题，这时候也意识到了`location /index`中index是关键字，关键字不能直接写在location匹配url中

# 5. 反思
在实际排查问题一时，并不是这么简单，而是想到什么测试什么，这样测试比较混乱，容易重复测试，或者漏掉测试，这样测试得出的结论也不自信，也容易忘记测试的结果。

应该提前想好可能是哪里出现问题了，并一一罗列出来，根据这些问题设计出测试方法，并想好每一种测试结果能推测出的结论。还要及时做记录，防止遗忘。

发现浏览器隐私模式也会使用缓存，在测试问题前要清理浏览器缓存。

# 6. nginx root与alias的使用
在排查问题时详细分析了nginx root与alias的用法: [详解nginx的root与alias](https://blog.hanfangyuan.cn/2022/11/23/详解nginx的root与alias/)

# 7. 更优雅地解决vue网页浏览器刷新404

## 7.1 try_files
上面的解决方法是在nginx中为vue的一个路由单独配置location规则，并依靠alias正确返回index.html文件，即构建好的前端页面。如果前端路由比较少，这种方法还能够接受。如果前端有十几个甚至几十个路由，就需要在nginx中同步配置这些路由，显然太麻烦了。

在[Vue Router官网](https://router.vuejs.org/zh/guide/essentials/history-mode.html#html5-%E6%A8%A1%E5%BC%8F)中提到了history路由模式下浏览器刷新404的问题
>当使用这种历史模式时，URL 会看起来很 "正常"，例如 https://example.com/user/id 漂亮!

> 不过，问题来了。由于我们的应用是一个单页的客户端应用，如果没有适当的服务器配置，用户在浏览器中直接访问 https://example.com/user/id ，就会得到一个 404 错误。这就尴尬了。

官方给出了nginx的解决方案

```
location / {
  try_files $uri $uri/ /index.html;
}
```
try_files指令的详细解析可以参考文章[Nginx的try_files指令详解](https://blog.csdn.net/zzhongcy/article/details/110181195)，其作用是按顺序检查文件是否存在，返回第一个找到的文件或文件夹(结尾加斜线表示为文件夹)，如果所有的文件或文件夹都找不到，会进行一个内部重定向到最后一个参数。
 
 我们以https://example.com/user/id 为例。
 
 try_files首先会在nginx指定的root目录下寻找$uri文件。假设root设置为/home/hfy/，那么就是寻找/home/hfy/$uri，其中的$uri是url中的路径，即/user/id。所以最终的访问路径是/home/hfy/user/id。

 如果不存在id这个文件，就继续判断是否存在/home/hfy/user/id/文件夹。如果仍然不存在，就返回指令的最后一个参数，root文件夹中的/index.html文件，即/home/hfy/index.html

### root与alias作用域

我们看到官方给的配置中，并未出现root
```
location / {
  try_files $uri $uri/ /index.html;
}
```

root可以在location内部指定，只针对该location规则生效，也可以写在location外server内，作用域是整个server，但是location内的root会覆盖掉外部root。如果没有设置root，默认是nginx安装目录的html文件夹。比如我的是/usr/local/nginx/html/

alias只能写在location内部。

## 7.2 error_page 404

在nginx设置
```
error_page 404 /index.html;
```
同样可以在刷新时正确返回index.html页面，这样做是在投机取巧。虽然返回了正确页面，但是在浏览器控制台的网络请求中可以看到响应的状态码仍然是404，所以推荐try_files的方法。

## 7.3 前端配置404

我们在nginx使用try_files指令解决了404问题，但是也带来了新的问题。假设用户访问前端并不存在的路由比如http://example.com/not/exist ，但nginx仍然返回了index.html，用户并不知道发生了404错误。这就需要在前端专门提供一个404页面，当用户访问不存在的路由时，展示该页面。

vue router配置如下
```javascript
const HomeView = () => import('@/views/HomeView')
const ErrorView = () => import('@/views/404View')
const routes = [
  {
    path: '/',
    redirect: '/home'
  },
  {
    path: '/home',
    name: 'home',
    component: HomeView
  },
  // 增加404路由与页面，测试发现path: '*' 不是必须写在最后的
  {
    path: '/404',
    component: ErrorView
  },
  {
    path: '*',
    redirect: '/404'
  }
]
```
