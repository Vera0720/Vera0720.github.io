---
title: 重复弹出层问题
date: 2017-02-18 17:08
tags: js
---
弹出层是一种很常见的页面显示部件,利用require js的模块化可以使部分功能组件重用性更高.以弹出层为例,探讨关于this的问题

首先来看这样一段代码

<!-- more -->

Html部分代码

    <body>
        <button id="open">open</button>
        <div class="dialog-container">
            <div class="dialog-mask"></div>
            <div class="dialog-box">
                <div class="dialog-title">
                    <div class="dialog-title-item">弹出层</div>
                    <div class="dialog-title-close">[X]</div>
                </div>
                <div class="dialog-content">content</div>
            </div>
        </div>
        <script src="js/require.js" data-main="js/index"></script>
    </body>

 

点击open按钮会显示弹出层,并且点一下open会有一个弹出层,点击[X]会关闭当前弹窗,简单的css和js代码我不贴出来看了

 

可以看到每当我点击一次open按钮,都会创建一个class=”dialog-container”的div

Js代码示例一:
    
    $(".dialog-title-close").on("click", function(){
        $(".dialog-container").remove();
    });

当我点击关闭按钮时,可以看到是所有class为dialog-container的都被清除,所以和需求不符,要求为当前的对话框关闭.此时我们可以想到this.

Js代码示例二:

利用类及类创建的对象,每一个对象的关闭即可实现需求

封装一个类,步骤省略,关闭部分代码如下所示

    var that = this;
        this.close.on("click", function(){
            that.closeDialog();
        });
    };
    Dialog.prototype.closeDialog = function(){
        this.container.remove();
    };
