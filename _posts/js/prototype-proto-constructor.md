---
title: prototype constructor __proto__ 的关系与理解
categories: 技术
date: 2021-11-23 14:00
updated: 2021-11-27 14:00
tags: js
---

#### 构造函数 Function

构造函数是一个 es5 的函数，可以通过 new 操作符进行实例化，它具有 prototype 属性，代表构造函数的原型

#### 类 Class

es6 新增类，其实本质是构造函数

#### 实例对象 object

类或者构造函数 new 之后的对象成为类的实例，多个实例共用一个原型，具有**proto**属性，指向原型。即 **object.\_\_proto\_\_ === Function.prototype**

#### 原型 prototype

类构造函数实例化之后，不会产生一份新的原型，多个实例共用一个原型，具有 constructor 属性，指向构造函数，即 **prototype.constrcutor === Function**

##### 示例

```js
function Foo() {}
const foo = new Foo()
```

>

> foo.\_\_proto\_\_ -> Foo.prototype ->prototype

> prototype.constrcutor -> Foo
