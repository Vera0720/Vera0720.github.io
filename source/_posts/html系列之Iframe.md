---
title: Iframe简单介绍
date: 2017-07-19 18:26:10
tags: js
---

Iframe可以用在以下几个场景

1.典型系统结构,左侧是功能树,右侧就是一些常见table或者表单之类的.为了每一个功能,单独分离出来,采用iframe.

2.AJAX上传文件

3.加载别的网站内容,例如广告,网站流量分析.

4.在上传图片时,不用flash实现无刷新

5.跨域访问的时候可以用到iframe,使用iframe请求不同域名下的资源

<!-- more -->

演示iframe的基本用法

iframe的各个属性含义如下：
width插入页的宽；

height插入页的高；

scrolling 是否显示页面滚动条（可选的参数为 auto、yes、no，如果省略这个参数，则默认为auto）；

frameborder边框大小；
注意：URL建议用绝对路径；


如果一个页面里面有框架,点页面里的连接，要求在这个<iframe> 里打开。<iframe name=**  ></iframe>
然后在修改默认打开模式,网页HEAD中加上<a href="URL" mce_href="URL" target=**>或部分连接的目标框架设为（**）.

要插入一个页面。要求只拿中间一部分,控制插入页被框架覆盖的深度 marginwidth和marginheight.控制框架覆盖上部分的深度 vspace

如果把frameborder设为1，效果就像文本框一样

透明的IFRAME的用法,必需IE5.5以上版本才支持

Iframe的优缺点:

1.解决加载缓慢的第三方内容如图标和广告的加载问题

2.Security sandbox

3.并行加载脚本

缺点:

1.iframe会阻塞主页面的onload事件

2.即时内容为空,加载也需要时间

3.没有语意
