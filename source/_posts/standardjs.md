---
title: standardJS
date: 2018-09-06 21:18:06
tags:
 - standardJS
categories:
 - 前端

---

## 安装

---

在dev环境下安装StandardJS:

`npm install standard --save-dev`

<!--more-->

## 规则

---

StandardJS提供了一套简洁的规则对代码格式进行检查,需要注意的是StandardJS的检查规则是不可更改的, 检查时只能够按照其规范进行代码修改. StandardJS完整的规范可以在[这里](https://standardjs.com/rules-zhcn.html#javascript-standard-style)看到. 

## 使用

---

由于我们是将StandardJS安装在dev环境下的,因此我们需要对项目目录下的package.json文件进行相应修改,通过npm run {{command}}的方式执行代码检查的命令. 需要配置如下:

```json
  "scripts": {
    "standardCheck": "standard src/**/*.js",
    "standardFix": "standard --fix"
  }
```

然后通过`npm run standardCheck`运行代码检查, 查看检查结果. 

StandardJS提供了自动代码检查工具根据上述配置,可以通过`npm run standardFix`自动代码格式化,但是该方式的自动代码格式化只能解决部分问题,还有许多格式问题需要手动修改.

## 容易遇到的坑

---

由于部分第三方插件向全局暴露了变量,因此在使用类似变量(Fetch, Headers, URI …)时,会出现variable is not defined的错误,为了解决该问题,可以在package.json文件中添加相应的配置:

```json
"standard": {
	"globals": [
      "fetch",
      "Headers",
      "URL",
      ...
    ]
}
```

`standard` 支持最新的 ECMAScript 特性，ES8（ES2017），包括处于 “Stage 4” 仍在提案阶段的特性。但是最新的实验特性无法支持,会报错,为了解决这一问题,standard支持自定义JavaScript解析器,以babel-eslint为例:

`npm install babel-eslint --save-dev`添加解析器,然后在package.json中添加相应配置:

```json
"standard": {
  "parser": "babel-eslint"
}
```

