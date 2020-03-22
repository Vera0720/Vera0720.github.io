---
title: 面向对象
date: 2017-04-06 21:20:58
tags: js
---
对象有三种:内置对象,DOM对象,自定义对象

那函数是什么类型,先看这样一段代码

<!-- more -->

function fn(){
        alert('hello world');
    }
    
    fn();

先声明,在调用,成功运行

    var fn = function(){
        alert('hello world');
    };
    fn();

先声明,在调用,成功运行

    fn();
    function fn(){
        alert('hello world');
    }

再看这段代码,按照js是从上向下执行的规则,函数先调用再声明是不行的,但这段代码运行后也成功弹出了hello world.

    fn();
    var fn = function(){
        alert('hello world');
    };

报错,提示fn is not a function

原因是

function fn(){}叫函数的声明,var fn = function(){}叫做函数的表达式.函数的表达式必须先声明再调用,但函数的声明特殊在可以先调用再声明,js引擎在解析js代码时会先检测有多少这样的函数,然后把它提前声明

函数是不是对象?我们typeof一下发现是function,那function不是一个对象么?关于函数其实有标准的写法:

    var fn = function(){
    
    alert(hello world);
    
    }

写成var fn = new Function(“alert(hello world);”)

这时的typeof虽然是function 但在js中凡是new出来的都可以算作对象.此时,函数便可以算作一个对象了

对象直接量:

在js中,我们可以写出如下代码

    var o = new Object();
    
    o.name = 'lisi';
    
    o.sayhello = function(){
    
        alert(hello);
    
    }

其实还有一种更为直观的写法,被称作对象直接量

    var o = {
    
        name:'lisi',
    
        sayhello:function(){}
    
    }
    
    o.sayhello();

这种方式更为简单和直观,相当于把一些属性方法都封装到一个对象里,在接下来的编程我将只关心这个对象即可

类:具有相同方法和属性的对象的集合

例:

        function Cat(name,age){
    
            this.name = name,
            
            this.age = age
            
            }
            
            Cat.prototype.eat = function(){
            
            console(aaa);
            
        }
        
        var cat1 = New Cat(‘mimi’,3);
        
        cat1.eat();

将属性封装到类里,将方法封装到原型prototype中

在es6中可以直接用class关键字
