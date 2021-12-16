---
title: commonjs es6module 用法
date: 2021-11-29 20:07:14
updated: 2021-11-29 20:07:14
categories: 技术
tags: js
---


### commonjs 

`commonjs` 使用require引入`js`或者`json`文件，通过`module.exports = {}`导出内容，是一个对象

> `CommonJS`定义的模块分为: 模块标识(`module`)、模块定义(`exports`) 、模块引用(`require`)



```js
exports.a = "11" // 将module.exports的属性a赋值为"11"

exports.b = "22"

// 最终相当于

module.exports = {a: "11", b: "22"}

module.exports = {a: 11, b: 22}
```



### es6 module

`es6 module` 使用import引入文件， 通过`export`、`export.default`导出文件

export 只能导出变量定义以及声明

| 导出                                                         | 导入                       | 错误                           |
| ------------------------------------------------------------ | -------------------------- | ------------------------------ |
| export const a = 1 <br />export function aa() {}             | import {a, aa} from 'a.js' |                                |
| ~~const b = 2 <br />export b~~<br />const b = 2<br />export {b} | import {b} from 'b.js'     | 导出错误，要导出完成定义或声明 |
| var c = 10; <br />export default c;<br />等效于<br />export {c as default} | import xx from './c.js'    |                                |



##### as 关键字

```js
// a.js
var a = function() {};
export { a as fun };

// b.js
import { fun as a } from "./a";
a();
```



### 静态 import

- 引入文件不含有`export default`

  ```javascript
  import { a, b, c } from "./a";
  ```

- 引入文件仅含有 `export default`

  ```javascript
  import a from "./d";
  
  //等效于
  import { default as a } from "./d";
  ```

- 引入文件既含有`export default`,还含有其他`export`

  ```javascript
  import $, { each, map } from "jquery";
  ```

### 动态 import-> import()

`import a from "./a";`代码不能出现在代码块中，要动态加载就要用import()函数，返回一个promise对象

```javascript
// 报错
if (x > 2) {
  import a from "./a";
}
///repl: 'import' and 'export' may only appear at the top level (2:2)

// 正常运行
if (condition) {
  import('moduleA').then(...);
} else {
  import('moduleB').then(...);
}
```

参考资料：
[https://segmentfault.com/a/1190000021302446](https://segmentfault.com/a/1190000021302446)
