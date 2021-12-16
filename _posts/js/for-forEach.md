---
title: for forEach区别
date: 2021-11-28 17:34:03
updated: 2021-11-28 17:34:03
categories: 技术
tags: js
---

> 今天在看`webpack`文件依赖树获取的时候，视频中作者用的是`for`循环循环一个数组[entry]，当有新的依赖的时候，继续push，我在写的时候用`forEach`，结果发现只有一层依赖，没有的树级依赖，这打破了我原先的认知，~~`for`和`forEach`没什么差别，`forEach`只是简化`for`写法~~

#### 区别

我在循环的时候动态增加数组项，`for`根据用`index ` <  `arr.length`判断会继续循环后边的项；而`forEach`不会继续循环。

#### 看例子：

###### `forEach`

```js
var arr = [1,2];
arr.forEach((item, i)=>{  
        console.log('item:', arr[i])
        if(i<5) {    
            arr[i+1] = ++arr[i+1]
            arr.push(i)
        }                 
})
```

###### 结果：

![](https://raw.githubusercontent.com/liuyu666/md-img/master/image-20211128174054251.png)


###### `for`

```js
var arr = [1,2];
for (var i = 0; i < arr.length; i++) {
        console.log('item:', arr[i])
        if(i<4) {
            arr[i+1] = ++arr[i+1]
            arr.push(i)
        }                 
}
console.log(arr)
```

###### 结果

![](https://raw.githubusercontent.com/liuyu666/md-img/master/image-20211128174752231.png)


#### 思考

`for`循环的时候，判断是否执行下一次循环是实时拿到`arr.length`，这个时候`arr.length`是随可能循环增加的；而`forEach`内部实现也是一个`for`循环，循环的时候截止条件是直接存在一个变量`length` = `arr.length`,判断条件变成了`index`  < `length`而这个length是不变的，在循环开始已经`length` = `arr.length`赋值确定了。