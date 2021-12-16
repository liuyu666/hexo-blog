---
title: word-wrap word-break white-space word-spacing 的区别
categories: 技术
date: 2021-11-21 14:00
updated: 2021-12-08 14:00
tags: css
---

#### word-break

###### mdn 解释：

> The **`word-break`** [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS) property sets whether line breaks appear wherever the text would otherwise overflow its content box.( CSS 属性 `word-break` 指定了怎样在单词内断行。)

首先有以下四个属性值：

| 属性值     | 效果                                                                                                                                                                                                                                         |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| normal     | 默认值，按照空格来判断是不是一个单词结束，一行放不下一个单词会溢出，non-CJK (CJK 指中文/日文/韩文) 文本不会出现溢出 <img src="https://raw.githubusercontent.com/liuyu666/md-img/master/image-20211121190008248.png" style="zoom:33%;" />     |
| break-all  | 四个字非常板正，会打破一个单词<img src="https://raw.githubusercontent.com/liuyu666/md-img/master/image-20211121190041275.png" style="zoom:33%;" />                                                                                           |
| keep-all   | 两个字很刚，不出现空格我就不换行<img src="https://raw.githubusercontent.com/liuyu666/md-img/master/image-20211121190723082.png" style="zoom:33%;" />                                                                                         |
| break-word | 会打破单词，别的跟默认值一样的效果。本行最后一个单词放不下，会到下一行，下一行一个单词放不开会打破这个单词，不是无脑打破<img src="https://raw.githubusercontent.com/liuyu666/md-img/master/image-20211121190911961.png" style="zoom:33%;" /> |

#### word-wrap

原本属于微软的一个私有属性，在 CSS3 现在的文本规范草案中已经被重名为 [`overflow-wrap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow-wrap) 。 word-wrap 现在被当作 overflow-wrap 的 “别名”

###### mdn 解释：

> The **`overflow-wrap`** [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS) property applies to inline elements, setting whether the browser should insert line breaks within an otherwise unbreakable string to prevent text from overflowing its line box.（[CSS](https://developer.mozilla.org/en-US/CSS) 属性 **`overflow-wrap`** 是用来说明当一个不能被分开的字符串太长而不能填充其包裹盒时，为防止其溢出，浏览器是否允许这样的单词中断换行。）

| 属性值     | 效果                                                                                                                                                             |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| normal     | 默认值                                                                                                                                                           |
| break-word | 跟 word-break 对应属性值 break-word 表现一致<img src="https://raw.githubusercontent.com/liuyu666/md-img/master/image-20211121193334272.png" style="zoom:33%;" /> |

#### white-space

###### mdn 解释：

> The **`white-space`** CSS property sets how [white space](https://developer.mozilla.org/en-US/docs/Glossary/Whitespace) inside an element is handled.（**`white-space`** CSS 属性是用来设置如何处理元素中的 [空白 (en-US)](https://developer.mozilla.org/en-US/docs/Glossary/Whitespace)。）

|                | 换行符 | 空格和制表符 | 文字换行 | 行尾空格 |
| :------------- | :----- | :----------- | :------- | -------- |
| `normal`       | 合并   | 合并         | 换行     | 删除     |
| `nowrap`       | 合并   | 合并         | 不换行   | 删除     |
| `pre`          | 保留   | 保留         | 不换行   | 保留     |
| `pre-wrap`     | 保留   | 保留         | 换行     | 挂起     |
| `pre-line`     | 保留   | 合并         | 换行     | 删除     |
| `break-spaces` | 保留   | 保留         | 换行     | 换行     |

#### word-spacing

###### mdn 解释：
> 属性 word-spacing 用于声明标签和单词直接的间距行为。

就是word里边的字间距

**Values**
`normal`
正常的单词间距，由当前字体和/或浏览器定义。
`<length>`
通过指定具体的额外间距来增加字体的单词间距。查看 <length> 了解可用单位。
`<percentage>`
通过指定受影响字符的宽度的百分比的方式来增加的间距。

### 总结

word-wrap、word-break 属性值中的 break-word，会打破一整行放不下一个单词的情况，word-break 属性值 break-all 会打破所有在行末放不下的单词，不会去下一行判断能不能放下，word-spacing完全是另一个概念--字间距

white-space 设置**换行符** **空格和制表符** **文字换行** **行尾空格**表现形式 感觉只需要知道**`white-space: nowrap;`不换行就行**
