---
title: Docker学习笔记
tags: 
	- Docker
	- JAVA
	- SpringCloud
---
### Docker是什么？
Docker就是容器，有人经常拿Docker跟Wmware和VitrualBox等虚拟机做比较，原来我还是很喜欢用Wmware，自从发现有Docker这种容器技术的时候，我就深深爱上了它，Docker安装好以后，直接把Vmware删掉了
<!-- more -->
### Docker的使用场景
* 简化重复的配置，类似的工具也有vagrant
* 隔离应用
* 快速部署

云Paas和SAAS大量的用到了Docker

Docker在Centos和Ubuntu上面安装比较简单，网上百度一大堆
#### Centos
安装
```
yum install docker

```
配置镜像加速器
	针对Docker客户端版本大于1.10.0的用户
	您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器：
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://**.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
#### Ubuntu
安装
```
sudo apt install docker.io
```
配置镜像加速器
	针对Docker客户端版本大于1.10.0的用户
	您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器：
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://**.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
#### Mac 
配置镜像加速器
对于10.10.3以下的用户 推荐使用 Docker Toolbox
Mac系统的安装文件目录：http://mirrors.aliyun.com/docker-toolbox/mac/docker-toolbox/
对于10.10.3以上的用户 推荐使用 Docker for Mac
Mac系统的安装文件目录：http://mirrors.aliyun.com/docker-toolbox/mac/docker-for-mac/
如何配置镜像加速器
#### 针对安装了Docker Toolbox的用户，您可以参考以下配置步骤：

```
docker-machine create --engine-registry-mirror=https://6u2v1j6s.mirror.aliyuncs.com -d virtualbox default

```
查看机器的环境配置，并配置到本地，并通过Docker客户端访问Docker服务。
```
docker-machine env default
eval "$(docker-machine env default)"
docker info
```

#### Windows
配置镜像加速器
对于Windows 10以下的用户 推荐使用 Docker Toolbox
Windows系统的安装文件目录：http://mirrors.aliyun.com/docker-toolbox/windows/docker-toolbox/
对于Windows 10以上的用户 推荐使用 Docker for Windows
Windows系统的安装文件目录：http://mirrors.aliyun.com/docker-toolbox/windows/docker-for-windows/
如何配置镜像加速器
#### 针对安装了Docker Toolbox的用户，您可以参考以下配置步骤：

```
docker-machine create --engine-registry-mirror=https://6u2v1j6s.mirror.aliyuncs.com -d virtualbox default

```
查看机器的环境配置，并配置到本地，并通过Docker客户端访问Docker服务。
```
docker-machine env default
eval "$(docker-machine env default)"
docker info
```
针对安装了Docker for Windows的用户，您可以参考以下配置步骤：
```
{
  "registry-mirrors": ["https://6u2v1j6s.mirror.aliyuncs.com"]
}
```
编辑完成，点击 Apply 保存按钮，等待Docker重启并应用配置的镜像加速器。

Docker for Windows 和 Docker Toolbox是不兼容，如果同时安装两者的话，需要使用hyperv的参数启动。
```
docker-machine create --engine-registry-mirror=https://6u2v1j6s.mirror.aliyuncs.com -d hyperv default
```
Docker for Windows 有两种运行模式，一种运行Windows相关容器，一种运行传统的Linux容器。同一时间只能选择一种模式运行。


### Docker监控管理工具Seagull已经支持容器的资源监控
```
获取Seagull
sudo docker pull tobegit3hub/seagull
启动Seagull
sudo docker run -d -p 10086:10086 -v /var/run/docker.sock:/var/run/docker.sock tobegit3hub/seagull

```
### Docker安装Oracle数据库
获取Oracle
```
sudo docker pull registry.cn-hangzhou.aliyuncs.com/qida/oracle-xe-11g
```
启动服务
```
sudo docker run -d -p 49160:22 -p 49161:1521 registry.cn-hangzhou.aliyuncs.com/qida/oracle-xe-11g
```
SSH登陆
```
ssh root@localhost -p 49160 password: admin
hostname: localhost port: 49161 sid: xe username: system password: oracle Password for SYS & SYSTEM

```