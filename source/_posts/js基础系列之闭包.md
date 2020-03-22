---
title: js基础系列之闭包
date: 2019-01-03 07:18:06
tags: js
---
概念：能够读取其他函数内部变量的函数。或简单理解为定义在一个函数内部的函数，内部函数持有外部函数内变量的引用。
将内部函数传递到所在的词法作用域以外，它都会持有对原始作用域的引用，无论在何处执行这个函数都会使用闭包。
用途：
    1、读取函数内部的变量
    2、让这些变量的值始终保持在内存中。不会再f1调用后被自动清除。
    3、方便调用上下文的局部变量。利于代码封装。
    4、模块模式，允许定义外部不可见的私有实现细节（变量、函数），同时也可以提供允许从外部访问的公开API
原因：f1是f2的父函数，f2被赋给了一个全局变量，f2始终存在内存中，f2的存在依赖f1，因此f1也始终存在内存中，不会在调用结束后，被垃圾回收机制回收。

<!-- more -->

### 闭包的场景用途

返回值

　　最常用的一种形式是函数作为返回值被返回

    var fun = function(){
        var b = 'local';
        var innerFun = function(){
            return b;
        }
        return innerFun;
    }
    console.log(fun()());

函数赋值

　　一种变形的形式是将内部函数赋值给一个外部变量

    var inner;
    var fun = function(){
        var b = 'local';
        var innerFun = function(){
            return b;
        };
        inner = innerFun;
    };
    fun();
    console.log(inner());

函数参数

　　闭包可以通过函数参数传递函数的形式来实现

    var Inner = function(fn){
        console.log(fn());
    }
    var F = function(){
        var b = 'local';
        var N = function(){
            return b;
        }
        Inner(N);
    }
    F();

IIFE

　　由前面的示例代码可知，函数F()都是在声明后立即被调用，因此可以使用IIFE来替代。但是，要注意的是，这里的Inner()只能使用函数声明语句的形式，而不能使用函数表达式。详细原因移步至此

    function Inner(fn){
        console.log(fn());
    }
    (function(){
        var b = 'local';
        var N = function(){
            return b;
        }
        Inner(N);
    })();

循环赋值

　　在闭包问题上，最常见的一个错误就是循环赋值的错误。关于其错误原因的详细解释移步至此

    function foo(){
    
        var arr = [];
    
        for(var i = 0; i < 2; i++){
    
            arr[i] = function(){
    
                return i;
    
            }
    
        }
    
        return arr;
    
    }
    
    var bar = foo();
    
    console.log(bar[0]());//2
　　正确的写法如下

    function foo(){
    
        var arr = [];
    
        for(var i = 0; i < 2; i++){
    
            arr[i] = (function fn(j){
    
                return function test(){
    
                    return j;
    
                }
    
            })(i);
    
        }
    
        return arr;
    
    }
    
    var bar = foo();
    
    console.log(bar[0]());//0    
g(s)etter

　　我们通过提供getter()和setter()函数来将要操作的变量保存在函数内部，防止其暴露在外部

    var getValue,setValue;
    
    (function(){
    
        var secret = 0;
    
        getValue = function(){
    
            return secret;
    
        }
    
        setValue = function(v){
    
            if(typeof v === 'number'){
    
                secret = v;
    
            }
    
        }
    
    })();
    
    console.log(getValue());//0
    
    setValue(1);
    
    console.log(getValue());//1
迭代器

　　我们经常使用闭包来实现一个累加器

    var add = (function(){
    
        var counter = 0;
    
        return function(){
    
            return ++counter;
    
        }
    
    })();
    
    console.log(add())//1
    
    console.log(add())//2  
     
    
    　　类似地，使用闭包可以很方便的实现一个迭代器
    
    function setup(x){
    
        var i = 0;
    
        return function(){
    
            return x[i++];
    
        }
    
    }
    
    var next = setup(['a','b','c']);
    
    console.log(next());//'a'
    
    console.log(next());//'b'
    
    console.log(next());//'c'

### 闭包的创建过程

一个内嵌函数可以访问外套函数的应用环境，当内嵌函数运行在外套函数的作用域内时，满足这个要求很简单。但是在JavaScript中，函数还可以作为参数和返回值，这时，从内嵌函数的定义到调用它的代码，引用环境发生了改变。如果还要访问原来的引用环境，就必须以某种方式将内嵌函数的引用环境与外套函数的引用环境绑定在一起，这个绑定的过程即为闭包的创建过程。
函数的局部变量存在于函数调用时与执行环境相关联的活动对象中，如果没有闭包的存在，外套函数返回内嵌函数后，外套函数的执行环境会从执行环境栈中移除，返回的内嵌函数所能应用的外套函数的局部变量也将随之消失。

    function createClosure() {
      var i = 1;
      return function() {
        console.log(i)
      }
    }
    var fn = createClosure()
    fn() // 如果没有闭包存在，fn将无法访问createClosure函数的局部变量i
