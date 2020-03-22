---
title: html系列之文档兼容性
date: 2016-09-07 17:02:08
tags: html
---
为了帮助确保网页在将来的 IE版本中具有一致的外观，IE8 引入了文档兼容性。 文档兼容性是对 IE6中引入的兼容性模式的扩展，使我们可以选择IE用于显示网页的特定呈现模式。

    < meta http-equiv = "X-UA-Compatible" content = "IE=edge,chrome=1" />

这是个是IE8的专用标记,用来指定IE8浏览器去模拟某个特定版本的IE浏览器的渲染方式(比如IE6)，以此来解决部分兼容问题，IE=edge这意味着，会强制浏览器按照最新的标准去渲染。就像在Google’s CDN使用最新版本的JQuery一样，这是按照最新版本，但也可能由于没有固定的版本而破坏你的布局。这样写可以达到的效果是如果安装了GCF，则使用GCF来渲染页面，如果为安装GCF，则使用最高版本的IE内核进行渲染。

例如模拟IE7的具体方式如下：

    < meta http-equiv = "X-UA-Compatible" content = "IE=EmulateIE7" />

但令我好奇的是，此处这个标记后面竟然出现了chrome这样的值，难道IE也可以模拟chrome了?

<!-- more -->

迅速搜索了一下，才明白原来不是微软增强了IE，而是谷歌做了个外挂：Google Chrome Frame(谷歌内嵌浏览器框架GCF)。这个插件可以让用户的IE浏览器外不变，但用户在浏览网页时，实际上使用的是Google Chrome浏览器内核，而且支持IE6、7、8等多个版本的IE浏览器，谷歌这个墙角挖的真给力!

而上文提到的那个meta标记，则是在是安装了GCF后，用来指定页面使用chrome内核来渲染。

GCF下载地址: http://code.google.com/intl/zh-CN/chrome/chromeframe/

安装完成后,如果你想对某个页面使用GCF进行渲染，只需要在该页面的地址前加上 gcf： 即可，例如： gcf:http://cooleep.com

但是如果想要在开发时指定页面默认首先使用GCF进行渲染，如果未安装GCF再使用IE内核进行渲染，该如何进行呢?就是使用这个标记。

标记用法：

1.最基本的用法：在页面的头部加入

    < meta http-equiv = "X-UA-Compatible" content = "chrome=1" >

用以声明当前页面用chrome内核来渲染。

复杂一些的就是本文一开始看到的那中用法：

    < meta http-equiv = "X-UA-Compatible" content = "IE=edge,chrome=1" />

这样写可以达到的效果是如果安装了GCF，则使用GCF来渲染页面，如果为安装GCF，则使用最高版本的IE内核进行渲染。

2.通过修改HTTP头文件的方法来实现让指定的页面使用GCF内核进行渲染：

在HTTP的头文件中加入以下信息：X-UA-Compatible: chrome=1

在Apache服务器中，确保 mod_headers 和 mod_setenvif文件可用，然后在httpd.conf中加入以下配置信息：

    < IfModule mod_setenvif.c>
    
    < IfModule mod_headers.c>
    
    BrowserMatch chromeframe gcf
    
    Header append X-UA-Compatible "chrome=1" env=gcf

在IIS7或者更高版本的服务器中，只需要修改web.config文件,添加如下信息即可即可:

    < configuration >
    
    < system.webServer >
    
    < httpProtocol >
    
    < customHeaders >
    
    < add name = "X-UA-Compatible" value = "chrome=1" />
    
    </ customHeaders >
    
    </ httpProtocol >
    
    </ system.webServer >
    
    </ configuration >

兼容性对于网页设计师来说是非常重要的顾虑。虽然最好是可以建立一个完全不需依赖任何网页浏览器特性或功能的网站，有时候这是不可能实现的。文件兼容性模式便能将网页限制在某个特定版本的IE中。

使用X-UA-Compatible标头来指定你的页面支持的IE版本。使用document.documentMode判定页面的兼容性模式。

要判定网页使用IE8浏览时的文件兼容性模式，使用document object(文档对象)的documentMode功能。例如在IE8的网址列输入下列程式码会显示目前页面的文件模式。

javascript:alert(document.documentMode);
documentMode功能会回传一个数值对应目前页面的文件兼容性模式，举例来说，若网页指定为支持IE8模式，documentMode便会回传值"8"。

在IE6引入的compatMode功能不支持在IE8引入的documentMode功能。目前使用compatMode建立的应用程式还能在IE8中作用，但它们必须更新为使用documentMode。

若你希望使用JavaScript判定一个文件的兼容性模式，引入下面范例的这段程式码可支持旧版本的IE。

    engine = null;
    if (window.navigator.appName == "Microsoft Internet Explorer")
    {
    // This is an IE browser. What mode is the engine in?
    if (document.documentMode) // IE8
    engine = document.documentMode;
    else // IE 5-7
    {
    engine = 5; // Assume quirks mode unless proven otherwise
    if (document.compatMode)
    {
    if (document.compatMode == "CSS1Compat")
    engine = 7; // standards mode
    }
    }
    // the engine variable now contains the document compatibility mode.
    }
选择支持某个特定版本的IE，你可以确保你的页面在未来的浏览器版本中也能有显示的一致性。
