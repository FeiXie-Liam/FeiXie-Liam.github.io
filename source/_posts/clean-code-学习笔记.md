---
title: clean code 学习笔记
date: 2018-11-13 11:15:23
tags:
 - 代码规范
categories:
 - 代码规范
---

## 有意义的命名

---

### 名副其实

- 使用能够表达本意的名称
- 发现更好的名称就替换旧名称

<!--more-->

### 避免误导

- 避免使用其他领域的常用名称
  - hp, aix, sco为UNIX平台专有名词
  - List对程序员有特殊含义
- 提防使用差异很小的名称
- 避免使用消息字母l, 大写字母O作为变量名

### 做有意义的区分

- 避免混合使用Product, ProductInfo, ProductData这类名称不同,意义相同的类名

### 使用读得出来的名称

- 避免自造词

### 使用可搜索的名称

- 单字母名称和数字常量可能在代码中多处使用, 应赋予其便于搜索的名称

### 避免使用编码

- 匈牙利语标法
- 成员前缀
- 接口和实现
  - 宁愿实现编码也要避免接口编码

### 类名

- 类名和对象名应该是名字或名词短语
- 类名不应该是动词

### 方法名

- 方法名应该是动词和动词短语
- 属性访问器, 修改器和断言应该使用get, set, is并根据其值命名

### 别扮可爱

- 避免使用俗话和俚语

### 每个概念对应一个词

- 同一概念同一使用一个词
- 避免使用近义词表示同一概念

### 别用双关语

- 一个词不能对应多个不同概念

### 使用解决方案领域名称优先于所涉问题领域名称

- 优先使用计算机科学术语
- 无法使用程序员熟悉的术语, 再采用所涉问题领域的名称

### 添加有意义的语境

- 使用命名良好的类, 函数或名称空间放置名称

## 函数

---

### 短小

- 函数应该尽量短小(3-4行)
- 缩进层级不应该多余一层或两层

### 只做一件事

- 一件事: 函数只做了函数名下同一抽象层上的步骤
- 如果还能再拆出一个函数, 该函数不仅是单纯的重新诠释其实现, 则该函数不止做了一件事
- 函数能被切分为多个**区段**, 便是函数做事太多的征兆

### 每个函数一个抽象层级

- 自顶向下读代码: 向下规则. 让每个函数后面都跟着位于下一抽象层级的函数, 这样一来, 在查看函数列表时, 就能循抽象层级向下阅读了.

### switch语句

- 使用多态实现,将switch埋藏在较低的抽象层级, 而且永远不重复.

一般代码:

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType {
    switch(e.type) {
    	case COMMISSIONED:
    		return calculateCommissionedPay(e);
    	case HOURLY:
    		return calculateHourlyPay(e);
    	case SALARIED:
    		return calculateSalariedPay(e);
    	default:
    		throw new InvalidEmployeeType(e.type);
	}
}
```

使用抽象工厂隐藏switch语句:

```java
public abstract class Employee {
    public abstract boolean isPayday();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);
}

----------------------------------

public interface EmployeeFactory {
	public Empolyee makeEmployee(EmployeeRecord r) throws InvalidEmmployeeType;
}

----------------------------------
    
