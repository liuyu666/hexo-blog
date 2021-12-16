---
title: webpack 基本原理实现
date: 2021-11-27 18:52:09
updated: 2021-11-27 18:52:09
categories: 技术
tags: 工程化
---


1. `@babel/parser`: 分析我们通过 fs.readFileSync 读取的文件内容，返回 AST (抽象语法树)
2. `@babel/traverse`: 可以遍历 AST, 拿到必要的数据
3. `@babel/core`: babel 核心模块，其有个 transformFromAst 方法，可以将 AST 转化为浏览器可以运行的代码
4. `@babel/preset-env`: 将代码转化成 ES5 代码

#### 概念

> **webpack** 是一个用于现代 JavaScript 应用程序的 _静态模块打包工具_。当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，然后将你项目中所需的每一个模块组合成一个或多个 _bundles_，它们均为静态资源，用于展示你的内容。

#### 为什么要这么做

- 那浏览器是怎么执行 js 的？

  - script 引用一个 js 文件，加载到浏览器中会执行这个文件。

- 那如何实现模块化呢？

  - 就需要我们自己实现导入导出的函数 require exports（这个是@babel/core 转换 es6 模块时候将导入导出放在这两个里边）

- 怎么做呢？
  - 我们可以通过 babel 来将代码转为 AST（`@babel/parser`代码变成 AST），理解 AST 是一个对象，将一段代码通过词法分析和语法分析编译之后通过对象描述，AST 的好处是在 AST 我们可以对一些 token 进行遍历(`@babel/traverse`)拿到我们想要的信息和做一些转换处理(`@babel/core` AST 转为代码)
    - 首先将 es6 module 转为 AST
    - 遍历 AST 拿到依赖关系树状图
    - 通过 require 函数拿到 js 代码执行，导出的代码存在 exports 里面

#### 如何去做

引入工具

```js
const fs = require('fs')
const path = require('path')
const parser = require('@babel/parser')
const traverse = require('@babel/traverse').default
// traverse 采用的 ES Module 导出，我们通过 requier 引入的话就加个 .default
const babel = require('@babel/core')
```

es6 module 转为 AST

```js
const buffer = fs.readFileSync(filename, 'utf-8')
const AST = parser.parse(buffer, { sourceType: 'module' }) // 转换es6 module为AST
```

拿到一层的依赖关系对象 dependencies

```js
traverse(AST, {
  // ImportDeclaration 代表通过import导出的token节点
  ImportDeclaration({ node }) {
    // 函数名是 AST 中包含的内容，参数是一些节点，node 表示这些节点下的子内容
    const dirname = path.dirname(filename) // 我们从抽象语法树里面拿到的路径是相对路径，然后我们要处理它，在 bundler.js 中才能正确使用
    const newDirname =
      './' + path.join(dirname, node.source.value).replace('\\', '/') // 将dirname 和 获取到的依赖联合生成绝对路径
    dependencies[node.source.value] = newDirname // 将源路径和新路径以 key-value 的形式存储起来
  },
})
```

转成浏览器可执行的代码 code

```js
const { code } = babel.transformFromAst(AST, null, {
  presets: ['@babel/preset-env'],
})
```

**read 函数 **

```js
/**
 *
 * @param {String} filename
 * @returns {Object} {filename, dependencies, code}
 */
const read = (filename) => {
  const buffer = fs.readFileSync(filename, 'utf-8')
  const AST = parser.parse(buffer, { sourceType: 'module' })

  // 依赖收集
  const dependencies = {}
  traverse(AST, {
    ImportDeclaration({ node }) {
      const dirname = path.dirname(filename)
      const newDirname =
        './' + path.join(dirname, node.source.value).replace('\\', '/')
      dependencies[node.source.value] = newDirname
    },
  })
  // 将抽象语法树转换成浏览器可以运行的代码
  const { code } = babel.transformFromAst(AST, null, {
    presets: ['@babel/preset-env'],
  })
  console.log({
    filename,
    dependencies,
    code,
  })
  return {
    filename,
    dependencies,
    code,
  }
}
```

**递归拿到树级依赖关系**

```js
/**
 *
 * @param {String} entry 文件入口
 * @returns {Object} 树级依赖对象
 */
const makeDependenciesGraph = (entry) => {
  const esModule = read(entry)
  const moduleList = [esModule]
  const graph = {}
  for (let index = 0; index < moduleList.length; index++) {
    const { dependencies } = moduleList[index]
    if (Object.keys(dependencies).length > 0) {
      Object.values(dependencies).forEach((dependence) => {
        moduleList.push(read(dependence))
      })
    }
  }
  moduleList.forEach((module, index) => {
    graph[module.filename] = {
      dependencies: module.dependencies,
      code: module.code,
    }
  })
  return graph
}
/**
 * ./a.js 依赖了 ./b.js 依赖了 ./c.js 无依赖
 * {
 *  './a.js': {
 *    dependencies: {'./b.js': './b.js'},
 *    code: ''
 *  },
 *  './b.js': {
 *    dependencies: {'./c.js': './c.js'},
 *    code: ''
 *  },,
 *  './c.js': {
 *    dependencies: {},
 *    code: ''
 *  },
 * }
 */
```

**代码生成（实现 require 函数， exports 存模块代码）**

```js
/**
 *
 * @param {String} entry 入口文件名
 * @returns {String} 可执行代码
 */
function generateCode(entry) {
  const graph = JSON.stringify(makeDependenciesGraph(entry))

  return `(function(graph){
  function require(module) {
    function localRequire(path) {
      return require(graph[module].dependencies[path])
    }
    const exports = {};
    (function(require, exports, code) {
	  // ! require exports在下边eval代码执行的时候有这个require函数和exports对象，我们要自己实现
      eval(code)
    })(localRequire, exports, graph[module].code);

    return exports
  }
  require('${entry}')
})(${graph})
`
}
```

---

#### 功能点完成，现在看一下示例

_eg: ./a.js 依赖了 ./b.js 依赖了 ./c.js 无依赖_

```js
const result = generateCode('./test1.js')
fs.writeFileSync('app.js', result)
```

app.js

```js
;(function (graph) {
  function require(module) {
    function localRequire(path) {
      return require(graph[module].dependencies[path])
    }
    const exports = {}
    ;(function (require, exports, code) {
      eval(code)
    })(localRequire, exports, graph[module].code)

    return exports
  }
  require('./test1.js')
})({
  './test1.js': {
    dependencies: { './test2.js': './test2.js' },
    code: '"use strict";\n\nvar _test = _interopRequireDefault(require("./test2.js"));\n\nfunction _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }\n\nconsole.log(\'test111\');\n\nvar aa = function aa() {\n  return "9999";\n};\n\nconsole.log(aa());\nconsole.log(_test["default"]);',
  },
  './test2.js': {
    dependencies: { './test3.js': './test3.js' },
    code: '"use strict";\n\nObject.defineProperty(exports, "__esModule", {\n  value: true\n});\nexports["default"] = void 0;\n\nvar _test = _interopRequireDefault(require("./test3.js"));\n\nfunction _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }\n\nconsole.log(\'test3: \', _test["default"]);\nvar _default = 777;\nexports["default"] = _default;',
  },
  './test3.js': {
    dependencies: {},
    code: '"use strict";\n\nObject.defineProperty(exports, "__esModule", {\n  value: true\n});\nexports["default"] = void 0;\nvar _default = \'test3\';\nexports["default"] = _default;',
  },
})
```

浏览器执行结果

![](https://raw.githubusercontent.com/liuyu666/md-img/master/image-20211127184722638.png)
