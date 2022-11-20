---
title: docker快速上手
date: 2020-07-11 15:46:00
reward: true
tags: docker
---

# 一、在ubuntu系统安装
## 1. 脚本一键安装
`curl -sSL https://get.daocloud.io/docker | sh`
## 2. 使用清华镜像源安装
[清华镜像源安装说明](https://mirror.tuna.tsinghua.edu.cn/help/docker-ce/)
<!-- more -->
# 二、DockerHub镜像配置
 在 /etc/docker/daemon.json(没有则新建)文件中写入
```
{
    "registry-mirrors":["https://docker.mirrors.ustc.edu.cn/"]
}
```
重启服务
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

上述网址为中科大镜像源，也可加入网易镜像源`https://hub-mirror.c.163.com/`
使用`sudo docker info `查看镜像源是否配置成功
# 三、docker用户组
安装完成后，如果不加sudo直接使用docker命令会报错
```
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: 
Get http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/json: dial unix /var/run/docker.sock: connect: permission denied
```
把当前用户添加到docker用户组即可
```
sudo usermod -aG docker <用户名>
sudo service docker restart 
newgrp - docker

```
# 四、docker构成部分及关系
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200711152912344.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70)
docker主要涉及5个部分，仓库、镜像、Dockerfile、tar文件以及容器。
系统或者软件需要以容器的形式运行，而镜像则是系统或者软件的模板，可以用镜像生成具体的容器，可以类比面向对象编程中的类与对象的关系，一个镜像可以生成多个容器。
仓库中存放了大量构建好的各种系统软件的镜像文件，方便用户下载。
除了从仓库直接拉取镜像，还可以利用Dockerfile文件指导指导镜像的构建方式。另外，docker还可以使用save或者load指令把镜像保存为tar文件或者从tar文件加载生成一个镜像，十分方便移植。
# 五、docker常用命令
`docker pull <系统或软件镜像名称:版本号>`  从仓库拉取目标镜像，省略版本号表示最新版本

`docker images` 查看本地下载的镜像

`docker rmi <系统或软件镜像名称:版本号>/<镜像ID>` 删除镜像

---
`docker stop <容器名称>/<容器ID>` 停止正在运行的容器

`docker start <容器名称>/<容器ID>` 启动停止的容器

`docker restart <容器名称>/<容器ID>`  重启容器

`docker run <镜像名称:版本号>/<镜像ID>` 运行镜像(成为了容器) 

`docker run --name <指定的容器名称> <镜像名称:版本号>/<镜像ID>` 运行镜像并指定名称

`docker run -d -p <外部映射端口:内部端口> <镜像名称:版本号>/<镜像ID>` -d代表后台运行 -p指定网络端口的映射方式 比如 `-p 8080:80` 可以通过docker宿主机的8080端口访问到容器中的80端口

`docker run -d -v <外部映射文件夹:内部文件夹> <镜像名称:版本号>/<镜像ID> ` -v 指定内外文件夹的映射，相当于把内部文件放到外部，方便直接编辑

`docker run -it <镜像名称:版本号>/<镜像ID> bash` run -it 表示运行镜像并打开可以交互的终端，bash表示指定的终端为bash，在交互终端输入exit即可退出

`docker run -itd <镜像名称:版本号>/<镜像ID> bash` 打开交互式终端，并在后台运行

`docker  attach <容器名称>/<容器ID>` 进入后台运行的容器，如果能用exit命令退出，容器会停止

`docker exec -it <容器名称>/<容器ID> bash` 以交互终端的方式进入后台运行的容器，exit命令退出交互终端，容器也不会停止

`docker ps` 查看正在运行的容器

`docker rm -f <容器名称>/<容器ID>` 删除指定容器

`docker container prune` 删除所有未运行的容器

---
`docker commit <容器名称>/<容器ID> <指定的镜像名称>` 把容器保存为镜像

---
`docker build -t <指定镜像名称>  <Dockerfile所在的文件夹>`  

---
`docker save <镜像名称:版本号>/<镜像ID>  > <xxx.tar>`   把镜像文件保存为tar文件注意有个大于号
`docker load < <xxx.tar>` 把tar文件保存为镜像文件，同样注意有个小于号

---
参考视频: [【docker入门】10分钟，快速学会docker](https://www.bilibili.com/video/BV1R4411F7t9) @free-coder
