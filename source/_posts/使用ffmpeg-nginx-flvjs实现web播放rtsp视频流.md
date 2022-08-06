---
title: 使用ffmpeg+nginx+flvjs实现web播放rtsp视频流
date: 2022-05-27 15:36:40
tags: 视频直播
---


# 1. 简介

大部分网络摄像机，比如海康威视都支持rtsp协议视频流，但是web一般是无法直接播放rtsp协议视频流的，需要在服务器上把rtsp视频流转换成web其他协议。本篇博客介绍利用ffmpeg、nginx、flvjs实现web浏览rtsp视频流。主要参考了文章[【入门】无插件web直播解决方案，ffmpeg+nginx-http-flv-module+flv.js](https://blog.csdn.net/string_kai/article/details/100598268)、[Nginx+FFmpeg 海康、大华NVR实现rtsp转flv实时预览+录像回放](https://www.jianshu.com/p/547dca89dd43)
所用到的服务器操作系统为ubuntu20.04。
<!-- more -->
# 2. 安装ffmpeg

`sudo apt update && sudo apt install ffmpeg`

# 3. 安装nginx

nginx需要安装nginx-http-flv-module以支持flv格式视频流，需要下载该模块，并从源码重新编译nginx，在编译nginx前还需要下载一些必要的依赖，以下参考了[nginx源码编译教程](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/?_ga=2.126455236.898256039.1622205430-1600944689.1621243185#sources)

## 3.1 安装nginx依赖

### 3.1.1 pcre

教程中给的pcre下载链接无法使用，我更换成了以下链接~~https://ftp.pcre.org/pub/pcre/pcre-8.44.tar.gz~~ pcre官方不再维护，这是镜像地址https://sourceforge.net/projects/pcre/files/pcre/8.44/

```
wget https://ftp.pcre.org/pub/pcre/pcre-8.44.tar.gz
tar -zxf pcre-8.44.tar.gz
cd pcre-8.44
./configure
make
sudo make install
```

### 3.1.2  zlib

```
wget http://zlib.net/zlib-1.2.11.tar.gz
tar -zxf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure
make
sudo make install
```

### 3.1.3 openssl

再次按照该文档编译时，发现编译nginx执行configure命令无法找到编译的openssl(**经测试发现--prefix=/usr改为--prefix=/usr/local**即可)，改为用`sudo apt-get install libssl-dev` 直接安装，以下为原来的编译教程


教程中./Configure darwin64-x86_64-cc --prefix=/usr配置后有问题，我参考[stackoverflow解决方案](https://stackoverflow.com/questions/39024703/openssl-make-failure-error-x86-64-no-such-file-or-directory)，做了更改

```
wget http://www.openssl.org/source/openssl-1.1.1g.tar.gz
tar -zxf openssl-1.1.1g.tar.gz
cd openssl-1.1.1g
./Configure LIST | grep -i linux
./Configure linux-x86_64 --prefix=/usr/local
make
sudo make install
```

## 3.2 编译nginx

### 3.2.1 下载nginx

```
wget https://nginx.org/download/nginx-1.18.0.tar.gz
tar zxf nginx-1.18.0.tar.gz
```

### 3.2.2 下载nginx-http-flv-module

`git clone https://github.com/winshining/nginx-http-flv-module.git`

### 3.2.3 编译

下方/path/to应该修改成你的nginx-http-flv-module文件夹的位置，比如我在home目录**hfy**下执行的git clone命令，nginx-http-flv-module文件夹位于我的家目录中，配置命令应该写成`./configure --add-module=/home/hfy/nginx-http-flv-module`

```
cd nginx-1.18.0
./configure --add-module=/path/to/nginx-http-flv-module
make
sudo make install
```

# 4. 配置nginx

`sudo vi /usr/local/nginx/conf/nginx.conf` 打开nginx配置文件，删除原来的配置内容，替换为[nginx-http-flv-module github](https://github.com/winshining/nginx-http-flv-module) readme最后给出的example configuration

## 4.1 重点配置介绍

```
...
 
http {
    include       mime.types;
    default_type  application/octet-stream;
 
    keepalive_timeout  65;
 
    server {
        listen       80; #http-flv的拉流端口
 
        ...
        
        # http-flv的相关配置
        location /live {
            flv_live on; #打开HTTP播放FLV直播流功能
            chunked_transfer_encoding on; #支持'Transfer-Encoding: chunked'方式回复
 
            add_header 'Access-Control-Allow-Origin' '*'; #添加额外的HTTP头
            add_header 'Access-Control-Allow-Credentials' 'true'; #添加额外的HTTP头
        }
 
        ...
    }
}
 
rtmp_auto_push on;
rtmp_auto_push_reconnect 1s;
rtmp_socket_dir /tmp;
 
rtmp {
    out_queue           4096;
    out_cork            8;
    max_streams         128;
    timeout             15s;
    drop_idle_publisher 15s;
 
    log_interval 5s; #log模块在access.log中记录日志的间隔时间，对调试非常有用
    log_size     1m; #log模块用来记录日志的缓冲区大小
 
    server {
        listen 1935;
        server_name www.test.*; #用于虚拟主机名后缀通配
 
        #ffmpeg推流的application 
        application myapp {
            live on;
            gop_cache on; #打开GOP缓存，减少首屏等待时间 on时第一帧加载快，off时第一帧加载慢 
            # @StringKai 在博客https://blog.csdn.net/string_kai/article/details/100598268提到on时延高，off时延低，不过我在测试时并没有感觉出时延的差别
        }
 
       ...
    }
 
   ...
}
```

## 4.2 完整配置(方便复制粘贴)

```
worker_processes  1; #should be 1 for Windows, for it doesn't support Unix domain socket
#worker_processes  auto; #from versions 1.3.8 and 1.2.5

#worker_cpu_affinity  0001 0010 0100 1000; #only available on FreeBSD and Linux
#worker_cpu_affinity  auto; #from version 1.9.10

error_log logs/error.log error;

#if the module is compiled as a dynamic module and features relevant
#to RTMP are needed, the command below MUST be specified and MUST be
#located before events directive, otherwise the module won't be loaded
#or will be loaded unsuccessfully when NGINX is started

#load_module modules/ngx_http_flv_live_module.so;

events {
    worker_connections  4096;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    keepalive_timeout  65;

    server {
        listen       80;

        location / {
            root   /var/www;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        location /live {
            flv_live on; #open flv live streaming (subscribe)
            chunked_transfer_encoding  on; #open 'Transfer-Encoding: chunked' response

            add_header 'Access-Control-Allow-Origin' '*'; #add additional HTTP header
            add_header 'Access-Control-Allow-Credentials' 'true'; #add additional HTTP header
        }

        location /hls {
            types {
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }

            root /tmp;
            add_header 'Cache-Control' 'no-cache';
        }

        location /dash {
            root /tmp;
            add_header 'Cache-Control' 'no-cache';
        }

        location /stat {
            #configuration of streaming & recording statistics

            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }

        location /stat.xsl {
            root /var/www/rtmp; #specify in where stat.xsl located
        }

        #if JSON style stat needed, no need to specify
        #stat.xsl but a new directive rtmp_stat_format

        #location /stat {
        #    rtmp_stat all;
        #    rtmp_stat_format json;
        #}

        location /control {
            rtmp_control all; #configuration of control module of rtmp
        }
    }
}

rtmp_auto_push on;
rtmp_auto_push_reconnect 1s;
rtmp_socket_dir /tmp;

rtmp {
    out_queue           4096;
    out_cork            8;
    max_streams         128;
    timeout             15s;
    drop_idle_publisher 15s;

    log_interval 5s; #interval used by log module to log in access.log, it is very useful for debug
    log_size     1m; #buffer size used by log module to log in access.log

    server {
        listen 1935;
        server_name www.test.*; #for suffix wildcard matching of virtual host name

        application myapp {
            live on;
            gop_cache on; #open GOP cache for reducing the wating time for the first picture of video
        }

        application hls {
            live on;
            hls on;
            hls_path /tmp/hls;
        }

        application dash {
            live on;
            dash on;
            dash_path /tmp/dash;
        }
    }

    server {
        listen 1935;
        server_name *.test.com; #for prefix wildcard matching of virtual host name

        application myapp {
            live on;
            gop_cache on; #open GOP cache for reducing the wating time for the first picture of video
        }
    }

    server {
        listen 1935;
        server_name www.test.com; #for completely matching of virtual host name

        application myapp {
            live on;
            gop_cache on; #open GOP cache for reducing the wating time for the first picture of video
        }
    }
}

```

# 5. ffmpeg推流

## 5.1 海康威视网络摄像机推流命令

ffmpeg推流命令主要参考了文章：[Nginx+FFmpeg 海康、大华NVR实现rtsp转flv实时预览+录像回放](https://www.jianshu.com/p/547dca89dd43)，详细介绍了如何使用ffmpeg对海康威视网络摄像机推流

```
ffmpeg -rtsp_transport tcp -i  rtsp://user:password@ip:port/Streaming/channels/101  -c copy -f flv rtmp://127.0.0.1:1935/myapp/mystream
```

>参数解析
>-rtsp_transport tcp： 固定写法
>user：用户名
>password：密码
>ip：摄像头或NVR的IP地址
>port：摄像头或NVR的RTSP端口，默认是554，具体的RTSP取流规则可以百度
>-c copy： 输出直接复制，不转换格式
>-f flv：转成flv
>rtmp://127.0.0.1:1935/myapp/mystream：根据Nginx配置文件生成，端口号1935与nginx配置中的listen 1935对应；myapp对应配置文件中的application myapp；mystream名字不固定，会在后续用flvjs取流时用到

附海康威视网络摄像机rtsp取流地址

```
【新版本】
URL规定：
rtsp://username:password@[address]:[port]/Streaming/Channels/[id](?parm1=value1&parm2-=value2…)
注：VLC可以支持解析URL里的用户名密码，实际发给设备的RTSP请求不支持带用户名密码。
详细描述：
举例说明：
通道01主码流：
rtsp://admin:abc12345@172.6.22.234:554/Streaming/Channels/101?transportmode=unicast
通道01子码流：
rtsp://admin:abc12345@172.6.22.234:554/Streaming/Channels/102?transportmode=unicast(单播)
rtsp://admin:abc12345@172.6.22.106:554/Streaming/Channels/102?transportmode=multicast (多播)
rtsp://admin:abc12345@172.6.22.106:554/Streaming/Channels/102 (?后面可省略，默认单播)
通道01第3码流：
rtsp://admin:abc12345@172.6.22.234:554/Streaming/Channels/103?transportmode=unicast
零通道主码流（零通道无子码流）：

rtsp://admin:12345@172.6.22.106:554/Streaming/Channels/001

注：新版本URL，通道号全部按顺序从1开始。
```

## 5.2 rtsp视频流公共测试地址

如果手头没有现成的网络摄像头供测试，可以使用公共的rtsp视频测试地址
`rtsp://wowzaec2demo.streamlock.net/vod/mp4:BigBuckBunny_115k.mov`
相应的推流命令如下

```
ffmpeg -rtsp_transport tcp -i rtsp://wowzaec2demo.streamlock.net/vod/mp4:BigBuckBunny_115k.mov  -c copy -f flv rtmp://127.0.0.1:1935/myapp/mystream
```

# 6. flvjs播放

flvjs是哔哩哔哩开源的web播放器，使用方法可以参考[官方demo](https://github.com/bilibili/flv.js/blob/master/demo/index.html)。但是官方给出的demo代码比较多，如果只是想简单实现的话可以参考下方的完整代码，注意flvjs是通过[bootcdn](https://www.bootcdn.cn/flv.js/)引入的。

## 6.1 关键代码说明

```javascript
var flvPlayer = flvjs.createPlayer({
                type: 'flv',
                enableWorker: true,     //浏览器端开启flv.js的worker,多进程运行flv.js
                isLive: true,           //直播模式
                hasAudio: true,        //开启音频             
                hasVideo: true,
                stashInitialSize: 128,  
                enableStashBuffer: false, //播放flv时，设置是否启用播放缓存，只在直播起作用。
                url: ''
            })
```

>url格式 http://example.com:80/live?port=1935&app=myapp&stream=mystream
>example.com:**80**中的80端口与nginx配置中listen 80对应，后面的三个get参数**port=1935&app=myapp&stream=mystream**是固定格式，**1935**与nginx配置**listen 1935**对应，**myapp**与nginx配置中**application myapp**对应，**mystream**与ffmpeg推流命令最后的rtmp://127.0.0.1:1935/myapp/**mystream** 对应

## 6.2 完整代码(方便复制粘贴)

```html
<!DOCTYPE html>
<html>

<head>
    <meta content="text/html; charset=utf-8" http-equiv="Content-Type">
    <title>flv.js demo</title>
    <style>
        .mainContainer {
            display: block;
            width: 1024px;
            margin-left: auto;
            margin-right: auto;
        }

        .urlInput {
            display: block;
            width: 100%;
            margin-left: auto;
            margin-right: auto;
            margin-top: 8px;
            margin-bottom: 8px;
        }

        .centeredVideo {
            display: block;
            width: 100%;
            height: 576px;
            margin-left: auto;
            margin-right: auto;
            margin-bottom: auto;
        }

        .controls {
            display: block;
            width: 100%;
            text-align: left;
            margin-left: auto;
            margin-right: auto;
        }
    </style>
</head>

<body>
    <div class="mainContainer">
        <video id="videoElement" class="centeredVideo" controls width="1024" height="576">Your browser is too old which doesn't support HTML5 video.</video>
    </div>
    <br>
    <div class="controls">
        <button onclick="flv_start()">开始</button>
        <button onclick="flv_pause()">暂停</button>
        <button onclick="flv_destroy()">停止</button>
        <input style="width:100px" type="text" name="seekpoint" />
        <button onclick="flv_seekto()">跳转</button>
    </div>
    <script src="https://cdn.bootcdn.net/ajax/libs/flv.js/1.5.0/flv.js"></script>
    <script>
        var vElement = document.getElementById('videoElement');
        if (flvjs.isSupported()) {
            var flvPlayer = flvjs.createPlayer({
                type: 'flv',
                enableWorker: true,     //浏览器端开启flv.js的worker,多进程运行flv.js
                isLive: true,           //直播模式
                hasAudio: true,        //关闭音频             
                hasVideo: true,
                stashInitialSize: 128,  
                enableStashBuffer: false, //播放flv时，设置是否启用播放缓存，只在直播起作用。
                url: 'http://example.com/live?port=1935&app=myapp&stream=mystream'
            });
            flvPlayer.attachMediaElement(vElement)
            flvPlayer.load() //加载
        }
        
        setInterval(function () {
            vElement.playbackRate = 1
            console.log("时延校正判断");
            if (!vElement.buffered.length) {
                return
            }
            var end = vElement.buffered.end(0)
            var diff = end - vElement.currentTime
            console.log(diff)
            if (5 <= diff && diff <=60) {
                console.log("二倍速")
                vElement.playbackRate = 2
            }
            if (diff > 60) {
                console.log("跳帧")
                vElement.currentTime = end
            }
        }, 2500)

        function flv_start() {
            flvPlayer.play()
        }

        function flv_pause() {
            flvPlayer.pause()
        }

        function flv_destroy() {
            flvPlayer.pause()
            flvPlayer.unload()
            flvPlayer.detachMediaElement()
            flvPlayer.destroy()
            flvPlayer = null
        }

        function flv_seekto() {
            player.currentTime = parseFloat(document.getElementsByName('seekpoint')[0].value)
        }
        
    </script>
</body>
</html>
```

# 7. 其他问题

至此已经完成了整个实现过程，但是还有一些问题需要注意。

## 7.1 累积时延问题

由于网络波动或者网页切换到后台等原因，flvjs播放会有累积时延，这在摄像头监控画面中是无法忍受的，主要参考了[github issues](https://github.com/bilibili/flv.js/issues/654)以及[【入门】无插件web直播解决方案，ffmpeg+nginx-http-flv-module+flv.js](https://blog.csdn.net/string_kai/article/details/100598268)给出的解决方案:video对象可以获取currentTime以及endTime，设置一个定时器比较一下二者时间差，时间差小于60s时倍速播放，大于60s时直接跳帧，因为视频监控的用户更关心最近的画面，代码已经在6.2中给出，如下：

```javascript
        setInterval(function () {
            vElement.playbackRate = 1
            console.log("时延校正判断");
            if (!vElement.buffered.length) {
                return
            }
            var end = vElement.buffered.end(0)
            var diff = end - vElement.currentTime
            console.log(diff)
            if (5 <= diff && diff <=60) {
                console.log("二倍速")
                vElement.playbackRate = 2
            }
            if (diff > 60) {
                console.log("跳帧")
                vElement.currentTime = end
            }
        }, 2500)
```

## 7.2 自动播放问题

有时候可能一个web页面需要展示好几个监控视频画面，让用户依次点击开始播放不太方便，需要在video标签中加入autoplay 属性实现自动播放，但是一些浏览器比如chrome禁止音频内容的自动播放，可以在video标签中加入muted属性，如下：
`<video id="videoElement" class="centeredVideo" muted autoplay width="1024" height="576">Your browser is too old which doesn't support HTML5 video.</video>`

## 7.3 flvjs播放器要主动销毁

该问题是我在使用Vue进行前端开发时遇到的，在离开某个Vue页面后并没有主动销毁flvjs的播放器，后台仍然在接受数据，导致再次回到该页面时无法重复创建flvjs播放器，加载不出监控画面。解决方法是在离开页面的回调函数中写入销毁的函数

```javascript
        function flv_destroy() {
            flvPlayer.pause()
            flvPlayer.unload()
            flvPlayer.detachMediaElement()
            flvPlayer.destroy()
            flvPlayer = null
        }
```

## 7.4 同时在后台启动多个推流

当需要同时在后台启动多个推流命令时，使用screen可能比较麻烦。可以创建一个start_live.sh的脚本，内容如下：

```bash
# start_live.sh
nohup ffmpeg -rtsp_transport tcp -i rtsp://a.com/vod/mp4:BigBuckBunny_115k.mov  -c copy -f flv rtmp://127.0.0.1:1935/myapp/mystream1 &
nohup ffmpeg -rtsp_transport tcp -i rtsp://b.com/vod/mp4:BigBuckBunny_115k.mov  -c copy -f flv rtmp://127.0.0.1:1935/myapp/mystream2 &
nohup ffmpeg -rtsp_transport tcp -i rtsp://c.com/vod/mp4:BigBuckBunny_115k.mov  -c copy -f flv rtmp://127.0.0.1:1935/myapp/mystream3 &
```

为了防止在nohup.out中写入巨量的日志信息，可以在ffmpeg推流命令中加入`-loglevel quiet`参数。

