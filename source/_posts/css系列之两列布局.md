---
title: css系列之两列布局
date: 2016-12-07 15:28
tags: css
---
两列布局

两列布局是网页布局中常用的布局方式之一,根据需求确定布局方式.布局成为了面试中出现频率较高的问题.

以下为根据需求的几种实现方式

需求:两列布局左边定宽,右边自适应

<!-- more -->

方法一:

部分代码如下:

HTML部分

    <div id="div1">1</div>
    <div id="div2">2</div>

CSS样式

    #div1{
        width: 100px;
        height: 150px;
        background: red;
    }
    #div2{
        height: 150px;
        background: blue;
    }

因为div是块级元素,所以红色和蓝色并没有在同一水平上,此时想到,可以让红色向左浮动.


虽然运行后我们看到红色和蓝色在同一个水平上,但通过开发者工具可以看到此时的蓝色是充满整个水平方向的,因为红色浮动脱离文档流,空间释放蓝色块挤上去了,所以此时并不是所需要的两列布局效果.解决方法可以给蓝色增加margin-left长度为红色的宽度或者适当的长度

 

方法二:

除了float方式,还有一种为定位法,有人就想到可以给蓝色position:absolute,让它距离浏览器左边合适的宽度,代码如下:

CSS样式部分代码

    #div1{
        width: 100px;
        height: 150px;
        background: red;
    }
    #div2{
        height: 150px;
        background: blue;
        position: absolute;
        left: 200px;
    }

HTML部分代码

    <div id="div1"></div>
    <div id="div2"></div>

现象：蓝色消失

原因:定位后蓝色脱离文档了,它也没有宽度,所以看不见它.解决方法:给蓝色加width:100%和top:0.代码如下

    #div2{
        height: 150px;
        width: 100%;
        background: blue;
        position: absolute;
        left: 100px;
        top: 0;
    }

第一点,因为在不设置top值的时候,默认不是0,而是auto,所以我们应该养成习惯,即使top值不需要的时候,也把它写成0.第二点,蓝色是自适应,因为浏览器的不同我们无法加宽度,但是如果加上宽度100%,就不再是没有宽度了.运行结果如下

 
推荐方法一和方法二,兼容性问题较少,方法三和方法四涉及到css3中的属性,有兼容性问题,不推荐.后续会详细写css3中的弹性盒模型

方法三:

使用flex布局

代码如下:
    
    *{
        margin: 0;
        padding: 0;
    }
    #container {
        display: -webkit-box;
        display: -moz-box;
        display: -ms-box;
        display: -o-box;
        display: box;
    }
    #div1{
        width: 100px;
        height: 150px;
        background: red;
    }
    #div2{
        height: 150px;
        background: blue;
        -webkit-box-flex: 1;
        -moz-box-flex: 1;
        -ms-box-flex: 1;
        -o-box-flex: 1;
        box-flex: 1;
    }


方式四:

通过创建BFC来实现两列布局

代码如下:

    *{
        margin: 0;
        padding: 0;
    }
    #div1{
        width: 100px;
        height: 150px;
        background: red;
        float: left;
    }
    #div2{
        height: 150px;
        background: blue;
        overflow: hidden;
    }


