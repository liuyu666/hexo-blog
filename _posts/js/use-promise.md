---
title: promise 使用篇
categories: 技术
date: 2021-11-26 14:00
updated: 2021-11-26 14:00
tags: js
---

首先列举一下 promise 的方法

- Promise.all()
- Promise.allSettled()
- Promise.any()
- Promise.prototype.catch()
- Promise.prototype.finally()
- Promise.race()
- Promise.reject()
- Promise.resolve()
- Promise.prototype.then()

#### promise 简介

**Promise** 对象用于表示一个异步操作的最终完成 (或失败)及其结果值。

#### 基础篇

`Promise.prototype.then()`

`Promise.reject() `

`Promise.resolve() `

`Promise.prototype.catch() `

`Promise.prototype.finally() `

> new 一个 Promise，传一个函数，函数形参有两个，resolve 和 reject，成功用 resolve，失败用 reject，状态一旦改变，不能再次更改，之后会走到.then 方法，then 接受两个参数，分别是成功和失败之后执行的函数，函数返回 promise 示例，可以继续链式调用，假如 then 没有第二个参数，失败后会走到 catch 方法，.catch 等效.then 第二个参数，作为兜底，无论成功与否报错与否，最后执行 finally

```js
// promise.js

new Promise((resolve, reject) => {
  // 做一些异步操作
  console.log('start: 故事开始的地方')
  // success
  resolve('success1')
})
  .then(
    (data) => {
      console.log('data: ', data) // success1
      return new Promise((resolve, reject) => {
        // 做一些异步操作
        // fail
        reject('fail1')
      })
    },
    (err) => {
      // 不执行
    }
  )

  .then(
    (data) => {
      // 不执行
    },
    (err) => {
      console.log('err: ', err) // fail1
      return new Promise((resolve, reject) => {
        // 做一些异步操作
        // fail
        reject('fail2')
      })
    }
  )

  .catch((err) => {
    console.log('err: ', err) // fail2
    return new Promise((resolve, reject) => {
      // 做一些异步操作
      // fail
      reject('fail3')
    })
  })

  .then(
    (data) => {},
    (err) => {
      console.log('err: ', err) // fail3

      throw new Error('异常错误1')
    }
  )
  .catch((err) => {
    console.log('err: ', err) // Error: 异常错误1
  })
  .finally(() => {
    console.log('end: 无论如何我都在终点等你')
  })
```

###### 执行结果：

```bash
$ node promise.js
start
data:  success1
err:  fail1
err:  fail2
err:  fail3
err:  Error: 异常错误1
end: 无论如何我都在终点等你
```

#### 扩展篇

`Promise.all()`

`Promise.allSettled()`

`Promise.any()`

`Promise.race()`

**Promise.all():** 全部成功 resolve 结果数组，一个失败就 reject 第一个错误

> Promise.all() 方法接收一个 promise 的 iterable 类型（注：Array，Map，Set 都属于 ES6 的 iterable 类型）的输入，并且只返回一个[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)实例， 那个输入的所有 promise 的 resolve 回调的结果是一个数组。这个[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)的 resolve 回调执行是在所有输入的 promise 的 resolve 回调都结束，或者输入的 iterable 里没有 promise 了的时候。它的 reject 回调执行是，只要任何一个输入的 promise 的 reject 回调执行或者输入不合法的 promise 就会立即抛出错误，并且 reject 的是第一个抛出的错误信息。

**Promise.allSettled() :** 全部结束 resolve 结果数组，里边有状态和成功失败的结果

> 返回一个在所有给定的 promise 都已经`fulfilled`或`rejected`后的 promise，并带有一个对象数组，每个对象表示对应的 promise 结果；成功项是`{status: 'fulfilled', value: ''}`，失败项是`{status: 'rejected', reason: ''}` 。

**Promise.any() ：** 只要有一个成功就 resolve 这个项的值，全部失败就 reject[`AggregateError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/AggregateError)

> 接收一个[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)可迭代对象，只返回一个[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)实例，如果有一个成功，就直接 resolve，全部失败的话 reject 一个[`AggregateError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/AggregateError)类型的实例。本质上，这个方法和[`Promise.all()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)是相反的。

**Promise.race() ：** 拿到第一个结果，不管成功失败都返回

> 返回一个 promise，一旦迭代器中的某个 promise 解决或拒绝，返回的 promise 就会解决或拒绝。
