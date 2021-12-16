---
title: css溢出省略
date: 2021-12-09 18:13:15
updated: 2021-12-09 18:13:15
categories: 技术
tags: css
---

### 单行溢出省略

```css
div {
  overflow:hidden,
  text-overflow: ellipsis;
  white-space: nowrap
}

```

### 多行溢出省略

```css
div {
  overflow:hidden,
  text-overflow: ellipsis;
  dispaly: -webkit-box; // 做为弹性伸缩盒子展示
  -webkit-box-orient: vertical; // 行排列方式 --垂直
  -webkit-line-clamp: 2; // 显示的行
}

```
