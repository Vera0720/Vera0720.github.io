---
title: gulp基本用法
date: 2017-09-22 10:28:00
tags: gulp
---
这几天简单的研究了一下gulp的用法，gulp对于初学者来说还是很友好的。

官方给出gulp的优点如下：

1.通过代码优于配置的策略，Gulp 让简单的任务简单，复杂的任务可管理。

2.Gulp 严格的插件指南确保插件如你期望的那样简洁高质得工作。

3.利用 Node.js 流的威力，你可以快速构建项目并减少频繁的 IO 操作。

4.通过最少的 API，掌握 Gulp 毫不费力，构建工作尽在掌握：如同一系列流管道。

gulp是前端开发过程中对代码进行构建的工具，是自动化项目的构建利器；她不仅能对网站资源进行优化，而且在开发过程中很多重复的任务能够使用正确的工具自动完成；使用她，我们不仅可以很愉快的编写代码，(虽然在涉及到Sass问题都让我并不愉快)而且大大提高我们的工作效率。gulp是基于Nodejs的自动任务运行器， 她能自动化地完成 javascript/coffee/sass/less/html/image/css 等文件的的测试、检查、合并、压缩、格式化、浏览器自动刷新、部署文件生成，并监听文件在改动后重复指定的这些步骤。在实现上，她借鉴了Unix操作系统的管道（pipe）思想，前一级的输出，直接变成后一级的输入，使得在操作上非常简单。

第一步：gulp的安装配置

1.安装node  确定是否安装了node (确认是否安装 node -v)

2.安装gulp  npm install -g gulp,创建一个工作目录

3.npm init  初始化

4.npm install gulp —save-dev  添加到项目依赖

5.配置gulpfile.js  var gulp = require('gulp');

第二步：创建任务

gulp.task(‘hello’,function(){ … });

第三步：连接多个任务

gulp.task(‘taskName’,[‘task1’,’task2’]);

 

 

Gulp基础知识点

gulp.src()   找到要处理的文件

通配符路径匹配示例：

“src/a.js”：指定具体文件

“*”：匹配所有文件  

  例：src/*.js(包含src下的所有js文件)

“**”：匹配0个或多个子文件夹  

  例：src/**/*.js(包含src的0个或多个子文件夹下的js文件)

“{}”：匹配多个属性   

 例：src/{a,b}.js(包含a.js和b.js文件)

     src/*.{jpg,png,gif}(src下的所有jpg/png/gif文件)

“!”：排除文件    

例：!src/a.js(不包含src下的a.js文件)

gulp.pipe()通过管道去处理文件(在管道里可以去指定功能)

gulp.dest()将处理好的文件放在指定的地方

gulp.watch() 用于监听文件变化，文件一修改就执行指定任务

 

插件使用

1、下载插件，添加到依赖项；

npm install 插件名 --save-dev

2、加载插件   gulp.require('插件名称');

 

 

编译sass：gulp-sass

.pipe(sass({ outputStyle: 'expanded' }))

创建本地服务：gulp-connect

gulp.task('server',function(){
    connect.server({
        root:'dist'

    })
});

实时刷新：liveReload

gulp.task('watch',function(){
    gulp.watch('index.html',['copy-html']);//一旦index.html变化，执行copy-html
    gulp.watch('src/sass/*.scss',['sass']);
});
gulp.task('copy-html', function () {
    gulp.src('index.html')
        .pipe(gulp.dest('dist'))//扔到disk里
        .pipe(connect.reload());//然后重新加载
});
gulp.task('server',function(){
    connect.server({
        root:'dist',
        livereload:true//实时刷新
    })
});

合并文件

gulp.task('concat',function(){
    gulp.src('src/js/*.js').pipe(concat('index.js'))
        .pipe(gulp.dest('dist/js'))
});

最小化：

最小化js:gulp-uglify

最小化css:gulp-minify-css

最小化图片:gulp-imagemin

重命名：

文件重命名gulp-rename

.pipe(rename('index.min.js'))
