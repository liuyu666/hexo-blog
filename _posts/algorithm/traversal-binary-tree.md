---
title: 二叉树遍历
date: 2021-12-17 14:37:18
updated: 2021-12-17 14:37:18
categories: 技术
tags: 算法与数据结构
---

### 二叉树遍历方法

- 深度优先遍历
  - 前序遍历
  - 中序遍历
  - 后序遍历
- 广度优先遍历（层次遍历）



![](https://raw.githubusercontent.com/liuyu666/md-img/master/20200302114120701.png)



### 术语定义

> **深度优先搜索算法**（英语：Depth-First-Search，DFS）是一种用于遍历或搜索[树](https://zh.wikipedia.org/wiki/树_(数据结构))或[图](https://zh.wikipedia.org/wiki/图_(数学))的[算法](https://zh.wikipedia.org/wiki/算法)。这个算法会尽可能深的搜索树的分支。

> **广度优先搜索算法**（英语：Breadth-First Search，BFS），又译作**宽度优先搜索**，或**横向优先搜索**，是一种[图形搜索算法](https://zh.wikipedia.org/wiki/搜索算法)。简单的说，BFS是从[根节点](https://zh.wikipedia.org/wiki/树_(数据结构)#术语)开始，沿着树的宽度遍历树的[节点](https://zh.wikipedia.org/wiki/节点)。如果所有节点均被访问，则算法中止。

- D（current node）当前节点
- L （left node）左节点
- R （right node）右节点



**tips**: 记住前中后序遍历的小技巧，在前中后序遍历中都是先L后R，只是D位置不同，D在第一个为DLR，就是前序遍历，D放在中间为是LDR，就是中序遍历，D放在后面为是LRD，就是后序遍历

### 二叉树常见遍历，总结为下图：

| 遍历方式   | 前序遍历                                                     | 中序遍历                                                     | 后序遍历                                                     | 层次遍历                                                     |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 遍历规则   | DLR，即当前结点, 左孩子, 右孩子                              | LDR                                                          | LRD                                                          | 从上到下按层遍历，每层从左到右遍历                           |
| 遍历结果   | ABDGHCEIF                                                    | GDHBAEICF                                                    | GHDBIEFCA                                                    | ABCDEFGHI                                                    |
| 遍历示意图 | ![](https://raw.githubusercontent.com/liuyu666/md-img/master/preOrder.png) | ![](https://raw.githubusercontent.com/liuyu666/md-img/master/inOrder.png) | ![](https://raw.githubusercontent.com/liuyu666/md-img/master/postOrder.png) | ![](https://raw.githubusercontent.com/liuyu666/md-img/master/levelOrder.png) |

### 代码实现：

- 定义树结构：

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */

// eg: 这就是第一张树的js描述
const root = {
  val: 'A',
  left: {
    val: 'B',
    left: {
      val: 'D',
      left: {
        val: 'G',
        left: null,
        right: null
      },
      right: {
        val: 'H',
        left: null,
        right: null
      },
    },
    right: null
  },
  right: {
    val: 'C',
    left: {
      val: 'E',
      left: null,
      right: {
        val: 'I',
        left: null,
        right: null
      },
    },
    right: {
      val: 'F',
      left: null,
      right: null
    },
  }
}

```

- 构建二叉树

```js
// 根据前序遍历数组，构建二叉树
['A', 'B', 'D', 'G', null, null, 'H', null, null, null, 'C', 'E', null, 'I', null, null, 'F', null, null]
let index = 0
function generateTree(pNode, preOrder) {
   if(index >= preOrder.length) return;
   let val = preOrder[index++]
   if(val !== null) {
       pNode = new TreeNode(val)
       generateTree(pNode.left, preOrder)
       generateTree(pNode.right, preOrder)
   } else {
        pNode = null
    }
}
generateTree(preOrder)
```



- #### 深度优先遍历（前序遍历）

```js
// 递归
const preorderTraversal = function(root) {
    // DLR 根左右
    const result = [];
    const preorder = function(node) {
        node.val && result.push(node.val)
        node.left && preorder(node.left)
        node.right && preorder(node.right)
    }
    root && preorder(root)
    return result
}
preorderTraversal(root) // ['A', 'B', 'D', 'G', 'H', 'C', 'E', 'I', 'F']
```

```js
// 非递归 stack
const preorderTraversal = function(root) {
    // DLR 根左右
    const result = [];
    const stack = [];
    let pNode = root;
    while(pNode !== null || stack.length > 0) {
        if(pNode !== null) {
            result.push(pNode.val);
            stack.push(pNode);
            pNode = pNode.left;
        } else {
            const node = stack.pop();
            pNode = node.right;
        }
    }
    return result
}
preorderTraversal(root) // ['A', 'B', 'D', 'G', 'H', 'C', 'E', 'I', 'F']
```

**中序遍历 后序遍历思路类似，就不展开写了**

- #### 广度优先遍历（层次遍历）

```js
const bfsOrder = function(root) {
    if(root===null) return []
    // squeue
    const result = []
    const squeue = [root]
    while(squeue.length > 0) {
        const node = squeue.shift() // 出队列
        result.push(node.val)
        if(node.left) {
            squeue.push(node.left)
        }
        if(node.right) {
            squeue.push(node.right)
        }
    }
    return result
}
preorderTraversal(root) // ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I']
```


