---
title: prototype constructor __proto__ 的关系与理解
categories: 技术
date: 2021-11-23 14:00
updated: 2021-12-22 18:00
tags: js
---

#### 构造函数 Function

构造函数是一个 es5 的函数，可以通过 new 操作符进行实例化，它具有 prototype 属性，代表构造函数的原型

#### 类 Class

es6 新增类，其实本质是构造器Function(Function.constructor === Function)

#### 实例对象 object

类或者构造函数 new 之后的对象成为类的实例，多个实例共用一个原型，具有`__proto__`属性，指向原型。即 `object.__proto__ === Object.prototype`

#### 原型 prototype

类构造函数实例化之后，不会产生一份新的原型，多个实例共用一个原型，具有 constructor 属性，指向构造函数，即 `prototype.constrcutor === Function`

##### 示例

```js
function Foo() {}
const foo = new Foo()
```


instanceof 操作符

`instanceof` 检测左侧的 `__proto__` 原型链上，是否存在右侧的 `prototype` 原型。


看下边这个代码
```js
Function instanceof Object;//true
Object instanceof Function;//true
```

原因：
![](https://raw.githubusercontent.com/liuyu666/md-img/master/ObjectFunction.png)

配合代码看一下：
```js
//①构造器Function的构造器是它自身
Function.constructor=== Function;//true

//②构造器Object的构造器是Function（由此可知所有构造器的constructor都指向Function）
Object.constructor === Function;//true

//③构造器Function的__proto__是一个特殊的匿名函数function() {}
console.log(Function.__proto__);//function() {}

//④这个特殊的匿名函数的__proto__指向Object的prototype原型。
Function.__proto__.__proto__ === Object.prototype//true

//⑤Object的__proto__指向Function的prototype，也就是上面③中所述的特殊匿名函数
Object.__proto__ === Function.prototype;//true
Function.prototype === Function.__proto__;//true
```

### 总结
1. 所有的构造器的 constructor 都指向 Function

2. Function 的 prototype 指向一个特殊匿名函数，而这个特殊匿名函数的 `__proto__` 指向 Object.prototype

参考链接：
  https://juejin.cn/post/6844903454603739149