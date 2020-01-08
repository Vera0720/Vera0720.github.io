---
title: img和父容器之间有间隙的问题
date: 2017-08-15 19:30 
tags: css
---
在前端开发中,经常遇到在一个img外面套div的时候,div的大小和img的大小并不一样,在底部会有一段空白.

代码如下:

    <div>
    
    <img src = ''imgs/1.jpg ''/ >
    
    </div>

原因:img图片默认的vertical-align是baseline.而baseline又和父级底边有一定距离(这个距离和font-size,font-family有关),一个inline-block的元素,如果里面没有inline元素,或者overflow不是visible,则该元素的基线就是其margin底边缘,否则,则基线就是元素里面最后一行内联元素的基线.

方法:

1.将img图片的display:block

2.将div的line-height设置的足够小

3.将容器里的fontsize设置为0

4.将vertical-align设置为middle,bottom,top,只要不取baseline即可

相关:

1.IE的显示有几种模式,假如声明为Strict模式,IE以W3C显示文档,而W3C里img默认inline,除非自己声明block

2.空隙是ie针对盒模型默认的line-height和font-size.给img display:block虽然能解决问题,但并没从结构上考虑问题
