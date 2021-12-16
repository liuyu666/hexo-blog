---
title: 函数柯里化
date: 2021-12-16 15:59:41
updated: 2021-12-16 15:59:41
categories: 技术
tags: js
---

### 概念
> 函数柯里化 简单理解就是一个函数传参很多的时候，可以变为分步传参的调用模式。

##### 1. 举个例子： 比如：add(1,2,3) === 6 变为 curryAdd(1)(2)(3)

`代码实现`
```js
function add(a, b, c) {
  return a+b+c
}

function curryAdd(a) {
  return function(b) {
    return function(c){
      return a+b+c
    }
  }
}
```

##### 2. 这时候想要更灵活的实现柯里化呢？ 比如：curryAdd(1,2)(3)
很显然上边的实现不优雅

`下边来看这一段代码`

```js
function add(a, b, c) {
  return a+b+c
}

function curry(fn) {
  const length = fn.length //fn 函数有几个参数
  let args = [].slice.call(arguments, 1) // 初始化参数
  return function() {
    args = args.concat([].slice.call(arguments))
    if(args>=length) return fn.apply(this,args) // 已到达可执行add的情况了（参数个数够了）
    else return arguments.callee // 返回函数本身 继续加参数
  }
}
const curryAdd = curry(add)
curryAdd(1)(2)(3) // 6
curryAdd(1,2)(3) // 6
curryAdd(1)(2,3) // 6
curryAdd(1,2,3) // 6

const curryAdd1 = curry(add, 1)
curryAdd1(2)(3) // 6

```
这就是函数柯里化一段比较完善的代码，注释已作说明，看明白你就会了：），我好像在说废话

##### 3. 假如我向更灵活一点呢，比如：curryAdd(1,2)(3) === 6，curryAdd(2)(3) === 5，传几个参数都行，就是相加所有参数

`show me code`

```js
function curryAdd() {
  var args = [].slice.call(arguments)
  function adder() {
    args = args.concat([].slice.call(arguments))
  }
  adder.toString = function() {
    return args.reduce((pre, cur)=>{
      return pre + cur
    })
  }
  return adder
}

/* 注意： 这儿用+'' 将打印的内容隐式调用 toString，从而执行adder方法 */
console.log(curryAdd(1,2)(3) + '') // 6
console.log(curryAdd(2)(3) + '') //5
```

### 总结
 柯里化是将一个多参数函数分为多次执行传入参数，比如传递的参数是陆续拿到到，你可以在每次拿到所需参数的时候，立即执行柯里化的函数，到最终可以拿到结果