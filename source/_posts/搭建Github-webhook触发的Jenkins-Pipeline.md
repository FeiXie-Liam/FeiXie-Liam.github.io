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

`docker run -d --name myjenkins -p 9999:8080 -p 50000:50000 -v /var/jenkins_home jenkins`

`-d`参数表示容器可在后台运行
`--name`指定容器名称	
`-p [port]:[containerPort]`表示将容器内部的端口(containerPort)映射到外部端口(port)
`jenkins/jenkins`表示docker image的名称

完成之后便可以通过`docker ps`命令查看到创建的好的Jenkins容器.

![image](http://images2.imagebam.com/8b/25/6d/87dfea1095234874.png)

## 初始化Jenkins客户端

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

## 配置Github webhook

---

webhook可以在Github仓库有新的提交时,自动发送消息到Jenkins,触发Jenkins流水线自动运行.

### 生成github token

进入github --> setting --> Developer settings --> Personal Access Token --> Generate new token

![github token](https://upload-images.jianshu.io/upload_images/2518611-6c844d8a6bb58800.png?imageMogr2/auto-orient/)

![token generate success](https://upload-images.jianshu.io/upload_images/436630-943711ff2a74919d.png?imageMogr2/auto-orient/)

### Github webhooks设置

由于是在本地使用docker启动的Jenkins容器,因此需要使用[ngrok](https://ngrok.com/download)将Jenkins地址暴露给外网. 

下载完ngrok后, 在ngrok的目录下执行`ngrok http 8081`, 其中`8081`指本地Jenkins服务端口. 然后可以看到以下界面

![ngrok](http://images2.imagebam.com/70/2a/f5/e31f6c1110649784.jpg)

将ngrok暴露出的域名复制下来, 然后进入GitHub上指定的项目 --> setting --> WebHooks --> add webhook

![webhook config](http://images2.imagebam.com/2c/d8/e8/8c8cdd1110652714.jpg)

填写Payload URL为ngrok生成的域名加上/github-webhook/, 并将上一步生成的github token填写到secret中. 点击保存.

## 配置Jenkins Github Plugin

---

系统管理 --> 系统设置 --> GitHub --> Add GitHub Sever

![jenkins github config](https://upload-images.jianshu.io/upload_images/3087126-c3a4356bb2262e51.png)

`API URL` 输入 `https://api.github.com`，Credentials点击Add添加，选择Secret Text，如下图

![add secret](https://upload-images.jianshu.io/upload_images/2518611-547c6e295e263296.png)

设置完成后, 点击`TestConnection`, 提示`Credentials verified for user UUserName, rate limit: xxx`, 则表明有效

## 创建流水线

---

在Jenkins管理主页面点击新建任务选项, 选择新建pipeline

![create pipeline](http://images2.imagebam.com/cc/38/02/eae83a1110670344.jpg)

选择触发器`GitHub hook trigger for GITScm polling`，这样每次push代码都会触发Jenkins自动构建.

![github repo config](https://upload-images.jianshu.io/upload_images/3087126-ff7a204110da8f26.png)

选择`pipeline script from scm`, scm选择`git`然后在`Repository URL`中填入项目地址.

![repo address](http://images2.imagebam.com/c1/96/07/22df9d1110680084.jpg)

最后点击保存.

## 添加Jenkinsfile

---

最后需要在本地代码库添加Jenkinsfile指定流水线的运行步骤. 默认需要将Jenkinsfile放置在代码根目录下. 文件内容如下

```groovy
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh './gradlew test'
            }
        }
        stage('Build') {
            steps {
                sh './gradlew clean build'
            }
        }
    }
}
```

然后将代码push到github, 便能够看到Jenkins自动运行流水线.