实际上闭包并不只是在函数返回是才创建的，任何闭包都是随同函数定义时一起创建的，有人的地方就有江湖，有函数的地方就有闭包。在JavaScript中，每个函数在定义的时候，都会创建一个与之关联的scope属性，scope总是指向定义函数时所在的环境。我们可以将函数的scope属性看成函数的闭包。
尝试说出以下代码的执行结果：

    function f(fn, x) {           //定义一个全局函数f
      if(x < 1) {
        f(g, 1);
      } else {
        fn();
      }
      function g() {              //定义一个局部函数g
        console.log(x);
      }
    }
    
    function h() {                //定义一个全局函数h
    }
    
    f(h, 0)                       //执行函数f
1.执行函数f
当执行进入f(h, 0)时，JS引擎需要完成以下工作：
首先，创建函数f的执行环境，并加入到执行环境栈的栈顶获得执行权。
然后，创建函数f执行环境的作用域链，初始化为函数f的scope所包含的对象。
接着，创建与函数f执行环境相关联的活动对象，该活动对象包含函数f的形参fn和x、arguments对象，this以及局部定义的函数g。注意，在定义函数g的时候，会为函数g添加一个scope属性，指向函数g定义时所处的环境即函数f的活动对象。此时的ECStack结构如下：

    ECStack = [                            //执行环境栈
      EC(f) = {                            //函数f的执行环境
        [scope]: VO(G),                    //VO是全局变量对象
        AO(f): {                           //创建函数f的活动对象
          fn: function(){...},             //f的形参，全局函数h
          x: 0,                            //f的形参
          g: function(){...},              //定义局部函数g
          g[[scope]]: this                 //为局部函数g添加scope属性，指向函数f的活动对象
        }
        scopeChain:[AO(f), f[[scope]]]
      },
      EC(G) = {
        VO(G):{                            //创建全局变量对象
          ...                              //包含全局对象原有的属性
          f: function(){...},              //定义函数f
          f[[scope]]: this,                //定义f的scope，f[[scope]] == VO(G)
          h: function(){...},              //定义函数h
          h[[scope]]: this                 //定义h的scope，gh[[scope]] == VO(G)
        }
      }
    ];
    
2.第二次执行函数f    
由于x为0，满足x < 1的条件，所以执行进入f(g, 1)，JS引擎需要完成以下工作：
首先，创建函数f的执行环境（该执行环境与进入f(h, 0)时创建的执行环境完全不同），并加入到执行环境栈的栈顶获得执行权（当执行f(h, 0)返回后，f的执行环境就会从栈中被删除，只留下全局执行环境）。
然后，创建函数f执行环境的作用域链，初始化为函数f的scope所包含的对象。
接着，创建与函数f执行环境相关联的活动对象，该活动对象包含函数f的形参fn和x、arguments对象，this以及局部定义的函数g。注意，在定义函数g的时候，会为函数g添加一个scope属性，指向函数g定义时所处的环境即函数f的活动对象。此时的ECStack结构如下：
    
    ECStack = [                            //执行环境栈
      EC(f) = {                            //函数f的执行环境
        [scope]: VO(G),                    //VO是全局变量对象
        AO(f): {                           //创建函数f的活动对象
          fn: function(){...},             //f的形参，局部函数g
          x: 1,                            //f的形参
          g: function(){...},              //定义局部函数g
          g[[scope]]: this                 //为局部函数g添加scope属性，指向函数f的活动对象
        }
        scopeChain:[AO(f), f[[scope]]]
      },
      EC(G) = {
        VO(G):{                            //创建全局变量对象
          ...                              //包含全局对象原有的属性
          f: function(){...},              //定义函数f
          f[[scope]]: this,                //定义f的scope，f[[scope]] == VO(G)
          h: function(){...},              //定义函数h
          h[[scope]]: this                 //定义h的scope，gh[[scope]] == VO(G)
        }
      }
    ];
3.执行函数g
由于x为1，不满足x < 1的条件，所以执行进入fn()，即执行局部函数g，JS引擎需要完成以下工作：
首先，创建函数g的执行环境，并加入到执行环境栈的栈顶获得执行权（当执行f(g, 1)返回后，f的执行环境就会从栈中被删除，只留下全局执行环境）。
然后，创建函数g执行环境的作用域链，初始化为函数g的scope所包含的对象，此时函数g的scope所指向的对象为执行f(h, 0)时所创建的活动对象。
ps:要到创建这个函数的那个作用域中取值——是“创建”，而不是“调用”，切记切记,其实这就是所谓的“静态作用域”。
    
    AO(f): {                           //创建函数f的活动对象
      fn: function(){...},             //f的形参，全局函数h
      x: 0,                            //f的形参
      g: function(){...},              //定义局部函数g
      g[[scope]]: this                 //为局部函数g添加scope属性，指向函数f的活动对象
    }

接着，创建与函数g执行环境相关联的活动对象。
最后，执行console.log(x)，打印0。

函数g执行的时候，第一次进去的f函数上下文已经被销毁了，那函数g是如何获取到scope变量的呢
函数g执行上下文维护了一个作用域链，会指向指向f作用域，作用域链是一个数组，结构如下。
ScopeChain: [ AO, AO(f), VO ],
所以指向关系是当前作用域 --> f作用域--> 全局作用域，即使f被销毁了，但是JavaScript依然会让f.AO（活动对象)活在内存中，依然可以通过作用域链找到它，这就是闭包实现的关键。


