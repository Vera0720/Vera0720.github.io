---
title: react之setState到底是同步还是异步
date: 2020-02-01 22:06:58
tags: react
---

在我印象中，setState一直是异步的，直到有一天，我被问到什么情况下是同步？什么情况下是异步？批量更新的时候会发生什么？

<!-- more -->

react为了解决跨平台，兼容性问题，自己封装了一套事件机制，代理了原生的事件，像在jsx中常见的onClick、onChange这些都是合成事件。


在 React 的 setState 函数实现中，会根据一个变量 isBatchingUpdates 判断是直接更新 this.state 还是放到队列中回头再说，而 isBatchingUpdates 默认是 false，也就表示 setState 会同步更新 this.state，但是，有一个函数 batchedUpdates，这个函数会把 isBatchingUpdates 修改为 true，而当 React 在调用事件处理函数之前就会调用这个 batchedUpdates，造成的后果，就是由 React 控制的事件处理过程 setState 不会同步更新 this.state。

![1](/image/react/1.jpg) 

由 React 控制的事件处理过程 setState 不会同步更新 this.state！
也就是说，在 React 控制之外的情况， setState 会同步更新 this.state！



