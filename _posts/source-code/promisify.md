---
title: promisify
date: 2022-02-18 10:35:13
updated: 2022-02-18 10:35:13
categories: 技术
tags: 源码 nodejs
---

在若川源码学习中，学习一个node库（remote-git-tags）的时候，里边用到了一个promisify，这个函数主要是用来将异步回调转变成promise形式的调用

例如：

```js
function getData(data, callback) {
    get('/user', data).then((data)=>{
        callback(null, data)
    }).catch((err)=>{
        callback(new Error(err))
    })
}
// 正常使用
getData({id:1}, (err, data)=>{
    if(err){
       retrun console.error(err)
    }
    // do next thing
    console.log(data)
})

// 转为promise形式调用
const getDataPromise = promisify(getData);
try{
    let data = await getDataPromise({id: 1});
    // do next thing
    console.log(data) 
} catch(err) {
    console.error(err)
}

```

#### 分析一下这个promisify函数做了什么事情

1. 传入一个函数，返回一个函数
2. 返回的函数执行的时候，return一个promise
3. 传入的函数，最后一个函数是回调；返回的函数，传参只传函数执行所需数据，不传回调，而是promisify构造callback，通过resolve reject返回结果

#### 实现

```js
function promisify(fn) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      args.push((err,  ...values) => { // 构造callback函数，调用时候，用户不需要传callback，但是传入的函数fn是需要在函数形参中声明最后一个是callback，并且调用
        if (err) {
          return reject(err)
        }
        resolve(values)
      })
      // fn.apply(this, args)
      Reflect.apply(fn, this, args)
    })
  }
}
```

> 注意最后执行函数的一段代码，apply的使用我们都知道，是绑定 this 指向，这个时候，用 Reflect.apply(fn, this, args) 是什么意思，其实 Reflect 是 es6 中的映射

###### 用 Function.prototype.apply(this, args) 与 Reflect.apply(fn, this, args)，后者优点在哪里呢？

1. 更加清晰

```js
fn.call.apply(this, ...args)
// 这个时候，我们是否知道 fn.call 是个函数,不太明确这个 apply 其实是调用  Function.prototype 上的 apply
Reflect.apply(fn.call, this, args)
// 明确了函数是 fn.call
```

2. 易于类型推断

> ```js
> Reflect.apply(target, thisArgument, argumentsList)
> ```

> ```js
> func.apply(thisArg, [argsArray])
> ```

Reflect 参数必传，不会出现 undefined 和 arrayLike 合并描述传参的情况
