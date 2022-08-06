---
title: ubuntu-16.04ROS-kinetic下VLP-16激光雷达测试详细教程
date: 2022-06-06 15:46:00
tags: 激光雷达
---
# 1. 测试环境介绍

激光雷达型号:VLP-16-A
操作系统:ubuntu16.04
ROS版本:kinetic
<!-- more -->

# 2. 连接激光雷达

## 2.1 启动雷达

给激光雷达上电，并通过网线把雷达与电脑连接起来
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201201105355815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)

## 2.2 配置电脑ip

1. 编辑以太网有线连接，如果没有则创建(点击add-->选择Ethernet-->create)
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201129210721965.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201129211021987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)![在这里插入图片描述](https://img-blog.csdnimg.cn/20201129213157667.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)
2. 编辑ipv4，方式设置为手动，ip地址、掩码以及网关设置成下图(ip地址的100可以设置成其他的，只要不与激光雷达的201相同即可)
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201129211315451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)
3. 访问192.168.1.201，查看激光雷达配置页面，如果查看到下图信息表示雷达连接成功
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020112921385140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)

# 3. 安装ROS依赖以及驱动

## 3.1 安装ROS依赖

`sudo apt install ros-kinetic-velodyne`
有的博主提到还需要安装下方的依赖，我试了试不装也可以正常运行，还是放到这里吧
`sudo apt-get install libpcap-dev` 

## 3.2 安装驱动

```
mkdir -p ~/catkin_velodyne/src && cd ~/catkin_velodyne/src
git clone https://github.com/ros-drivers/velodyne.git
cd ~/catkin_velodyne/ && catkin_make
source ~/catkin_velodyne/devel/setup.bash
```

# 4. 运行驱动程序查看激光点云

## 4.1 运行ROS程序

`roslaunch velodyne_pointcloud VLP16_points.launch`

## 4.2 rostopic 终端打印点云数据

`rostopic echo /velodyne_points`

## 4.3 rviz查看激光点云

`rosrun rviz rviz -f velodyne`
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020112922091335.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)

# 5. 常见问题以及解决

>首先需要注意的是launch之前一定要source setup.bash

## 5.1 roslaunch velodyne_pointcloud VLP16_points.launch报错

 [ WARN] [1606659546.548501176]: Velodyne poll() timeout 
 [ERROR] [1606659546.548607516]: DriverNodelet::devicePoll - Failed to poll device.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201129215722322.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)
报这个错误是因为ROS读取不到激光雷达的数据，一般是因为VLP16_points.launch文件中设置的端口与激光雷达的数据端口不一致。
**解决方式**

1. 到192.168.1.201查看数据端口，如下图查看到端口号为3201
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201129222401806.png#pic_center)
2. 修改 ~/catkin_velodyne/src/velodyne/velodyne_pointcloud/launch/VLP16_points.launch 文件，使端口号与激光雷达的实际数据端口保持一致
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201129223347279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)
3. 重新运行 roslaunch velodyne_pointcloud VLP16_points.launch即可

## 5.2 rviz窗口不显示点云数据

点击add 添加/velodyne_points话题即可正常显示
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201129223937149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDM4NzMzOQ==,size_16,color_FFFFFF,t_70#pic_center)
	
参考链接：

[1] [Getting Started with the Velodyne VLP16](http://wiki.ros.org/velodyne/Tutorials/Getting%20Started%20with%20the%20Velodyne%20VLP16)
[2] [Velodyne VLP16激光雷达的使用(非常详细）](https://blog.csdn.net/zbr794866300/article/details/99305864)
[3] [VLP-16第一课: Velodyne的工作原理和驱动安装](https://blog.csdn.net/qq_29797957/article/details/102807553)