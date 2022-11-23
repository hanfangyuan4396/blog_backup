---
title: 详解nginx的root与alias
date: 2022-11-23 19:34:28
tags: nginx
---

nginx版本: 1.18.0
# 1. 结论

location命中后

如果是root，会把请求url的 `ip/域名+port`替换为root指定的目录，访问资源

如果是alias，会把请求url的`ip/域名+port+匹配到的路径`替换为alias指定的目录，访问资源
<!-- more -->

# 2. 详解root

## 2.1 基本用法
以请求http://example.com/foo/bar/hello.html为例，location配置如下
```
location /foo {
    root /home/hfy/;
}
```
匹配到/foo，url的`域名+port`替换为root指定的目录，即url中的examp.com被替换为了/home/hfy，所以实际访问的路径为`/home/hfy/foo/bar/hello.html`

为了更好理解，再来一个例子，请求的url不变，location配置更改为
```
location /foo/bar {
    root /home/hfy/;
}
```
匹配到/foo/bar，url的`域名+port`替换为root指定的目录，即url中的examp.com被替换为了/home/hfy，所以实际访问的路径仍然为`/home/hfy/foo/bar/hello.html`。**root在替换时不会替换匹配到的路径**。

## 2.2 location的最左匹配原则
location会从url最左边的路径匹配，如果一致则命中该location。只有中间匹配到不会命中。
比如请求的url为http://example.com/foo/bar/hello.html，location为
```
location /bar {
    root /home/hfy/;
}
```
不会命中该location，因为从url中的/foo开始匹配，与`location /bar`不一致，不会命中，如果url更改为http://example.com/bar/hello.html 才会命中该规则

## 2.3 index
在location内部其实默认配置了一条规则`index index.html`，补全后的规则如下
```
location /foo {
    root /home/hfy/;
    index index.html;
}
```

假设我们访问的url为http://example.com/foo/bar，匹配到/foo，实际访问的路径为/home/hfy/foo/bar。如果我们的bar是一个文件夹，其中如果包含index.html文件，则会把该文件返回。所以index的作用是，当实际访问的是一个目录时，会返回该目录中index指定的文件，如果该目录中不存在index指定的文件，则会返回403。

在访问http://example.com/foo/bar，时我们打开浏览器的控制台，查看发送的请求，会发现发生了一个301重定向，http://example.com/foo/bar 被重定向为http://example.com/foo/bar/ ，由此引发了新的问题，为什么会发生重定向，url末尾的/，location 匹配路径末尾的/，以及root 指定目录末尾的/都表示什么意思

## 2.4 末尾'/'
事先声明，仅是我个人粗浅的理解，根据对不同情况的测试，尝试总结 '/'的含义

- url末尾/的含义

http://example.com/foo/bar 表示我们把bar当成一个文件，想要访问bar文件
http://example.com/foo/bar/ 表示我们把bar当成一个目录，想要访问bar目录下index指定的文件

- location 匹配路径末尾/的含义
```
location /foo {
    root /home/hfy/;
    index index.html;
}
```
/foo 既能匹配http://example.com/foo 也能匹配 http://example.com/foo/

```
location /foo/ {
    root /home/hfy/;
    index index.html;
}
```
/foo/只能匹配http://example.com/foo/

- root 指定目录末尾/的含义
```
location /foo {
    root /home/hfy/;
    index index.html;
}
```
/home/hfy 与 /home/hfy/没有区别(不过alias有种特殊情况，后面会提到)，都表示hfy是个目录，并且就应该是个目录。

对于这三个斜杠，在实践中可以有如下使用方案

1. url末尾不加/，如果需要带/时依靠nginx自动帮我们重定向加/
2. location 路径不加/，这样末尾有无/的url都能匹配到
3. root或者alias指定的目录后面加/，明确表示root指定的是目录，增强配置的可读性

现在我们在回头看一下2.3中另一个问题，在访问http://example.com/foo/bar 为什么发生了301重定向









假设url为http://example.com/foo/bar 

location为
```
location /foo {
    root /home/hfy/;
    index index.html;
}
```



# 3. 详解alias
## 3.1 基本用法
以请求http://example.com/foo/bar/hello.html为例，location配置如下
```
location /foo {
    alias /home/hfy/;
}
```
匹配到/foo，url的`ip/域名+port+匹配到的路径`替换为alias指定的目录，即url中的example.com/foo被替换为了/home/hfy，所以实际访问的路径为`/home/hfy/bar/hello.html`

同样再来一个例子，请求的url不变，如果location配置更改为
```
location /foo/bar {
    alias /home/hfy/;
}
```
匹配到/foo/bar，url的`ip/域名+port+匹配到的路径`替换为alias指定的目录，即url中的example.com/foo/bar被替换为了/home/hfy，所以实际访问的路径为`/home/hfy/hello.html`。**alias在替换时会替换匹配到的路径**。

与root一样，alias的location同样遵循最左匹配原则