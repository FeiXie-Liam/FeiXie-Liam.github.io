---
title: contract dsl
date: 2018-08-31 22:37:06
tags:
 - contract
 - groovy
 - yaml
categories:
 - 后端

---

## 基本属性

---

### name

指定当前contract文件生成的测试名称，如果不指定name，默认为文件名。

例如文件名为someTest，默认测试名为：validate_someTest

<!--more-->

groovy:`name(some_test)` 

yaml:`name: ...`

测试名为:validate_some_test

### description

指定当前contract文件的描述，具体使用场景尚不明确。

groovy:`description(...)`

yaml:`description: ...`

### Ignore

指定当前contract文件被忽略，跳过stub和test文件的生成

groovy:`ignore()`

yaml:`ignore: true`