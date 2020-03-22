---
title: ajax顺序执行
date: 2017-07-23 16:01:08
tags: js
---
循环中的Ajax

在前后端分离的项目中，Ajax是连接前后端的枢纽。

需求：有一个需要循环发起n次的请求，但是n次循环传参不同，我并不知道n是多少，并且要求能够保证返回顺序。JSONP用同步锁无效

<!-- more -->

示例一代码：

    for(var i = 0;i<5;i++){
        $.ajax({
            url : '',
            data : {},
            type:'GET',
            dataType : 'jsonp',
            success: function(res){
                console.log('ok');
            },
            error : function () {
                console.log(i);
            }
        });
    }

这个时候由于url为空，走error方法，我们想要输出12345，但事实控制台输出了5次5。

因为ajax是异步请求，for循环会瞬间执行完，此时ajax还没有正确的返回数据。

实例二代码：

    for(var i = 0;i<5;i++){
        (function (i) {
            $.ajax({
                url : '',
                data : {},
                type:'GET',
                dataType : 'jsonp',
                success: function(res){
                    console.log('ok');
                },
                error : function () {
                    console.log(i);
                }
            })
        })(i)
    }

想利用闭包的方式传值，这时候输出结果012345不定排序，并不是按照我想要的顺序返回，因为我们不能确定哪个AJAX请求是先返回的。

示例三代码：
    
    var currentIndex = 0;
    function a(){
     if(currentIndex >= 5){
         return;
     }
     $.ajax({
         url : '',
         data : {},
         type:'GET',
         dataType : 'jsonp',
         success: function(res){
             alert('ok');
         },
         error : function () {
             currentIndex++;
             console.log(currentIndex);
             a();
         }
     });
    }
    a();

由于我们并不知道会有多少请求发送，所以以递归的形式循环，既能循环n次ajax请求，又能保证数据返回的顺序。