public class EmployeeFactoryImpl implements EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
    	switch(r.type) {
        	case COMMISSIONED:
    			return new CommissionedEmployee(r);
    		case HOURLY:
    			return new HourlyEmployee(r);
    		case SALARIED:
    			return new SalariedEmployee(r);
        	default:
        	    throw new InvalidEmployeeType(r.type);
    	}
	}        
}
```

### 使用描述性的名称

- 长而具有描述性的名称, 比短而令人费解的名称好
- 命名方式要保持一致

### 函数参数

- 参数数量越少越好, 应该尽量避免三或三个以上参数
- 参数不易对付, 带有太多概念性
- 参数增加,测试难度也会增加
- 尽量避免标识参数(boolean)
- 如果函数看来需要多个参数(2个以上), 考虑将其中一些参数封装为类

### 无副作用

- 函数对自己类中的变量作出未能预期的变动
- 尽量避免使用输出参数. 修改所属对象的状态作为替代

### 分隔指令与询问

- 函数要么做什么事, 要么回答什么事, 二者不可得兼

### 使用异常替代返回错误码

- Try/catch代码块丑陋不堪, 最好把try和catch代码块的主体部分抽离出来, 另外形成函数

### 去除重复代码

## 注释

---

注释是弥补我们在用代码表达意图时遭遇的失败, 代码时刻在变动, 而注释往往不会跟着代码变动, 因此容易造成注释越来越不准确.

- 注释无法美化糟糕的代码
- 用代码来阐述

### 好注释

- 法律信息

  > Copyright (C) 2003, 2004, 2005 by Object Mentor, Inc. All rights reserved.

- 提供信息的注释

  > //format matched kk:mm:ss EEE, MMM dd, yyyy
  >
  > Pattern timeMatcher = Pattern.compile("\\\\d\*:\\\\d\*:\\\\d\*  \\\\w\*, \\\\w\* \\\\d\*, \\\\d\*")

- 对意图的解释

- 阐释

- 警示

- TODO注释

- 公共API的Javadoc

### 怀注释

- 喃喃自语

- 多余的注释

- 误导性注释

- 循规式注释

- 日志式注释

- 废话注释

- 能用函数或变量时别用注释

  > //does the module from the global list <mod> depend on the
  >
  > //subsystem we are part of?
  >
  > if (smodule.getDependSubsystems().contains(subSysMod.getSubsystem())
  >
  > \>\>\>
  >
  > <<<
  >
  > ArrayList moduleDependees = smodule.getDependSubsystems();
  >
  > String ourSubSystem = subSysMod.getSubSystem();
  >
  > if(moduleDependees.contains(ourSubSystem))

- 位置标记

- 括号后的注释

- 归属与署名

- 注释掉的代码

- HTML注释

- 非本地信息

- 信息过多

- 联系不明显

## 格式

---

### 垂直格式

- 短文件通常比长文件易于理解
- 概念间(封包声明, 导入声明, 每个函数)添加垂直方向的区隔
- 相关联的代码应该相互靠近
- 本地变量放置在函数的顶部
- 实体变量应该在类的顶部声明
- 调用函数放在临近被调用函数上方
- 执行相似操作的一组函数应该放置在一起

### 横向格式

- 上限120字符(不超过屏幕宽度)
- 不需要水平对齐

### 遵从团队规则

## 对象和数据结构

---

### 数据抽象

使用数据抽象隐藏实现细节. 隐藏实现并非只是在变量之间放上一个函数层那么简单. 隐藏实现关乎抽象! 类并不简单的使用取值器和赋值器将其变量推向外面, 而是暴露抽象接口, 以便用户无需了解数据的实现就能够操作数据本体.

具象点:

```java
public class Point {   
    public double getX() ...;
    public double getY() ...;
    
    public void setX() ...;
    public void setY() ...;
}
```

抽象点:

```java
public class Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
    double getR();
    double getTheta();
    void setPolar(double r, double theta);
}
```

### 数据, 对象的反对称性

- 对象吧数据隐藏于抽象之后, 暴露操作数据的函数
- 数据结构暴露数据, 没有提供有意义的函数

过程式形状代码:

```java
public class Square {
    public Point topLeft;
    public double side;
}

public class Rectangle {
    public Point topLeft;
    public double height;
    public double width;
}

public class Circle [
    public Point center;
    public double redius;
]

public class Geometry {
    public final double PI = 3.141592653589793;
    
    public double area(Object shape) throws NoSuchShapeException {
        if (shape instanceof Square) {
            Square s = (Square)shape;
            return s.side * s.side;
        }
        else if (shape instanceof Rectangle) {
            Rectangle r = (Rectangle)shape;
            return r.height * r.width;
        }
        else if (shape instanceof Circle) {
            Circle c = (Circle)shape;
            return PI * c.radius * c.radius;
        }
        throw new NoSuchShapeException();
    }
}
```

多态式形状:

```java
public class Square implements Shape {
    private Point topLeft;
    private double side;
    
    public double area() {
        return side * side;
    }
}

public class Rectangle implements Shape {
    private Point topLeft;
    private double height;
    private double width;
    
    public double area() {
        return height * width;
    }
}

public class Circle implements Shape {
    private Point center;
    private double redius;
    private final double PI = 3.141592653589793;
    
