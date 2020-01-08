---
title: 前端优化之css替代js动画
date: 2017-10-28 23:19:20
tags: css
---
导致JavaScript效率低的两大原因：操作DOM和使用页面动画。通常我们会通过频繁的操作 DOM的CSS来实现视觉上的动画效果，导致js效率低的两个因素都包括在内了在频繁的操作DOM和CSS时，浏览器会不停的执行重排和重绘，在PC版本的浏览器中，因为浏览器可用的内存比较大，用户肉眼几乎看不见页面动画产生的repaint和reflow，所以工程师几乎无需过多的考虑动画带来的性能问题，但在移动设备上可大有不同，移动设备分配给内置浏览器的内存可没有PC版本的浏览器内存可观，目前对CSS3支持最好的莫过于webkit浏览器了，在webkit内核的浏览器，莫过于safari其次是chrome.

用CSS3动画替代JS模拟动画的好处：

1. 不占用JS主线程；

2. 可以利用硬件加速；

3. 浏览器可对动画做优化（元素不可见时不动画减少对FPS影响）

坏处是：

浏览器对渲染的批量异步化处理让动画难以控制，此时可以用如下代码来强制同步。

    $.fn.repaint = function () {
    
        this.each(function (item) {
        
            return item.clientHeight;
        
        }); 
    }

CSS3动画提供了2D和3D以及常规动画属性接口，它可以工作在页面的任何一个元素的任意一个属性，CSS3的动画是利用C语言编写的，它是系统层面的动画

CSS3动画与javascript模拟动画有以下区别：

1.  CSS 3D动画在js中无法实现

CSS3的3D动画是CSS3中非常强大的功能，因为它的工作原理是在一个三维的空间里，因此js是无法模拟出像CSS3那样的3D动画

2. CSS 2D矩阵动画效率高于js利用margin和left,top模拟的矩阵动画

CSS3的2D动画是指是2D矩阵Transform变化，js当然是不能做变形动画的。就拿坐标动画来说，使用CSS3的transform做translateXY动画比js中的position left，position right快了近700mm!而且视觉上也比js动画流畅很多。

3. CSS3其它常规动画属性的效率均低于js模拟的动画

常规动画属性在这里是指：height，width,opacity,border-width,color
每在页面执行一次动画时，都应该带有至少两个事件animationStart和animationEnd有的可能还需要 animationDuration，在手机上我们不推荐animationDuration，本来效率就已经非常低了，尽量不要在动画执行期间还做其它的事件。用js模拟动画，需要开发者编写这些动画事件的接口，以便更好的做下一步的工作，而CSS3动画不需要开发者编写这些事件接口，浏览器本身就已经提供了， 拿webkit内核的浏览器举例，它提供了 webkitTransitionStart,webkitTransitionEnd,webkitAnimationStart,webkitAnimationDuration,webkitAnimationEnd 事件接口，开发者可以很方便的使用这些事件。

采用js动画还是css3动画，需要开发者根据不同的需求做出不同的抉择，但应该遵循一个基本的原则是：如果你需要做2D动画，请勿必使用CSS3的transition或animation
