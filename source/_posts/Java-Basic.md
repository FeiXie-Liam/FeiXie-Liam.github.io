---
title: Java Basic
date: 2018-09-05 23:03:14
tags:
 - java
categories:
 - 后端

---

## Enum

---

基本示例:

<!--more-->

```java
public enum Options {
    LIST_ALL_BOOKS,
    RETURN_BOOK,
    QUIT
}
```

可继承的方法：

| 返回类型                      | 方法名称                                         | 方法说明                                                     |
| ----------------------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| `int`                         | `compareTo(E o)`                                 | 比较此枚举与指定对象的顺序                                   |
| `boolean`                     | `equals(Object other)`                           | 当指定对象等于此枚举常量时，返回 true。                      |
| `Class<?>`                    | `getDeclaringClass()`                            | 返回与此枚举常量的枚举类型相对应的 Class 对象                |
| `String`                      | `name()`                                         | 返回此枚举常量的名称，在其枚举声明中对其进行声明             |
| `int`                         | `ordinal()`                                      | 返回枚举常量的序数（它在枚举声明中的位置，其中初始常量序数为零） |
| `String`                      | `toString()`                                     | 返回枚举常量的名称，它包含在声明中                           |
| `static<T extends Enum<T>> T` | `static valueOf(Class<T> enumType, String name)` | 返回带指定名称的指定枚举类型的枚举常量。                     |

若需要重载枚举类型的具体返回值，可以通过以下方式进行：

```java
public enum Options {
    LIST_ALL_BOOKS{
        @Override
        public String toString() {
            return String.valueOf(super.ordinal());
        }
    },
    RETURN_BOOK{
        @Override
        public String toString() {
            return String.valueOf(super.ordinal());
        }
    },
    QUIT{
        @Override
        public String toString() {
            return String.valueOf(super.ordinal());
        }
    }
}
```

其中`super.ordinal()`默认返回当前没去常量的序数（从零开始）。