    public double area() {
        return PI * radius * radius;
    }
}
```

***过程式代码(使用数据结构的代码)便于在不改动既有数据结构的前提下添加新函数, 面对对象代码便于在不改动既有函数的前提下添加新类.***

或

***过程式代码难以添加新数据结构, 因为必须修改所有函数. 面向对象代码难以添加新函数, 因为必须修改所有类.***

### 得墨忒尔律

模块不应该了解它所操作对象的内部情况, 类C的方法f只应该调用以下对象的方法:

- C
- 由f创建的对象
- 作为参数传递给f的对象
- 由C的实体变量持有的对象

违反德墨忒尔律的典型案例:

> final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();

### 数据传送对象

最为精炼的数据结构, 是一个只有公共变量, 没有函数的类. 这种数据结构有时被称为数据传送对象, 或DTO(Data Transfer Objects). DTO是非常有用的结构, 尤其是在于数据库通信或解析套接字传递的消息场景中.

另一种更常见的结构为bean结构, 该结构由赋值器和取值器操作的私有变量. 不过该结构相比于DTO并没有实质性的好处.

Active Record 是一种特殊的DTO形式. 它拥有公共(或可豆式访问的)变量的数据结构, 但通常也会拥有类似save和find这样的可浏览方法.

## 错误处理

---

错误处理很重要, **但是如果错误处理搞乱了代码逻辑, 就是错误的做法**

- 使用异常而非返回码
- 先写try/catch/finally语句
- 使用不可控异常
- 对异常发生的环境进行说明
- 依据调用者需要定义异常类
- 定义常规流程
- 别返回, 传递null值

## 边界

---

### 使用第三方代码

以java.util.Map为例, 应用程序需要一个包容Sensor对象的Map映射图, 大概是这样:

```java
Map sensors = new HashMap();
Sensor s = (Sensor)sensors.get(sensorId);
```

代码的调用端承担了Map中取得对象并将其转化为正确类型的职责, 且Map暴露的接口可能会超出应用程序的期望(不希望删除).

更整洁的方式大致如下:

```java
public class Sensors {
    private Map sensors = new HashMap();
    
    public Sensor getById(String id) {
        return (Sensor) sensors.get(id);
    }
}
```

### 浏览和学习第三方库

**学习性测试**: 不要在生产代码中实验新东西, 而是编写测试来理解第三方代码.

- 学习性测试时一种精确实验, 班组我们增进对api的理解
- 当第三方程序包发布新版本, 可以运行学习性测试, 查看程序包的行为有没有改变
- 第三方程序包的修改和测试不兼容时, 能够马上发现

## 单元测试

---

### TDD三定律

- 在编写不能通过的单元测试前, 不能编写生产代码
- 只可编写刚好无法通过的单元测试, 不能编译也算不通过
- 只可编写刚好足以通过当前失败测试的生产代码

### 保持测试整洁

**测试代码与生产代码同样重要**

- 单元测试让生产代码可扩展, 可维护, 可复用
- 有了测试, 就不用担心对代码的修改

测试整洁的要素在于**可读性**, 明确, 简洁, 还有足够的表达力. 整洁的测试中不应该包含太多细节, 应该呈现出构造-操作-检验(BUILD-OPERATE-CHECK)模式.

整洁的代码还遵循以下5条规则(F.I.R.S.T):

- 快速(Fast)
- 独立(independent)
- 可重复(Repeatable)
- 自足验证(Self-Validating)
- 及时(Timely)

## 类

---

### 类应该短小

通过权责(responsibility)衡量类的大小

- 单一权责原则
- 内聚
  - 类应该只有少量实体变量
  - 类中的每个方法都应该操作一个或多个这种变量
  - 通常方法操作的变量越多, 该类具有越大的内聚性

### 组织类结构来避免修改

希望将系统打造成在添加或修改特性时尽可能少惹麻烦的架子. 在理想系统中, 我们通过**扩展系统**而非修改现有代码来添加新特性.

### 隔离修改

借助接口和抽象类来隔离实现细节修改带来的影响.

依赖倒置原则(Dependency Inversion Principle, DIP), 类应当依赖于抽象而不是依赖于具体细节.

