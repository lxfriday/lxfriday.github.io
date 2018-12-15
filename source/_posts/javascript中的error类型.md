---
title: js中的error类型
comments: true
date: 2018-09-04 21:06:37
categories: javascript
tags: javascript中的error类型
---

Error 是通用异常构造器，所有其他的异常构造器都是它的子构造器。

## 异常的类型

1. `EvalError` 在标准中未使用，只是为了兼容上一版本的标准
1. `RangeError` 表示一个数值超出了允许的范围
    > new Array(-1)
    RangeError: Invalid array length
1. `ReferenceError` 表示发现了一个非法的引用值，通常这是一个未知的变量
    > unknownVariable
    ReferenceError: unknownVariable is not defined
1. `SyntaxError` 表示产生了一个语法解析错误
    > eval('3 + ')
    SyntaxError: unexpected end of input
1.  `TypeError` 表示一个被操作的实际类型与其期望的类型不一致
    > undefined.foo
    TypeError: Cannot read property 'foo' of undefined
1. `URIError` 表示某个全局的 URI 控制函数的使用不兼容其定义
    > decodeURI('%2')
    URIError: URI malformed

## 异常的属性值
 
 1. `name` 异常的类型
 1. `message` 异常的信息
 1. `stack` 异常发生的堆栈信息

例子
 
 ```js
try {
  throw new Error('oh no!,this is a error!')
} catch (e) {
  console.log(e.name);
  // Error
  console.log(e.message);
  // oh no!,this is a error!
  console.log(e.stack);
  // Error: oh no!,this is a error!
  // at <anonymous>:2:9
}
```
