---
title: angular入门
date: 2018-11-29 20:05:28
tags:
 - angular
 - 前端
categories:
 - 前端

---

<!--more-->

## 初始化Angular项目

---

### 先决条件

- nodejs
- npm

### 安装Angular CLI

在使用Angular CLI初始化Angular项目前, 需要全局安装Angular CLI: 

>  npm install -g @angular/cli

### 创建工作区

使用Angular CLI创建angular项目工作目录

> ng new angular-app

创建好之后, 工作区包含了:

1. 一个命名为angular-app的新的工作区
2. 在/src目录下,初始化的项目架构
3. 一个end-to-end测试项目(/e2e目录)
4. 相关的配置文件

该工作区初始化完成后为Welcome app, 直接可以运行

### 启动项目

工作区初始化完毕之后, 可以使用Angular CLI命令本地启动该项目.

首先到工作区目录下, 然后运行以下命令:

> ng serve --open

使用该方式启动项目后, 会监测你的代码变动, 一旦有更新, 项目会重新构建, 前端页面也会显示最新的变动.

## 工作区目录文件解析

---

在初识化项目中, 包含了许多工作区级别的配置文件, 本节将简要介绍每个文件的作用.

| 工作区配置文件 | 目的                                                         |
| -------------- | ------------------------------------------------------------ |
| .editorconfig  | 编辑器的相应配置(缩进, 编码, 换行符等), 参考 [EditorConfig](https://editorconfig.org/). |
| .gitignore     | git仓库的忽略文件列表                                        |
| angular.json   | 项目中默认的CLI配置, 包含了构建, 启动和测试的配置选项, 参考[Angular项目配置](https://angular.io/guide/workspace-config). |
| node_modules   | npm本地包的存放位置                                          |
| package.json   | npm包依赖配置文件                                            |
| tsconfig.json  | 当前项目默认的TypeScript配置, 参考[TypeScript Configuration](https://angular.io/guide/typescript-configuration). |
| tslint.json    | 默认的tslint代码检查配置文件                                 |
| README.md      | 项目的介绍文档                                               |

在该工作区的所有项目会共享[CLI配置上下文](https://angular.io/guide/workspace-config),某个特定项目的TypeScript, TSLint配置文件会继承上级的tsconfig.*.json, tsline.json.

src目录下包含了主要的源文件(app逻辑, 数据, 和assets), 以及初识的项目配置文件

| app源/配置文件 | 目的                                                         |
| :------------- | ------------------------------------------------------------ |
| app/           | 包含了项目中的组件文件, 即项目的主要逻辑和数据定义.参考[App source folder](https://angular.io/guide/file-structure#app-src) |
| assets/        | 包含了在构建app时需要拷贝的图像或其他资源文件                |
| environments/  | 包含了为不同环境的不同构建配置选项                           |
| browserlist    | 配置各种前端工具之间的目标浏览器和node.js版本的共享.参考[Browserlist on GitHub](https://github.com/browserslist/browserslist) |
| favicon.ico    | 在浏览器书签栏的图标                                         |
| index.html     | 入口HTML页面. 当构建项目时, Angular CLI会自动添加所有js/ts和css文件到该文件下 |
| main.ts        | 项目的入口点,默认使用[JIT编译器](https://angular.io/guide/glossary#jit)编译应用程序, 并引导程序的根模块在浏览器中运行, 也可以使用`—aot`参数让项目通过[AOT编译器](https://angular.io/guide/aot-compiler) |
| polyfills.ts   | 提供用于浏览器支持的polyfill脚本                             |
| styles.scss    | 列出为项目提供样式的CSS文件,扩展名反映了您为项目配置的样式预处理器。 |
| test.ts        | 单元测试的入口点, 具有部分Angular特定的配置                  |

e2e目录下存放了e2e测试相关文件:

```
my-app/
  e2e/                  (端到端测试文件)
    src/                (app source files)
    protractor.conf.js  (test-tool config)
    tsconfig.e2e.json   (TypeScript config inherits from workspace tsconfig.json)
```

Project目录下存放了其他的apps和libs

```
  projects/           (additional apps and libs)
    my-other-app/     (a second app)
      src/
      (config files)
    my-other-app-e2e/  (corresponding test app) 
      src/
      (config files)
    my-lib/            (a generated library)
      (config files)
```

在src/文件夹中, app/文件夹包含应用程序的逻辑和数据, 组件, 模板和样式在这里. 资产/子文件夹包含图像以及您的应用所需的任何其他内容.  src/ support顶层的文件测试和运行你的应用程序. 

| app源文件                 | 目的                                                         |
| ------------------------- | ------------------------------------------------------------ |
| app/app.component.ts      | 定义应用程序根组件的逻辑，名为AppComponent。在向应用程序添加组件和服务时，与此根组件关联的视图将成为视图层次结构的根。 |
| app/app.component.html    | 定义与根AppComponent关联的HTML模板。                         |
| app/app.component.css     | 定义根AppComponent的基本CSS样式表。                          |
| app/app.component.spec.ts | 定义根AppComponent的单元测试。                               |
| app/app.module.ts         | 定义名为AppModule的根模块，它告诉Angular如何组装应用程序。最初只声明AppComponent。当您向应用添加更多组件时，必须在此处声明它们。 |
| assets/*                  | 包含要在构建应用程序时按原样复制的图像文件和其他资源文件。   |
|                           |                                                              |

