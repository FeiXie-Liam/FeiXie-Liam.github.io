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

### 初始化Jenkins客户端

---

根据docker创建的Jenkins容器暴露出的端口`8081`,可以通过`http://localhost:8081`访问到Jenkins客户端, 客户端首次登陆需要解锁Jenkins.

![Jenkins_unlock](http://images2.imagebam.com/a9/33/0b/b73a9a1108011354.jpg)

解锁步骤如下:

1. 执行`docker exec -it {jenkins_container_name} bash`进入容器
2. 然后执行`cat /var/jenkins_home/secrets/initialAdminPassword`
3. 复制终端打印出的秘钥, 并粘贴到Jenkins客户端初始页面,点击继续

然后进入下一页面:

![jenkins_plugins](http://images2.imagebam.com/bc/a3/a0/df06f01108014974.jpg)

点击Install suggested plugins, 然后等待所有插件安装完成.插件完成后, 将转到创建管理员的页面:

![create_admin](http://images2.imagebam.com/91/70/1a/bdc1db1108034754.jpg)

填写完相应信息之后, 点击继续, 然后进入配置示例页面,直接点击继续, 完成Jenkins初始化配置工作.

![jenkins_ready](http://images2.imagebam.com/02/0e/55/b7a8ad1108037054.jpg)

