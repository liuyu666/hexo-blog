---
title: 正则表达式
date: 2022-02-07 16:20:23
updated: 2022-02-07 16:20:23
categories: 技术
tags: 正则
---

- **大小写字母代表取反**
  
  \d 数字
  
  \D 非数字
  
- **?= 与 ?!**
  
  x(?=y) 当x后面跟着y（即y的正则匹配成功）的时候，匹配成功
  
  x(?!y) 当x后面跟的不是y（即y的正则匹配成功）的时候，匹配成功
  
  ```js
  // 定义
  let reg1 = new RegExp('Kim(?=Yin)');
  let reg2 = new RegExp('Kim(?!Yin)');
  // 测试1
  reg1.test('Kim'); // false
  reg2.test('Kim'); // true
  // 测试2
  reg1.test('KimY'); // false
  reg2.test('KimY'); // true
  // 测试3
  reg1.test('KimYin'); // true
  reg2.test('KimYin'); // false
  // 其他用法
  let reg3 = /Kim(?=Yin)/g;
  'KimYinKimdewKimYin'.replace(reg1, '1')  // '1YinKimdew1Yin'
  ```
  
- \1 \2
  
  `\1` 匹配的是 所获取的第1个()匹配的引用。例如，`(\d)\1` 匹配两个连续数字字符。如`33aa` 中的`33`
  
  `\2` 匹配的是 所获取的第2个()匹配的引用。例如，`(\d)(a)\1` 匹配第一是数字第二是字符a,第三\1必须匹配第一个一样的数字重复一次，也就是被引用一次。如 `9a9` 被匹配，但 `9a8` 不会被匹配，因为第三位的\1必须是9才可以，`(\d)(a)\2` 匹配第一个是一个数字，第二个是a，第三个\2必须是第二组（）中匹配一样的，如，`8aa` 被匹配，但`8ab` ，`7a7` 不会被匹配，第三位必须是第二组字符的复制版，也是就引用第二组正则的匹配内容。
  
  以此类推
  
  ```js
  '33ff4'.match(/(\d)\1/); // 33
  
  '33f3f4'.match(/(\d)([a-zA-Z])\1/); // 3f3
  
  '33ff4'.match(/(\d)([a-zA-Z])\2/); // 3ff
  ```
  
- ?=和?!
  
  要理解?=和?!，首先需要理解前瞻，后顾，负前瞻，负后顾四个概念：
  
  ```js
  // 前瞻：
  exp1(?=exp2) 查找exp2前面的exp1
  // 后顾：
  (?<=exp2)exp1 查找exp2后面的exp1
  // 负前瞻：
  exp1(?!exp2) 查找后面不是exp2的exp1
  // 负后顾：
  (?<!exp2)exp1 查找前面不是exp2的exp1
  ```
  
  举例：
  
  ```js
  "中国人".replace(/(?<=中国)人/, "rr") // 匹配中国人中的人，将其替换为rr，结果为 中国rr
  "法国人".replace(/(?<=中国)人/, "rr") // 结果为 法国人，因为人前面不是中国，所以无法匹配到
  ```
  
  要理解?:则需要理解捕获分组和非捕获分组的概念：
  
  ```js
  ()表示捕获分组，()会把每个分组里的匹配的值保存起来，使用$n(n是一个数字，表示第n个捕获组的内容)
  (?:)表示非捕获分组，和捕获分组唯一的区别在于，非捕获分组匹配的值不会保存起来
  ```
  
  举例：
  
  ```js
  // 数字格式化 1,123,000
  "1234567890".replace(/\B(?=(?:\d{3})+(?!\d))/g,",") // 结果：1,234,567,890，匹配的是后面是3*n个数字的非单词边界(\B)
  ```