---
title: miniconda安装与使用
date: 2020-10-19 15:46:00
tags: conda
---

# 1. 下载
到清华镜像源下载miniconda安装程序 https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/
挑选合适的下载，例如：
`wget -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py37_4.8.3-Linux-x86_64.sh`
<!-- more -->
# 2. 安装
1. `bash Miniconda3-py37_4.8.3-Linux-x86_64.sh`
2. 遇到Do you accept the license terms? [yes|no]
回车
q键退出阅读license
yes
3. Miniconda3 will now be installed into this location:
/home/hfy/miniconda3
回车默认
4. 可以选择不初始化，使用conda时需要先`source ~/miniconda3/bin/activate`
Do you wish the installer to initialize Miniconda3
by running conda init? [yes|no]
5. 启动
`source ~/miniconda3/bin/activate`
6. 添加镜像源
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda 
conda config --set show_channel_urls true
```
# 3. 常用命令
1. 查看版本
`conda --version`
2. 创建环境
`conda create --name your_env_name <python=版本> <python包>`
3. 删除环境
`conda remove --name your_env_name --all`
4. 查看创建环境
`conda env list`
5. 进入某个环境
`conda activate env_name`
6. 退出环境
`conda deactivate`

参考文章：
[Ubuntu miniconda3安装与环境配置](https://www.jianshu.com/p/914edc1de634)
[Conda常用命令整理](https://blog.csdn.net/menc15/article/details/71477949/)

