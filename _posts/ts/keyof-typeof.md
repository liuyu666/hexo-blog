---
title: ts keyof typeof
date: 2022-02-16 17:19:06
updated: 2022-02-16 17:19:06
categories: 技术
tags: ts
---

`vue-next` 源码工具函数中有这么一段代码，是用ts写的

```js
const hasOwnProperty = Object.prototype.hasOwnProperty
export const hasOwn = (
  val: object,
  key: string | symbol
): key is keyof typeof val => hasOwnProperty.call(val, key)
```

`key is keyof typeof val => hasOwnProperty.call(val, key)`

分两部分来看

- `key is xxx`:  类型保护，将类型 key 缩小为`xxx`类型，
- `keyof typeof val`:
  1. `typeof` 获取val类型
  2. `keyof`  获取该类型所有键

#### `keyof` 高阶用法

```js
function prop(obj: object, key: string) {
  return obj[key];
}
// 报错 string 不能作为key
```

```js
function prop(obj: object, key: string) {
  return (obj as any)[key];
}
// 不友好
```

```js
function prop<T extends object, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}
// 使用泛型, key取值根据对象的key直接来取，会变成具体几个值， 比如： "name" | "age"
```
