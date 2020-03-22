---
title: 模块化之requirejs
date: 2019-02-28 10:54:14
tags: JavaScript
categories: JavaScript
---
## 前言
程序员最怕两件事，第一件事是产品改需求，第二件事是接手的代码乱七八糟结果最后发现是自己以前写的。
很不幸，我正在经历第二件事。
本文旨在让你对模块机制有简单了解以及对require js的核心原理的介绍。
<!-- more -->
## 什么是模块
模块的实质就是业务逻辑的低耦合高内聚，一个模块独立实现一个功能不依赖其他模块，这就是低耦合高内聚。而不是所有功能代码堆叠在一起，牵一发而动全身。模块最重要的是你使用它时仅导入导出你所需要的绑定。

引入模块和引入脚本是有区别的，模块可以理解为按需加载，后者更多是一次性引入全部不管你有没有用，例如引入JQuery。

## 模块编程的几种方式

### 原始写法

    function a() {
        //......
    }
    function b() {
    	//......
    }
  >  这种做法的缺点很明显："污染"了全局变量，无法保证不与其他模块发生变量名冲突，而且模块成员之间看不出直接关系。

### 对象写法
   
    var obj = new Object({
        num:0,
    	fn1 : function (){
        	//...
    	},
    	fn2 : function (){
        	//...
    	}
	});
	obj.num = 10
>  这种方法会有一个明显的缺点，就是内部的变量会被外部改变。举个栗子，你定义了一个a=10,但我在我的代码中把a改成了100，但其实模块里的a应该一直是10，不应该被外部所改变。
      
### 立即执行函数写法
  
    var immediately = (function(){
        var num = 0;
    	var func1 = function(){
        	//...
    	};
    	var func2 = function(){
        	//...
    	};
    	return {
        	func1 : func1,
        	func2 : func2
    	};
	})();
	console.log(immediately.num)
> 这种特点也很明显，就是外部无法读取内部的变量,虽然模块不应该被改变变量，但好歹获取得让我获取一下啊。

### 输入全局变量写法

    var  globalVariable= (function ($,y) {
           //...
    })(JQuery,yhooh);
> 输入全局变量写法,为了在模块内部调用全局变量，必须将其他变量输入模块,就把这两个库（也是两个模块）当作参数.这样做除了保证模块的独立性，还使得模块之间的依赖关系变得明显。

## Common js的require和AMD的require

* Commonjs是同步加载，在服务器端并不是问题，因为服务器端的文件都是存在本地硬盘里可以同步加载，等待时间就是硬盘读取时间，但是浏览器端不同，时间取决于网速，很可能造成浏览器假死。
* AMD规范下的require接受两个参数，异步加载所需模块，不阻塞进程，只有依赖模块加载完毕后才会执行回调方法。

## require js优点
* 实现js文件的异步加载，避免网页失去响应
* 管理模块之间的依赖性

## Require的原理

* Require作为程序的入口，调度javascript资源，加载到各个defined模块时，各个模块就悄无声息的动态创建script标签加载文件，加载结束后往require队列里报告自己结束了，require中所有以来的模块都结束了，就执行回调函数。
* 举个栗子：马上开学了，小明的寒假作业还有很多没写（需要依赖的模块很多），如果让他自己写（按照顺序加载），可能会写不完（等待时间过长造成浏览器卡死），小明找了几个自己的好朋友帮自己一起写（异步执行加载模块操作），每个小伙伴领了自己的任务开始写作业（创建script标签放到html的head头里），每一个人写完小明给他们的作业了就告诉小明，我写完了（每一个模块向队列报告加载完成），小明把作业都放进自己的书包里（记录模块加载完成的个数长度），当所有人的任务都完成，小明就开开心心上学了（所有模块依赖完成后，执行回调代码）

一起来看看我写的一个require源码中核心思想，源码太多了，就讲讲原理吧

