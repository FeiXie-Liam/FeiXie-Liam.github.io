---
title: 搭建Github webhook触发的Jenkins Pipeline
date: 2019-01-04 19:24:15
tags:
 - Jenkins
 - Pipeline
categories:
 - Jenkins

---

本文通过docker启动Jenkins流水线, 使用ngrok将Jenkins接口暴露给github, 然后配置项目的github webhook关联Jenkins流水线项目, 实现代码变更触发流水线运行.

<!--more-->

## 创建Jenkins容器

---

首先下载jenkins的docker镜像: `docker pull jenkins/jenkins`

**注意不要使用`docker pull jenkins`下载官方镜像, 因为官方镜像版本没有更新, 无法使用pipeline等主要Jenkins插件.**

镜像下载完成后, 使用命令创建Jenkins容器:

`docker run -d --name jenkins_test -p 8081:8080 -p 50001:50000 jenkins/jenkins`

`-d`参数表示容器可在后台运行
`--name`指定容器名称
`-p [port]:[containerPort]`表示将容器内部的端口(containerPort)映射到外部端口(port)
`jenkins/jenkins`表示docker image的名称

完成之后便可以通过`docker ps`命令查看到创建的好的Jenkins容器.

![image](http://images2.imagebam.com/8b/25/6d/87dfea1095234874.png)

