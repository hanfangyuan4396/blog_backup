---
title: nginx快速上手
date: 2020-09-06 15:46:00
reward: true
tags: nginx
---

# 使用ubuntu18.04安装部署nginx
### 1. 使用apt安装nginx
`sudo apt install nginx`
### 2. nginx启动方式
##### 2.1 普通启动
`nginx`
<!-- more -->
##### 2.2 使用配置文件启动
`nginx -c <nginx conf文件>`
##### 2.3 nginx是否启动检查
`ps -ef|grep nginx`
nginx程序包括master进程与worker进程多个进程
##### 2.4 查看端口占用情况
`sudo netstat -nutp`
-n 拒绝显示别名，能显示数字的全部转化成数字
-u (udp)仅显示udp相关选项
-t (tcp)仅显示tcp相关选项
-p 显示建立相关链接的程序名
### 3. nginx停止方式
##### 3.1 正常停止(服务完现有的请求再停止)
`ps -ef|grep nginx` 查看master pid
`kill -QUIT <master pid>`
##### 3.2 强制停止(立即停止，不处理现有请求)
`ps -ef|grep nginx` 查看master pid
`kill -TERM <master pid>`

### 4. 其他常用命令
##### 4.1 重启nginx
`nginx -s reload`
##### 4.2 配置文件检查
`nginx -c <nginx conf文件> -t`

##### 4.3 nginx版本查看
`nginx -v` 
`nginx -V`
### 5. nginx文件结构
配置文件 /etc/nginx
程序文件 /usr/sbin/nginx
日志文件 /var/log/nginx
网页文件 /usr/share/nginx/html
### 6. nginx配置文件nginx.conf分析
nginx.conf可以划分为下面三部分
##### 6.1 基本配置
worker进程运行用户
worker进程数目
日志
主进程pid(指定master pid，方便用kill关闭，而不用每次都先用ps查找pid)
##### 6.2 events配置
工作模式
连接数
##### 6.3 http配置
**基本配置**
支持的多媒体类型
文件默认类型
文件传输模式
长连接超时时间
日志格式
日志存放路径
网络阻塞
gzip压缩


**server配置**
监听端口
服务器名
字符集
访问日志
```
location / {
	root html;
	index index.html index.htm;
}
 1）访问路径中有'/' 会被 'location / {}' 匹配
 2）ip+端口号 替换为 root指定的路径
```
**alias**
```
 location /video  {
	alias /home/hfy/sites/video/front
	index index.html
}
 1）访问路径中有'/video 会被 'location /video {}' 匹配
 2）ip+端口号还有video会被替换为 alias指定的路径
 比如访问 test.com/video/index.html 会被替换为 /home/hfy/sites/video/front/index.html
```
**开启显示目录列表功能**
目录列表功能有什么作用呢？
我们看一下这种情况，我们要访问test.com/video，根据下方匹配规则，会映射到文件/home/hfy/sites/video/front/index.html
```
 location /video  {
	alias /home/hfy/sites/video/front
	index index.html
}
```
规则中的index index.html 就是返回front目录下的index.html 文件，当然也可以指定为index2.html文件
如果不指定index的话，就会默认返回index.html
而如果front目录中没有index.html文件呢?这就是显示目录列表功能的作用，如果打开该功能，在目录中没有index.html文件的情况下，就会显示这个目录下的文件列表；如果没有打开该功能，nginx就会给浏览器提示403 forbidden。

目录列表功能配置如下

```
server {
        autoindex on;
        autoindex_exact_size off;
        autoindex_localtime on;
        charset utf-8;

        root /home/hfy;
        listen       80;
        
        location / {
            root   /home/hfy/sites;
            index  index.html;
        }
        location /video {
            root   /home/hfy/sites;
            index  index.html;
        }
}
```

>1.目录显示功能配置解释
autoindex on;　　　　　　　　 自动显示目录
autoindex_exact_size off;　　　人性化方式显示文件大小否则以byte显示
autoindex_localtime on;　　　   按服务器时间显示，否则以gmt时间显示
charset utf-8;                               防止文件名乱码

>2.root /home/hfy配置解释
该配置在location外，server内，对这个server起作用。经实验发现，如果没有写 location /匹配规则的话，直接访问网站
test.com的话就会访问到server 内的配置 root 指定的文件夹。root /home/hfy;与location / 作用一致。
如果root与location / 都存在的话，location / 配置会覆盖root配置
比如按照上方的配置
/home/hfy/ 有test文件夹
而/home/hfy/sites 没有test文件夹
我们访问 test.com/test 并不会访问到 /home/hfy/test文件夹，而是先匹配location，与location / 匹配上了，根据规则，会把
test.com/test 映射到 /home/hfy/sites/test，但是没有这个文件夹，会返回not found 404

### 8. 静态文件访问配置
```
 location ~ .*\.(js|css|htm|html|gif|jpg|jpeg|png|mp3)$  {
	root /opt/static;
}
```
# 使用docker部署nginx
docker简单命令可参考链接：[docker学习笔记](https://blog.csdn.net/weixin_44387339/article/details/107285351)
### 1. 下载nginx docker镜像
`docker pull nginx:1.17.8`
### 2. 运行测试nginx容器
`sudo docker run --name nginx -p 80:80 -d nginx:1.17.8` 开启nginx容器
`docker ps`查看是否运行成功
访问容器宿主机ip
### 3. 配置部署nginx容器
##### 3.1 宿主机创建对应目录
```
# 创建html目录
mkdir -p ~/server/nginx/html
# 创建⽇志目录
mkdir -p ~/server/nginx/logs
# 创建配置目录
mkdir -p ~/server/nginx/conf
```
##### 3.2 nginx容器内配置文件复制到宿主机
`docker cp <容器名称或id:容器内源文件> <宿主机目标文件>` docker cp 命令使用方法
`docker cp nginx:/etc/nginx/nginx.conf ~/server/nginx/conf` 必须保证nginx容器正在运行
##### 3.3 创建html文件
在宿主机~/server/nginx/html目录下创建index.html文件，用于测试部署是否成功
```

<!DOCTYPE html>
<html>
	<head>
	</head>
	<body>
		<h1>success</h1>
	</body>
</html>
```
##### 3.4 映射目录运行nginx容器
目前已有一个监听80端口的docker容器在运行，为了防止后面端口冲突，先停止并删除这个容器
`docker stop nginx`
`docker rm nginx`
运行nginx容器同时映射对应目录与文件
```
docker run -d -p 80:80 --name nginx -v ~/server/nginx/html:/usr/share/nginx/html -v ~/server/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v ~/server/nginx/logs:/var/log/nginx  nginx:1.17.8
```
访问容器宿主机ip可看到success

参考视频: 
[【动力节点】2020最新Nginx详细教程(nginx快速上手)](https://www.bilibili.com/video/BV1zE411N7m9?p=1) @CXK篮r球
[Docker安装和配置nginx服务](https://www.bilibili.com/video/BV1SE411x7hw?t=483&p=1) @码宗宗主田不平
