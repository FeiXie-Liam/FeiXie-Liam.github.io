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