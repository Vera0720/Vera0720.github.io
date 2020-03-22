---
title: css系列之inline-block间隙
date: 2016-09-02 14:09:06
tags: css
---
一、现象描述

真正意义上的inline-block水平呈现的元素间，换行显示或空格分隔的情况下会有间距

<!-- more -->

二、方法之移除空格

元素间留白间距出现的原因就是标签段之间的空格，因此，去掉HTML中的空格，自然间距就木有了。考虑到代码可读性，显然连成一行的写法是不可取的，我们可以：

    <div class="space">
    
        <a href="##">
    
        惆怅</a><a href="##">
    
        淡定</a><a href="##">
    
        热血</a>
    
    </div>

或者是：

    <div class="space">
    
        <a href="##">惆怅</a
    
        ><a href="##">淡定</a
    
        ><a href="##">热血</a>
    
    </div>

或者是借助HTML注释：

    <div class="space">
    
        <a href="##">惆怅</a><!--
    
        --><a href="##">淡定</a><!--
    
        --><a href="##">热血</a>
    
    </div>

三、使用margin负值

    .space a {
    
        display: inline-block;
    
        margin-right: -3px;
    
    }

margin负值的大小与上下文的字体和文字大小相关

例如，对于12像素大小的上下文，Arial字体的margin负值为-3像素，Tahoma和Verdana就是-4像素，而Geneva为-6像素。

由于外部环境的不确定性，以及最后一个元素多出的父margin值等问题，这个方法不适合大规模使用。

四、让闭合标签吃胶囊

如下处理：

    <div class="space">
    
        <a href="##">惆怅
    
        <a href="##">淡定
    
        <a href="##">热血</a>
    
    </div>

注意，为了向下兼容IE6/IE7浏览器，最后一个列表的标签的结束（闭合）标签不能丢。

在HTML5中，我们直接：

    <div class="space">
    
        <a href="##">惆怅
    
        <a href="##">淡定
    
        <a href="##">热血
    
    </div>

好吧，虽然感觉上有点怪怪的，但是，这是OK的。

五、使用font-size:0

类似下面的代码：
    
    .space {
    
        font-size: 0;
    
    }
    
    .space a {
    
        font-size: 12px;
    
    }

这个方法，基本上可以解决大部分浏览器下inline-block元素之间的间距(IE7等浏览器有时候会有1像素的间距)。不过有个浏览器，就是Chrome, 其默认有最小字体大小限制，因为，考虑到兼容性，我们还需要添加：
类似下面的代码：

    .space {
    
        font-size: 0;
    
        -webkit-text-size-adjust:none;
    
    }

补充：目前Chrome浏览器已经取消了最小字体限制。因此，上面的-webkit-text-size-adjust:none;代码估计时日不多了。

六、使用letter-spacing

类似下面的代码：

    .space {
    
        letter-spacing: -3px;
    
    }
    
    .space a {
    
        letter-spacing: 0;
    
    }

根据我去年的测试，该方法可以搞定基本上所有浏览器，不过Opera浏览器下有蛋疼的问题：最小间距1像素，然后，letter-spacing再小就还原了。

七、使用word-spacing

类似下面代码：

    .space {
    
        word-spacing: -6px;
    
    }
    
    .space a {
    
        word-spacing: 0;
    
    }

一个是字符间距(letter-spacing)一个是单词间距(word-spacing)，大同小异。据我测试，word-spacing的负值只要大到一定程度，其兼容性上的差异就可以被忽略。因为，貌似，word-spacing即使负值很大，也不会发生重叠。如果您使用Chrome浏览器，可能看到的是间距依旧存在。确实是有该问题，原因我是不清楚，不过我知道，可以添加display: table;或display:inline-table;

    .space {
    
        display: inline-table;
    
        word-spacing: -6px;
    
    }
