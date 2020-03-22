---
title: 定位以及z-index
date: 2016-11-04 20:28
tags: css
---
定位

定位用来控制元素的位置

定位的关键字是position,position有4个值,分别是relative,absolute,static,fixed当元素定位以后,元素有4个值可以用,分别是left,right,top,bottom.

我用以下几个例子来演示absolute绝对定位和relative相对定位

<!-- more -->

1.我定义了一个main里面套着三个div,部分代码如下

    #main{
        width: 800px;
        height: 400px;
        background: #ccc;
    }
    #main div{
        width: 200px;
        height: 100px;
        display: inline-block;
    }
    #div1{
        background: red;
    }
    #div2{
        background: blue;
        position: relative;
        left: 50px;
        top: 50px;
    }
    #div3{
        background: green;
    }
    
     
    
    <body>
    
    <div id="main">
    
        <div id="div1">a</div>
    
        <div id="div2">b</div>
    
        <div id="div3">c</div>
    
    </div>
    
    </body>
 

 

2.然后我给div2加了定位,如下

    #div2{
    
        background: blue;
    
        position: relative;
    
        left: 50px;
    
        top: 50px;
    
    }
 

由此可以看出,相对定位relative相对于元素初始位置,并且空间不释放

3.在1的基础上增加div2样式如下

    #div2{
    
        background: blue;
    
        position: absolute;
    
        left: 50px;
    
        top: 50px;
    
    }
 

由此可以看出,absolute相对于body定位,absolute空间释放

同样的 ,我将main用margin-left和margin-top让它向右下方蹿100px,而不用定位,然后将b做absolute定位,代码如下

    #main{
    
        width: 800px;
    
        height: 400px;
    
        background: #ccc;
    
        margin-left: 100px;
    
        margin-top: 100px;
    
    }
    
    #main div{
    
        width: 200px;
    
        height: 100px;
    
        display: inline-block;
    
    }
    
    #div1{
    
        background: red;
    
    }
    
    #div2{
    
        background: blue;
    
        position: absolute;
    
        left: 50px;
    
        top: 50px;
    
    }
    
    #div3{
    
        background: green;
    
    }
 

 

此时可以看出absolute还是相对于body,因为我没有给main定位

4.在1的基础上增加main样式如下

    #main{
    
        width: 800px;
    
        height: 400px;
    
        background: #ccc;
    
        position: relative;
    
        left: 50px;
    
        top: 50px;
    
    }
 

由此可得,当给main定位并且加参数时,会看到main带着三个div往下窜

 

5.在4的基础上,给div2添加样式,如下

    #main{
    
        width: 800px;
    
        height: 400px;
    
        background: #ccc;
    
        position: relative;
    
        left: 50px;
    
        top: 50px;
    
    }
     
    
    #div2{
    
        background: blue;
    
        position: absolute;
    
        left: 50px;
    
        top: 50px;
    
    }
 

空间还会释放,但b不再相对于body,而是相对于main.也就是说,某一个子元素定位并且是绝对定位,这个时候它相对于最近的已定位的祖先元素.

 

 

Static静态定位是默认值,元素出现在正常的流,没有left,right,top,bottom这四个值.

Fixed和absolute的唯一区别是:absolute是根据最近的定位的祖先元素确定自己的位置,而fixed永远根据浏览器确定位置,即使窗口为滚动窗口也不会移动.不占据空间.

 

z-index

    #main{
    
        width: 800px;
    
        height: 400px;
    
        background: #ccc;
    
        position: relative;
    
        left: 100px;
    
        top: 100px;
    
    }
    
    #main div{
    
        width: 200px;
    
        height: 100px;
    
        display: inline-block;
    
    }
    
    #div1{
    
        background: red;
    
    }
    
    #div2{
    
        background: blue;
    
        position: absolute;
    
        left: 50px;
    
        top: 50px;
    
    }
    
    #div3{
    
        background: green;
    
    }
 

 

这段代码以及运行后如上图所示,但是现在我需要a在上面不被b覆盖,我需要改变它的层叠关系,这时候用到了z-index,它用控制定位元素的层级关系.z-index我将它翻译成z轴的索引.当我用z-index的时候,必须给当前的加定位,简单来说就是让谁在上面,就给谁定位然后加z-index.定位元素默认z-index=0.代码示例如下

    #main{
    
        width: 800px;
    
        height: 400px;
    
        background: #ccc;
    
        position: relative;
    
        left: 100px;
    
        top: 100px;
    
    }
    
    #main div{
    
        width: 200px;
    
        height: 100px;
    
        display: inline-block;
    
    }
    
    #div1{
    
        background: red;
    
        position: absolute;
    
        left: 0;
    
        top: 0;
    
        z-index: 1;
    
    }
    
    #div2{
    
        background: blue;
    
        position: absolute;
    
        left: 50px;
    
        top: 50px;
    
    }
    
    #div3{
    
        background: green;
    
    }
 

 

a看起来没动,但z-index值改变,绝对定位元素空间释放,所以c跑到a下面去了.
