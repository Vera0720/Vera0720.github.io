---
title: 原生js实现input的数据双向绑定
date: 2017-10-06 19:25:10
tags: js
---
最近接触了vue，在谈到vue等等的mvvm框架之前，先了解什么是数据双向绑定以及如何利用原生JS实现数据双向绑定

<!-- more -->

单向数据绑定

指先把模板写好，然后把模板和数据（数据可能来自后台）整合到一起形成HTML代码，然后把这段HTML代码插入到文档流里

缺点：一旦HTML代码生成就没有办法改变，如果有新数据重新传入，就必须重新把模板和数据整合到一起插入到文档流中

数据双向绑定

数据模型和视图之间的双向绑定，用户在视图上的修改会自动同步到数据模型中，同样的，如果数据模型中的值发生变化，也会同步到视图中去。

优点：无需进行类似单向数据绑定的那些CRUD操作。

最经常应用的场景：表单

缺点：应用场景有限

框架：angular js,Vue js

底层实现大概有两种：

1.手动绑定，同时使用dirty check去循环监听。（代表angular js）

2.前端数据劫持，使用define Property,(代表Vue js)

以下是一个简单的小例子，在input框里输入什么，后面就会显示什么

代码如下：

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
    <input type="text" id="aa"/>
    <span id="bb">{{hello}}</span>
    <script>
        var obj = {};
        Object.defineProperty(obj,'hello',{
            set:function(val){
                document.getElementById('bb').innerHTML = val;
                document.getElementById('aa').value = val;
            }
        });
        document.getElementById('aa').onkeyup = function(e){
            obj.hello = e.target.value;
        };
        obj.hello = "";
    
    
    </script>
    </body>
    </html>
