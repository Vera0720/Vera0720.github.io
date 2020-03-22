---
title: 'sass,scss入门'
date: 2017-09-02 14:10:14
tags: css
---
最近研究了一下sass和scss,清晰结构化,在某些地方的确强大,所以写了一篇入门教程,大概15分钟左右就能明白基本原理

什么是sass(css预处理器)

Sass官网上是这样描述sass的,sass是一门高于css的元语言,他能用来清晰地,结构化的描述文件样式,有着比普通css功能更加强大的功能,sass能提供更简洁,更优雅的语法,同时提供多种功能来穿件可维护和管理的样式表.

<!-- more -->

SASS 和 SCSS 有什么区别？

文件扩展名不同，Sass 是以“.sass”后缀为扩展名，而 SCSS 是以“.scss”后缀为扩展名

语法书写方式不同，Sass 是以严格的缩进式语法规则来书写，不带大括号({})和分号(;)，而 SCSS 的语法书写和我们的 CSS 语法书写方式非常类似。


命令编译：

sass <要编译的Sass文件路径>/style.scss:<要输出CSS文件路径>/style.css

单文件监听命令

sass --watch style.scss:style.css

GUI工具编译

自动化编译gulp

 

不同样式风格的输出方法

sass --watch style.scss:style.css --style compact

嵌套输出方式 nested

展开输出方式 expanded  

紧凑输出方式 compact

压缩输出方式 compressed

 

$变量

普通变量

默认变量

$color:#ccc;
$color:#000 !default;

特殊变量(一般我们定义的变量都为属性值，可直接使用，但是如果变量作为属性或在某些特殊情况下等则必须要以#{$variables}形式使用。)

1.应用于class和属性

2.应用于复杂的属性值

 

嵌套

选择器嵌套

属性嵌套

伪类嵌套

例:hover

a{
  color: #ff0000;
  &:hover{
    color: green;
  }
}

需要在:hover前加&,不然选择的是a里面的元素hover

 

混合宏 @mixin 引用  @include

1、声明混合宏

@mixin border($color1,$color2){
  border:1px solid $color1;
  border-radius: 5px;
  box-shadow: 4px 5px 3px 5px $color2;
}

2、调用混合宏

.aa{
  width: 200px;
  height: 200px;
  @include border();
}

3、混合宏的参数   传一个不带值的参数  如上图的$color1,$color2

4、混合宏的参数   传带值的参数 (默认值)  可以在$color1,$color2后面加默认值,如($color1:#ccc,$color2:#ddd)

Eg:

@mixin css3($attr,$val){
  -webkit-#{$attr}:$val;
  -moz-#{$attr}:$val;
  -ms-#{$attr}:$val;
  -o-#{$attr}:$val;
  #{$attr}:$val;
}
.bb{
  @include css3(border-radius,10px);
}

 

继承 @extend

.ee{
  width: 200px;
  height: 300px;
  @extend .bb;
}

占位符 %placeholder   @extend

%ff{
  width: 200px;
  height: 200px;
}
.g{
  @extend %ff;
}

只有在引入后占位符的样式才会生效

 

@import  引入另一个scss文件,一般引入公共样式

自定义函数@function

@function add($a,$b){
  @return $a+$b;
}
.k{
  width: add(200px,300px);
}

sass具有运算的特性，可以对数值型的Value(如：数字、颜色、变量等)进行加减乘除四则运算。

 

数据类型Data Type

Sass -i   type-of()

 1. 数字: 如，1、 2、 13、 10px；

 2. 字符串：有引号字符串或无引号字符串，如，”foo"、 'bar'、 baz；

 3. 颜色：如，blue、 #04a3f9、 rgba(255,0,0,0.5)；

 4. 布尔型：如，true、 false；

 5. 空值：如，null；

 6. 值列表：用空格或者逗号分开，如，1.5em 1em 0 2em。

 

函数

 1. 数字函数: abs()、round()、ceil()、floor()

 2. 字符串函数：to-upper-case()、to-lower-case()、str-length()、str-index()

 3. 颜色：rgb()、lighten()、darken()

 4. 值列表：length()、nth()、index()、append()、join()

 

注释

注释对于一名程序员来说，是极其重要，良好的注释能帮助自己或者别人阅读源码。在 Sass 中注释有两种方式：

1、类似 CSS 的注释方式，使用 ”/*  */ ”

2、类似 JavaScript 的注释方式，使用“//”

两者区别，前者会在编译出来的 CSS 显示，后者在编译出来的 CSS 中不会显示，

 

Sass的控制命令

1. @if

三目判断语法为：if($condition, $if_true, $if_false)

2. @for循环

@for $i from <start> through <end>

@for $i from <start> to <end>

3. @while循环

4. @each循环

@each $var in <list>
