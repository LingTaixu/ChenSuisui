---
layout: old
title: Promise-foundation
date: 2022-02-14 15:39:02
tags: 旧
---

## Promise 原理
> MDN The Promise object is used for asynchronous computations
> MDN中文 用于异步计算
> promise 任务队列化 自我理解

### promise 来源
- JavaScript 为检查表单而生 操作dom 获取数据
- 何为同步？ 如果顺序为1234 那么必须按照1234执行且必须等待上一个执行完毕后才能执行

### 微观和宏观
- js的运作流程为从上到下（排除变量提升）例如以下代码
```JavaScript 
console.log(1);

setTimeout(function () {
  console.log(2);
}, 0);

new Promise(resolve => {
  console.log(3);
  resolve();
  console.log(4);
}).then(() => {
  console.log(5);
})

console.log(6);

```
解析：
  1. 第一次打印 `1` 是毋庸置疑的，自上而下的顺序 `但是他遇到了setTimeout`
  2. 因为 `setTimeout` 是一个宏观任务 那么问题又来了 什么是微观任务什么是宏观任务
     1. 宏观任务： 在早期`ECMAScript`规范中`JavaScript`不存在异步的操作模式 JavaScript将代码交给引擎后 引擎只`按顺序`执行代码，这个由`宿主`（`JavaScript`的宿主可以理解为浏览器自身）发起的任务我们称为宏观任务
     2. 微观任务：在`ECMAScript 5` （或称为`ECMAScript 2009`） 之后的规范中出现了异步任务 例如 `promise` `await/async` 此类任务不需要浏览器安排其执行 引擎可以自己发起任务 称之为微观任务
  3. 上述知道微观和宏观之后解释一下执行顺序 当js代码运行从上到下时遇到setTimeout之后会将其移入`宏观任务队列`当进入宏观任务队列让出进程 2022年3月23日11:32:15 中断 刷题去了