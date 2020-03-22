---
title: css系列之弹性盒模型
date: 2017-03-29 19:34 
tags: css
---
盒模型有两种,一种是W3C标准盒模型,一种是怪异盒模型.

W3C盒模型的宽度和高度,指的是内容的宽度和高度

怪异盒模型的宽度和高度,指的是内容+padding+border

怪异盒模型适用IE6及以前的版本,通过去掉docutype来开启怪异模式

这个问题在前端面试中出现的频率很高,为css3中的重中之重

弹性盒模型应用范围很广,在web移动开发以及布局都有极其重要的地位

<!-- more -->

首先来看下面一段代码

HTML代码如下

    <div id="main">
        <div id="div1">div1</div>
        <div id="div2">div2</div>
        <div id="div3">div3</div>
    </div>

css代码如下

    #div1,#div2,#div3{
        height: 300px;
    }
    #div1{
        background: #ff0000;
    }
    #div2{
        background: #00ff00;
    }
    #div3{
        background: #ffff00;
    }

此时运行的结果显示

![图1](/image/弹性盒模型/1.png) 

然后在css样式加上

    #main{
        display: -webkit-box;
    }

运行结果如图

![图2](/image/弹性盒模型/2.png) 

div是块级元素,没加-webkit-box时,会以块级元素方式排列.当我们在父元素上加-webkit-box时,会变成以上这种方式排列,也可以通过-webkit-box-orient:vertical来实现块级元素排列方式

CSS部分样式改变如下
    
    #main{
        display: -webkit-box;
    }
    #div1,#div2,#div3{
        height: 300px;
        -webkit-box-flex: 1;
    }

运行结果为

![图3](/image/弹性盒模型/3.png) 

当改变浏览器大小时

![图4](/image/弹性盒模型/4.png) 

改变每一个div的-webkit-box-flex的值

    #main{
        display: -webkit-box;
    }
    #div1,#div2,#div3{
        height: 300px;
        /*-webkit-box-flex: 1;*/
    }
    #div1{
        background: #ff0000;
        -webkit-box-flex: 1;
    }
    #div2{
        background: #00ff00;
        -webkit-box-flex: 2;
    }
    #div3{
        background: #ffff00;
        -webkit-box-flex: 3;
    }

运行如图所示

![图5](/image/弹性盒模型/5.png) 

所以,-webkit-box-flex是将父元素动态分成几份,即使改变浏览器的大小,所占份数也不会改变

CSS代码改变如下

    #main{
        display: -webkit-box;
        margin-left: 200px;
    }

运行结果

![图6](/image/弹性盒模型/6.png) 

由此可知,弹性盒模型是将父元素剩余空间分份,将父元素的剩余空间进行动态分布.
