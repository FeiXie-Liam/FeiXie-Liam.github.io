---
title: ReactiveX学习
date: 2018-12-17 19:23:01
tags:
 - RxJava
 - ReactiveX
categories:
 - ReactiveX

---

ReactiveX是Reactive Extensions的缩写，一般简写为Rx。Rx是一个编程模型，目标是提供一致的编程接口，帮助开发者更方便的处理异步数据流，Rx库支持.NET、JavaScript和C++，Rx近几年越来越流行了，现在已经支持几乎全部的流行编程语言了。社区网站是 [ReactiveX](http://reactivex.io/) 。

<!--more-->

## ReactiveX操作符

---

### Just

为特定的目标创建一个Observable对象并发射该对象到流中.

与From操作符类似, 但From操作符会将可迭代的数组中的元素取出来组合成数据流, 而Just则将数组对象整体发射到数据流.

![Just](http://images2.imagebam.com/ee/b4/dd/94a3f21095224544.png)

### Interval

创建一个在固定时间间隔内, 持续发射一个无穷的整数序列的Observable对象

![Interval](http://images2.imagebam.com/21/79/a6/9c5d291095224594.png)

### FlatMap

转换被Observable对象发射的对象为Observables对象, 然后展开Observables对象为单个的Observable对象.

![FlatMap](http://reactivex.io/documentation/operators/images/flatMap.c.png)

### subscribeOn与observeOn

subscribeOn与observeOn操作符都用于切换线程. 

1. subscribeOn的调用会切换之前的线程
2. observeOn的调用会切换之后的线程
3. observeOn之后调用subscribeOn无效

eg:

```java
Observable
.map                    // 操作1
.flatMap                // 操作2
.subscribeOn(io)
.map                    //操作3
.flatMap                //操作4
.observeOn(main)
.map                    //操作5
.flatMap                //操作6
.subscribeOn(io)        //!!特别注意
.subscribe(handleData)
```

1. 操作1，操作2是在io线程上，因为之后subscribeOn切换了线程
2. 操作3，操作4也是在io线程上，因为在subscribeOn切换了线程之后，并没有发生改变。
3. 操作5，操作6是在main线程上，因为在他们之前的observeOn切换了线程。
4. 特别注意那一段，对于操作5和操作6是无效的

