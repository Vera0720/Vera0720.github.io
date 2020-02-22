---
title: promise
date: 2019-10-20 22:30:56
tags: js
---
在 Promise 出现以前，我们处理一个异步网络请求，大概是疯狂回调嵌套地狱。
回调地狱带来的负面作用有以下几点：
1.代码臃肿
2.可读性差
3.耦合度过高，可维护性差
4.代码复用性差
5.容易滋生 bug
6.只能在回调里处理异常
为了解决这些问题，于是Promise规范诞生了，并且在业界有了很多实现来解决回调地狱的痛点。比如业界著名的Q和bluebird，bluebird甚至号称运行最快的类库。
<!-- more -->

### 定义：Promise对象用于异步操作，它表示一个尚未完成且预计在未来完成的异步操作。

来看看他的常规操作

    new Promise(请求1)
        .then(请求2(请求结果1))
        .then(请求3(请求结果2))
        .then(请求4(请求结果3))
        .then(请求5(请求结果4))
        .catch(处理异常(异常信息))

### 状态
pending：初始值，不是fulfilled，也不是rejected
fulfilled：代表操作成功
rejected：代表操作失败
Promise有两种状态改变的方式，既可以从pending转变为fulfilled，也可以从pending转变为rejected。一旦状态改变，就「凝固」了，会一直保持这个状态，不会再发生变化。当状态发生变化，promise.then绑定的函数就会被调用。
### 基本api
.then 两个参数，成功和失败，会创建并返回一个新的promise,这个promise可以用于实现promise链式流程控制
.catch 失败，相当于.then(null,reject)
.all 多个Promise实例，包装成一个新的Promise实例。接受一个数组作参数，数组中的对象均为promise实例（如果不是一个promise，该项会被用Promise.resolve转换为一个promise)。它的状态由这三个promise实例决定。当p1, p2, p3状态都变为fulfilled，p的状态才会变为fulfilled，并将三个promise返回的结果，按参数的顺序（而不是 resolved的顺序）存入数组，传给p的回调函数。当p1, p2, p3其中之一状态变为rejected，p的状态也会变为rejected，并把第一个被reject的promise的返回值，传给p的回调函数。如果传入一个空数组，会立即完成
.race Promise.race方法同样接受一个数组作参数。当参数中有一个实例的状态发生改变（变为fulfilled或rejected），p的状态就跟着改变。并把第一个改变状态的promise的返回值，传给p回调函数。在第一个promise对象变为resolve后，并不会取消其他promise对象的执行。如果传入一个空数组，会挂住，永远不会决议。
.resolve 让这个Promise对象立即进入resolved状态，并将结果success传递给then指定的onFulfilled回调函数。Promise.resolve()的另一个作用就是将thenable对象（即带有then方法的对象）转换为promise对象。
.reject 这段代码会让这个Promise对象立即进入rejected状态，并将错误对象传递给then指定的onRejected回调函数。
### 优缺点
优点：Promise的真正强大之处在于它的多重链式调用，可以避免层层嵌套回调。
缺点：如果没有使用catch方法指定处理错误的回调函数，Promise对象抛出的错误不会传递到外层代码，即不会有任何反应（Chrome会抛错）；Promise一旦新建就会「立即执行」，无法取消。
### 封装ajax

    function get(url) {
        // 返回一个Promise对象
        return new Promise(function (resolve, reject) {
            // 创建一个XHR对象
            let req = new XMLHttpRequest() || new ActiveXObject('Microsoft.XMLHTTP')
            req.open('GET', url, true)
            // 使用req.onload监听req的状态
            req.onload = function () {
                if (req.readyState == 4 && req.status == 200) {
                    resolve(req.response)
                } else {
                    reject(Error(req.statusText))
                }
            }
            // 网络错误
            req.onerror = function () {
                reject(Error("Network Error"));
            };
            // 发送请求
            req.send();
        });
    }
    get("http://something").then(function (response) {
        console.log(response);
    }).catch(function (error) {
        console.log(error);
    })

### Promise与事件循环
Promise在初始化时，传入的函数是同步执行的，然后注册then回调。注册完之后，继续往下执行同步代码，在这之前，then中回调不会执行。同步代码块执行完毕后，才会在事件循环中检测是否有可用的promise回调，如果有，那么执行，如果没有，继续下一个事件循环。
Promise在事件循环中还有一个微任务的概念
### 常见问题
promise.then(onFulfilled, onRejected)------>在onFulfilled中发生异常的话，在onRejected中是捕获不到这个异常的。
promise.then(onFulfilled).catch(onRejected)------->.then中产生的异常能在.catch中捕获
如果在then中抛错，而没有对错误进行处理（即catch），那么会一直保持reject状态，直到catch了错误
每次调用then都会返回一个新创建的promise对象，而then内部只是返回的数据
在异步回调中抛错，不会被catch到
promise状态变为resove或reject，就凝固了，不会再改变
