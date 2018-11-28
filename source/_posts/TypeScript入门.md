---
title: TypeScript入门
date: 2018-11-28 19:06:02
tags:
 - 前端
 - TypeScript
categories:
 - 前端

---

TypeScript是JavaScript的超集, TypeScript可以兼容JavaScript, TypeScript通过类型注解提供编译时的静态类型检查. TypeScript可以处理已有的JavaScript代码, 并只对TypeScript代码进行编译.

<!--more-->

## 语法特性

---

- 类 classes
- 接口 interfaces
- 模块 modules
- 类型注解 type annotations
- 编译时类型检查 compile time type checking
- Arrow函数

## 类型批注

---

example:

```typescript
function Add(left: number, right: number): number {
    return left + right;
}
```

TypeScript可以忽略类型批注, 基本类型的批注为`number, bool, string`, 弱类型或动态类型结构则是`any`类型.

enum:

```typescript
enum Color {Red = 1, Green, Blue};
let c: Color = Color.Red;

let colorName: string = Color[2];//colorName = 'Green'
```

enum默认从0开始, 也可以指定成员的值

any和Object的区别:

```typescript
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
```

## 接口

---

example:

```typescript
interface Shape {
    name: string;
    width: number;
    height: number;
    color?: string;
}
    
function area(shape: Shape) {
    var area = shape.width * shape.height;
    return "I'm" + shape.name + " with area " + area + " cm squared";
}

console.log(area({name: "rectangle", width: 30, height: 40}));
```

接口可以作为一个类型批注

在interface中的字段后方加入`?`符号表示该字段在interface可为空.

## 箭头函数表达式(lambda表达式)

---

lambda表达式:`()=>{something}或()=>something`相当于js中的函数, 它的好处是可以自动将函数中的this附加到上下文中.

example:

```typescript
var shape = {
    name: "rectangle",
    popup: function() {
 
        console.log('This inside popup(): ' + this.name);
 
        setTimeout(function() {
            console.log('This inside setTimeout(): ' + this.name);
            console.log("I'm a " + this.name + "!");//输出为:I'm a !
        }, 3000);
 
        setTimeout(() => {
          console.log('This inside setTimeout(): ' + this.name);
            console.log("I'm a " + this.name + "!");//输出为:I'm a rectangle!  
        })
    }
};
 
shape.popup();
```

## 类

---

TypeScript支持集成了可选的类型批注支持的ECMAScript 6的类

example:

```typescript
class Shape {
 
    area: number;
    private color: string;
 
    constructor ( public name: string, width: number, height: number ) {
        this.area = width * height;
        this.color = "pink";
    };
 
    shoutout() {
        return "I'm " + this.color + " " + this.name +  " with an area of " + this.area + " cm squared.";
    }
}
 
var square = new Shape("square", 30, 30);
 
console.log( square.shoutout() );
console.log( 'Area of Shape: ' + square.area );
console.log( 'Name of Shape: ' + square.name );//不会报错
console.log( 'Color of Shape: ' + square.color );//报warning
console.log( 'Width of Shape: ' + square.width );
console.log( 'Height of Shape: ' + square.height );
```

## 继承

---

使用`extends`创建派生类

example:

```typescript
class Shape3D extends Shape {
 
    volume: number;
 
    constructor ( public name: string, width: number, height: number, length: number ) {
        super( name, width, height );
        this.volume = length * this.area;
    };
 
    shoutout() {
        return "I'm " + this.name +  " with a volume of " + this.volume + " cm cube.";
    }
 
    superShout() {
        return super.shoutout();
    }
}
 
var cube = new Shape3D("cube", 30, 30, 30);
console.log( cube.shoutout() );
console.log( cube.superShout() );
```