---
title: animation、transition、transform、translate
categories: 技术
date: 2021-11-21 14:00
updated: 2021-11-21 14:00
tags: css
---

## animation、transition、transform、translate

CSS 中有几个单词比较容易混淆

| 属性               | 含义                                              |
| ------------------ | ------------------------------------------------- |
| animation（动画）  | 设置动画属性，是一个简写属性，有六个属性          |
| transition（过渡） | 设置元素样式过渡，和 animation 类似，需要事件触发 |
| transform（变形）  | 用于元素平移、旋转、缩放、倾斜操作，跟动画无关    |
| translate（移动）  | transform 的一个属性值，移动                      |

#### transition

过渡，元素从一个样式过渡到另一个样式，是一个值的转变，比如从一个颜色变为另一个颜色，需要一个事件触发，:hover、:focus、:checked、媒体查询或者 JavaScript。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>transition</title>
    <style>
      #box {
        height: 100px;
        width: 100px;
        background: green;
        transition: transform 1s ease-in 1s;
      }

      #box:hover {
        transform: rotate(180deg) scale(0.5, 0.5);
      }
    </style>
  </head>
  <body>
    <div id="box"></div>
  </body>
</html>
```

语法：**transition: property duration timing-function delay;**

| 值                         | 描述                              |
| -------------------------- | --------------------------------- |
| transition-property        | 规定设置过渡效果的 CSS 属性的名称 |
| transition-duration        | 规定完成过渡效果需要多少秒或毫秒  |
| transition-timing-function | 规定速度效果的速度曲线函数        |
| transition-delay           | 定义过渡效果何时开始              |

#### animation

官方介绍是 transition 的扩展，animation 是多个 transition 的叠加，无需触发

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>animation</title>
    <style>
      .box {
        height: 100px;
        width: 100px;
        border: 15px solid black;
        animation: changebox 1s ease-in-out 1s infinite alternate running
          forwards;
      }

      .box:hover {
        animation-play-state: paused;
      }

      @keyframes changebox {
        10% {
          background: red;
        }
        50% {
          width: 80px;
        }
        70% {
          border: 15px solid yellow;
        }
        100% {
          width: 180px;
          height: 180px;
        }
      }
    </style>
  </head>

  <body>
    <div class="box"></div>
  </body>
</html>
```

有 8 个属性值比 transition 多了四个

语法：**animation: name duration timing-function delay iteration-count direction play-state fill-mode;**

| 值              | 描述                                                                                                                                                                                                                                                             |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name            | 用来调用@keyframes 定义好的动画，与@keyframes 定义的动画名称一致                                                                                                                                                                                                 |
| duration        | 指定元素播放动画所持续的时间                                                                                                                                                                                                                                     |
| timing-function | 规定速度效果的速度曲线，是针对每一个小动画所在时间范围的变换速率                                                                                                                                                                                                 |
| delay           | 定义在浏览器开始执行动画之前等待的时间，值整个 animation 执行之前等待的时间                                                                                                                                                                                      |
| iteration-count | 定义动画的播放次数，可选具体次数或者无限(infinite)                                                                                                                                                                                                               |
| direction       | 设置动画播放方向：normal(按时间轴顺序),reverse(时间轴反方向运行),alternate(轮流，即来回往复进行),alternate-reverse(动画先反运行再正方向运行，并持续交替运行)                                                                                                     |
| play-state      | 控制元素动画的播放状态，通过此来控制动画的暂停和继续，两个值：running(继续)，paused(暂停)                                                                                                                                                                        |
| fill-mode       | 控制动画结束后，元素的样式，有四个值：none(回到动画没开始时的状态)，forwards(动画结束后动画停留在结束状态)，backwords(动画回到第一帧的状态)，both(根据 animation-direction 轮流应用 forwards 和 backwards 规则)，注意与 iteration-count 不要冲突(动画执行无限次) |