index.html

	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>Title</title>
	</head>
	<body>
	<script src="myRequire.js"></script>
	<script>
	    require(['defined1.js'], function(def) {
	        def.define.sayHello();
	    });
	</script>
	</body>
	</html>
>	这里require就是myRequire.js里的方法，require接受两个参数，第一个参数是个数组，也就是所有依赖的模块名，第二个参数是加载完这些模块后执行的回调函数.def1和def2是这两个模块定义的时候导出的对象

defined1.js

	exports.define = {
	    topic: '老王',
	    desc: '说了声Hello',
	    sayHello: function() {
	        console.log( this.topic + this.desc);
	    }
	}
>	这其实就是一个简单的模块---defined1模块,exports.define包裹的部分也就是导出的内容，也就是之前提到过的def1

myRequire.js
		
	//标记已经加载成功的个数
	let successNum = 0;
	//模块导出
	window.exports = {};
	//记录各个模块的顺序
	let moduleOrder = [];
	
	//判断是否数组
	function isArray(param) {
	    return param instanceof Array;
	}
	
	//require 真正实现
	function require(arr, callback) {
	    let req_list;
	
	    // 转化成数组。（arr就是小明的朋友们）
	    if(isArray(arr)) {
	        req_list = arr;
	    } else {
	        req_list = [arr];
	    }
	
	    let req_len = req_list.length;
	
	    //模块逐个加载（小明的朋友们都到齐了）
	    for(let i=0;i<req_len;i++) {
	        let req_item = req_list[i];
	        // 每一个都创建script标签（每一个小伙伴都领了一部分要帮小明写的作业）
	        let $script = createScript(req_item, i);
			//html里的head节点
			let $node = document.querySelector('head');
			
	        (function($script) {
		        //将每个script标签加入扫head头里（每个小伙伴正在写作业）
	            $node.appendChild($script);
	            //检测script 的onload事件(判断小明的朋友们是否写完作业)
	            $script.onload = function() {
	
	                // 将加载完成后的模块按照顺序塞到一个数组里（写完作业的朋友们将作业给了小明，小明记录了已经写完的作业数量）
	                successNum++;
	
	                let script_index = $script.getAttribute('index');
					//这是模块加载完成顺序数组（小明按照顺序将写好的作业排好）
	                moduleOrder[script_index] = exports;
	
	                window.exports = {};
	
	                //所有的模块都加载成功后，执行callback（所有的小伙伴都写完了作业，小明收好了所有的作业，去上学了）
	                if(successNum == req_len) {
	
	                    callback && callback.apply(exports, moduleOrder);
	                }
	
	            }
	          
	        })($script);
	
	    }
	
	}
	
	//创建一个script标签（小伙伴们领到任务开始写作业）
	function createScript(src, index) {
	    let $script = document.createElement('script');
	
	    $script.setAttribute('src', src);
	    $script.setAttribute('index', index);
	
	    return $script;
	}
>	回想上面提过的require原理

## Es6模块化export和import
### Es6模块化的基本特点：
*  基于文件的模块化，通俗的说就是一个文件一个模块
*  模块的API是静态的，需要在模块公开API中静态定义所有最高层导出，后期无法补充
*  单例模式，模块只有一个实例，其中维护了他的状态，每次被导入时，是对单个中心实例的引用，如果想要多个模块实例，模块需要提供某种工厂方法来实现这一点。
*  模块公开的API中暴露的属性和方法并不仅仅是普通的值或者引用赋值，他们是到内部模块定义中的标识符的实际绑定，几乎类似于指针。每一个模块内声明的变量都是局部变量，不会污染全局作用域。

### Export和export default
*  都可以用来导出常量，函数，模块，文件等等
*  都可以通过import引入
*  Export可以有很多，但export default只有一个
*  Export导出后面要用{ },export default不用
*  Import export default 输出的模块时，可以随便起名，但Import export输出的模块时，只能用export导出时定义的名字

以上就是所有的内容啦，BIU～


