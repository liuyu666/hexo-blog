---
title: validate-npm-package-name 源码注释
date: 2022-02-07 18:01:12
updated: 2022-02-07 18:01:12
categories: 技术
tags: 源码
---

```js
'use strict'

/* 
正则去除ASCII码 除了 @ / 之间的两段字符串
?: 代表此括号匹配的不存起来，所以是三个括号，匹配出两部分，@vue/cli 会匹配到vue和cli
*/
var scopedPackagePattern = new RegExp('^(?:@([^/]+?)[/])?([^/]+?)$')
var builtins = require('builtins')
// 不能跟这些重名
var blacklist = [
  'node_modules',
  'favicon.ico'
]

// 抛出一个校验函数，return一个done
var validate = module.exports = function (name) {
  var warnings = []
  var errors = []

  if (name === null) {
    errors.push('name cannot be null')
    return done(warnings, errors)
  }

  if (name === undefined) {
    errors.push('name cannot be undefined')
    return done(warnings, errors)
  }

  if (typeof name !== 'string') {
    errors.push('name must be a string')
    return done(warnings, errors)
  }

  if (!name.length) {
    errors.push('name length must be greater than zero')
  }

  if (name.match(/^\./)) {
    errors.push('name cannot start with a period')
  }

  if (name.match(/^_/)) {
    errors.push('name cannot start with an underscore')
  }

  if (name.trim() !== name) {
    errors.push('name cannot contain leading or trailing spaces')
  }

  // 不能用黑名单里边的名称
  blacklist.forEach(function (blacklistedName) {
    if (name.toLowerCase() === blacklistedName) {
      errors.push(blacklistedName + ' is a blacklisted name')
    }
  })
  // 内置模块不能重名
  builtins.forEach(function (builtin) {
    if (name.toLowerCase() === builtin) {
      warnings.push(builtin + ' is a core module name')
    }
  })

  // really-long-package-names-------------------------------such--length-----many---wow
  // the thisisareallyreallylongpackagenameitshouldpublishdowenowhavealimittothelengthofpackagenames-poch.
  if (name.length > 214) {
    warnings.push('name can no longer contain more than 214 characters')
  }

  // 不能有大写字母
  if (name.toLowerCase() !== name) {
    warnings.push('name can no longer contain capital letters')
  }

  if (/[~'!()*]/.test(name.split('/').slice(-1)[0])) {
    warnings.push('name can no longer contain special characters ("~\'!()*")')
  }
	// 含有可转义字符，拿出 @ / 之间的字符，再进行转义，没有就通过
  if (encodeURIComponent(name) !== name) {
    // Maybe it's a scoped package name, like @user/package
    var nameMatch = name.match(scopedPackagePattern)
    if (nameMatch) {
      var user = nameMatch[1]
      var pkg = nameMatch[2]
      if (encodeURIComponent(user) === user && encodeURIComponent(pkg) === pkg) {
        return done(warnings, errors)
      }
    }

    errors.push('name can only contain URL-friendly characters')
  }

  return done(warnings, errors)
}

// 包名正则可自定义，使用者可修改引用
validate.scopedPackagePattern = scopedPackagePattern

// return 一个描述对象
var done = function (warnings, errors) {
  var result = {
    validForNewPackages: errors.length === 0 && warnings.length === 0,
    validForOldPackages: errors.length === 0,
    warnings: warnings,
    errors: errors
  }
  if (!result.warnings.length) delete result.warnings
  if (!result.errors.length) delete result.errors
  return result
}
```

实现比较简单，就是判断不符合包名规则返回一个描述对象

学到有几个知识点：

- 正则用到 `()`分组匹配保存 加`?:`不保存匹配内容
- 转义字符`api`: `encodeURIComponent` 、 `decodeURIComponent`