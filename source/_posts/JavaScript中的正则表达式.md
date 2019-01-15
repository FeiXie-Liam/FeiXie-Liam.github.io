---
title: JavaScript中的正则表达式
date: 2019-01-08 15:27:56
tags:
 - JavaScript
 - 正则表达式
categories:
 - 前端

---

正则表达式使用单个字符来描述, 匹配一系列匹配某个句法规则的字符串. 在很多文本编辑器里，正则表达式通常被用来检索、替换那些匹配某个模式的文本. 在许多程序设计语言中都支持利用正则表达式进行字符串操作.

<!--more-->

## 正则表达式基本语法

---

![基本语法](https://upload-images.jianshu.io/upload_images/8656832-7fb7d25233929626.png)

## JavaScript中的正则表达式

---

### Patterns and flags

JavaScript有两种方式创建一个正则表达式, 一个正则表达式由pattern和可选的flags组成的.比如:

```javascript
regexp = new RegExp('pattern', 'flags')
```

或者使用`"/"`符号:

```javascript
regexp = /pattern/; //no flags
regexp = /pattern/gmi; //with flags
```

一般情况下, 我们会使用简单语法`/.../`但是这种语法不支持插入变量. 因此当你需要动态的创造正则规则时,需要使用`new RegExp`

在搜索字符串中符合特定规则的子字符串时, 可以使用`search`函数和正则表达式:

```javascript
let search = prompt('what you want?', 'love');
let regexp = new RegExp(search);

//find whatever user whats
alert('I love JavaScript'.search(regexp));
```

在JavaScript中一共有5个flags:

| flag | description                        |
| ---- | ---------------------------------- |
| `i`  | 匹配时将忽略大小写                 |
| `g`  | 匹配时匹配所有项, 默认只匹配第一项 |
| `m`  | 多行模式                           |
| `u`  | 启用完整的unicode支持              |
| `y`  | 粘滞模式(Sticky mode)              |

比如:

```javascript
let str = 'I love JavaScript!'

alert(str.search(/LOVE/)); //-1 (not found)
alert(str.search(/LOVE/i)); //2

let str = "I love JavaScript!";

=======================================

let reg = /javascript/iy;

alert( reg.lastIndex ); // 0 (default)
alert( str.match(reg) ); // null, not found at position 0

reg.lastIndex = 7;
alert( str.match(reg) ); // JavaScript (right, that word starts at position 7)

// for any other reg.lastIndex the result is null
```

`y`flag表示精确匹配, 即通过设置`reg.lastIndex`, 从该index位置开始匹配正则表达式, 若匹配不上则返回null.

### String和RexExp的方法

1. 内建的RegExp类包含了许多正则方法
2. string包含了一些常见的使用正则的方法

#### String

string中的内置方法有:

- search(reg)
- match(reg)
- split(reg|substr, limit)
- replace(str|reg, str|func)

`.search`和`.match`方法默认都仅查找第一个匹配元素,如果需要匹配所有的元素需要使用`g`flag, 当使用了`g`flag其他属性将会失效(eg.括号):

```javascript
let str = "HO-Ho-ho!";

let result_without_g = str.match( /ho/i );
let result_with_g = str.match( /ho/ig );
let result_with_g_and_parentheses = str.match( /h(o)/ig );

alert( result_without_g ); // HO, Ho, ho
alert( result_with_g ); // HO, Ho, ho
alert( result_with_g_and_parentheses ); // HO, Ho, ho
```

**注意: 使用match方法的返回时`null`而不是空的数组**

split方法可以接收字符串和正则两种参数:

```javascript
alert('12-34-56'.split('-')) // [12, 34, 56]
alert('12-34-56'.split(/-/)) // [12, 34, 56]
```

replace方法是字符串操作的**强力武器**

一个简单的示例如下:

```javascript
// replace dash by colon
alert('12-34-56'.replace("-", ":")) // 12:34-56
alert('12-34-56'.replace( /-/g, ":"))  // 12:34:56
```

replace的第二个参数是替换后的字符串, 该参数可使用以下符号:

| Symbol | Inserts                                                      |
| ------ | ------------------------------------------------------------ |
| `$$`   | `"$"`                                                        |
| `$&`   | the whole match                                              |
| $`     | a part of the string before the match                        |
| `$'`   | a part of the string after the match                         |
| `$n`   | if `n` is a 1-2 digit number, then it means the contents of n-th parentheses counting from left to right |

示例如下:

```javascript
let str = "John Doe, John Smith and John Bull.";

// for each John - replace it with Mr. and then John
alert(str.replace(/John/g, 'Mr.$&'));
// "Mr.John Doe, Mr.John Smith and Mr.John Bull.";
alert(str.replace(/John/g, 'Mr.$`'));
// "Mr. Doe, Mr. Smith and Mr. Bull.";
alert(str.replace(/Bull/g, 'Mr.$'));
// "John Doe, John Smith and John Mr...";

=================================
    
let str = "John Smith";

alert(str.replace(/(John) (Smith)/, '$2, $1')) // Smith, John

=================================
    
let i = 0;

// show and replace all matches
function replacer(str, offset, s) {
  alert(`Found ${str} at position ${offset} in string ${s}`);
  return str.toLowerCase();
}

let result = "HO-Ho-ho".replace(/ho/gi, replacer);
alert( 'Result: ' + result ); // Result: ho-ho-ho

// shows each match:
// Found HO at position 0 in string HO-Ho-ho
// Found Ho at position 3 in string HO-Ho-ho
// Found ho at position 6 in string HO-Ho-ho
```

如果replace函数的第二个参数接收一个函数, 且第一个参数包含多个括号, 则第二个函数的参数顺序为:

1. 匹配的字符串
2. 括号包含的匹配值按顺序往下
3. 匹配的索引值
4. 源字符串

#### RegExp

RegExp的常见方法有:

1. test(str)
2. exec(str)

test方法接收一个字符串, 并返回`true/false`, 例子如下:

```javascript
let str = "I love JavaScript";

// these two tests do the same
alert( /love/i.test(str) ); // true
alert( str.search(/love/i) != -1 ); // true
```

exec方法主要解决了字符串中存在括号以及存在多个匹配子字符串的情况下的复杂情况, 例子如下:

```javascript
let str = "A lot about JavaScript at https://javascript.info";

let regexp = /JAVA(SCRIPT)/ig;

// Look for the first match
let matchOne = regexp.exec(str);
alert( matchOne[0] ); // JavaScript
alert( matchOne[1] ); // script
alert( matchOne.index ); // 12 (the position of the match)
alert( matchOne.input ); // the same as str

alert( regexp.lastIndex ); // 22 (the position after the match)

// Look for the second match
let matchTwo = regexp.exec(str); // continue searching from regexp.lastIndex
alert( matchTwo[0] ); // javascript
alert( matchTwo[1] ); // script
alert( matchTwo.index ); // 34 (the position of the match)
alert( matchTwo.input ); // the same as str

alert( regexp.lastIndex ); // 44 (the position after the match)

// Look for the third match
let matchThree = regexp.exec(str); // continue searching from regexp.lastIndex
alert( matchThree ); // null (no match)

alert( regexp.lastIndex ); // 0 (reset)
```

一般情况下, 会在循环中使用exec函数, 以达到遍历整个字符串进行匹配的场景:

```javascript
let str = 'A lot about JavaScript at https://javascript.info';

let regexp = /javascript/ig;

let result;

while (result = regexp.exec(str)) {
  alert( `Found ${result[0]} at ${result.index}` );
}
```

循环会在`result.exec`函数返回`null`时自动停止.

## 组捕获

---

正则表达式的一部分规则通过`(...)`包含, 叫做组捕获, 组捕获可以:

1. 可以将字符串中, 符合匹配规则的部分单独存放在一个list中
2. 如果在`()`加入量词(+*?等)将会捕获匹配了量词的整体子字符串. eg:

```javascript
alert( 'Gogogo now!'.match(/(go)+/i) ); // "Gogogo"
```

对于使用`()`包裹起来的字符串, 最后的匹配结果将是符合`()`规则的子字符串从左至右的集合, 其中集合的首项为匹配规则的字符串本身, eg:

```javascript
let str = '<h1>Hello, world!</h1>';
let reg = /<(.*?)>/;

alert( str.match(reg) ); // Array: ["<h1>", "h1"]
```

但对于`match`方法, 仅当规则中不包含`/.../g`flag. 如果包含我们需要所有匹配的组, 则需要使用`RegExp`类中的`exec`方法:

```javascript
let str = '<h1>Hello, world!</h1>';

// two matches: opening <h1> and closing </h1> tags
let reg = /<(.*?)>/g;

let match;

while (match = reg.exec(str)) {
  // first shows the match: <h1>,h1
  // then shows the match: </h1>,/h1
  alert(match);
}
```

组捕获同样支持嵌套组, eg:

```javascript
let str = '<span class="my">';

let reg = /<(([a-z]+)\s*([^>]*))>/;

let result = str.match(reg);
alert(result); // <span class="my">, span class="my", span, class="my"
```

如果一个组是可选的且匹配不存在, 那么对于的组捕获结果中会返回`undefined`的数组元素, eg:

```javascript
let match = 'ack'.match(/a(z)?(c)?/)

alert( match.length ); // 3
alert( match[0] ); // ac (whole match)
alert( match[1] ); // undefined, because there's nothing for (z)?
alert( match[2] ); // c
```

如果我们需要使用`()`来正确使用quantifier, 但不需要捕获`()`中的内容, 可以在`()`内部开头添加`?:`, eg:

```javascript
let str = "Gogo John!";
// exclude Gogo from capturing
let reg = /(?:go)+ (\w+)/i;

let result = str.match(reg);

alert( result.length ); // 2
alert( result[1] ); // John
```

## 反向引用

---

通过`$n`,组同样可以应用于替换字符串中:

```javascript
let name = "John Smith";

name = name.replace(/(\w+) (\w+)/i, "$2, $1");
alert( name ); // Smith, John
```

另外一种反向引用的实现方式为`\n`, 假设有这样的场景, 我们需要寻找被`''`或者`""`包裹的内容, 那么可以使用`['"](.*?)['"]`结构来匹配, 但这样的话, 会存在一个问题:

```javascript
let str = "He said: \"She's the one!\".";

let reg = /['"](.*?)['"]/g;

// The result is not what we expect
alert( str.match(reg) ); // "She'
```

为了解决这一问题, 我们便可以使用`\n`反向引用:

```javascript
let str = "He said: \"She's the one!\".";

let reg = /(['"])(.*?)\1/g;

alert( str.match(reg) ); // "She's the one!"
```

