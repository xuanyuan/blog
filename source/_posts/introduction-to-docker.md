---
title: Docker入门
date: 2018-12-28 11:32:45
tags: Docker
---

### Docker常用命令

```shell
#搜索镜像
$ sudo search ubuntu

#下载镜像
$ sudo docker pull ubuntu

#IMAGE_ID
$ sudo docker images

#CONTAINER_ID
$ sudo docker ps

#查看该容器的详细信息。
$ sudo docker inspect $CONTAINER_ID  

#简单交互
$ sudo docker run ubuntu:latest echo "hello world"

#终止容器
$ sudo docker stop $CONTAINER_ID
#启动刚刚关闭的容器
$ sudo docker start $CONTAINER_ID
```

### 进入容器执行命令行操作

```shell
#创建一个守护态的Docker容器
$ sudo docker run -itd ubuntu:14.04 /bin/bash  
#然后我们使用docker ps查看到该容器信息

#推荐第二种
#第一种
#docker attach命令不太适合于生产环境，平时自己开发应用时可以使用该命令。
$ sudo docker attach $CONTAINER_ID  

#第二种
使用"docker attach"命令进入container（容器），每次从container中退出到前台时，container也跟着退出了。要想退出container时，
让container仍然在后台运行着，可以使用"docker exec -it"命令。每次使用这个命令进入container，当退出container后，container仍然在后台运行，命令使用方法如下：
$ sudo docker ps  
$ sudo docker exec -it $CONTAINER_ID /bin/bash

```
### 保存对容器的修改推送镜像

```shell
docker commit -a "作者" -m "提交信息" $IMAGES_ID nodejs:v1
docker tag $IMAGES_ID 192.168.1.1/front-end/nodejs:v1
docker push 192.168.1.1/front-end/nodejs:v1
```
### 私服推送登录Harbor私有仓库

登录私有仓库进行验证
```shell
#在/etc/docker新建daemon.json文件:

mkdir -p /etc/docker #新建docker目录
vim /etc/docker/daemon.json #新建daemon.json文件
#编辑内容如下:
#{"insecure-registries":["192.168.1.1"]}

# 重启docker
systemctl restart docker
# 重新登录
docker login 192.168.1.1
```

### 附录

#### 修改源

```shell
cp /etc/apt/sources.list /etc/apt/sources.list.bak
vim /etc/apt/sources.list    
```
2. 插入下列源

```visual basic
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

3. 更新

```shell
apt update
```

#### Docker安装

1. 移除旧的版本

```shell
sudo apt-get remove docker docker-engine docker.io
```

2. 一键安装docker

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
