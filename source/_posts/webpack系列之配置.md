---
title: webpack系列之配置
date: 2020-03-14 15:29:59
tags: webpack
---
为什么选择webpack呢？大多数团队会在开发时采用紧跟时代的技术，这些技术基本上是采用"模块化+新语言+新框架"，webpack可以提供一个一站式解决的方案。
不知道你是不是和曾经的我一样，只会在vue里简单用用它自带的，或者是百度出来直接复制。今天让我们一起从0开始了解webpack吧～
本文主要从配置，优化，实战，原理4个方向来梳理。在此感谢吴浩麟先生的书，本文有一部分内容是借鉴吴先生的文章。

<!-- more -->

Webpack 有以下几个核心概念。
Entry：入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
Module：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块。
Chunk：代码块，一个 Chunk 由多个模块组合而成，用于代码合并与分割。
Loader：模块转换器，用于把模块原内容按照需求转换成新内容。
Plugin：扩展插件，在 Webpack 构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要的事情。
Output：输出结果，在 Webpack 经过一系列处理并得出最终想要的代码后输出结果。

Webpack 启动后会从 Entry 里配置的 Module 开始递归解析 Entry 依赖的所有 Module。 每找到一个 Module， 就会根据配置的 Loader 去找出对应的转换规则，对 Module 进行转换后，再解析出当前 Module 依赖的 Module。 这些模块会以 Entry 为单位进行分组，一个 Entry 和其所有依赖的 Module 被分到一个组也就是一个 Chunk。最后 Webpack 会把所有 Chunk 转换成文件输出。 在整个流程中 Webpack 会在恰当的时机执行 Plugin 里定义的逻辑。

## 配置

### Entry
entry是配置模块的入口，webpack会通过这个入口开始查找。所以一定要配啊～

    // 单入口配置
    entry: "./src/app1.js"
    // 多入口数组配置
    entry: ["./src/app1.js","./src/app2.js"]
    // 多入口对象配置
    entry: {
        "app1": "./src/app1.js",
        "app2": "./src/app2.js",
    }
    // path方式配置
    entry: {
        context:path.resolve(__dirname,'app') 
    }
    // 动态入口---同步
    entry: () => {
        return {
            "app1": "./src/app1.js",
            "app2": "./src/app2.js",
        }
    }
    // 动态入口---异步
    entry: () => {
        return new Promise ((resolve) => {
            resolve({
                "app1": "./src/app1.js",
                "app2": "./src/app2.js",
            })
        })
        
    }
    
webpack会为每一个chunk取一个名字，chunk和entry的配置有关，如果entry是一个string/array，就只会生成一个名字为main的chunk，如果entry是一个object，那就会出现多个chunk，这是chunk的名称是object中对应的键的名字。

### Output
output是配置最后如何输出代码的配置项。

    output: {
            path: path.resolve(__dirname, 'dist'), // 输出文件在本地的绝对路径
            filename: '[name].[chunkhash:8].js', // 输出文件的名字，
            chunkFilename: '[name].[chunkhash:8].chunk.js',
            publicPath: '/'
    },
### Module

    module: {
        rules: [
            {
              // 命中 JavaScript 文件
              test: /\.js$/,
              // 用 babel-loader 转换 JavaScript 文件
              // ?cacheDirectory 表示传给 babel-loader 的参数，用于缓存 babel 编译结果加快重新编译速度
              use: ['babel-loader?cacheDirectory'],
              // 只命中src目录里的js文件，加快 Webpack 搜索速度
              include: path.resolve(__dirname, 'src')
            },
            {
              // 命中 SCSS 文件
              test: /\.scss$/,
              // 使用一组 Loader 去处理 SCSS 文件。
              // 处理顺序为从后到前，即先交给 sass-loader 处理，再把结果交给 css-loader 最后再给 style-loader。
              use: ['style-loader', 'css-loader', 'sass-loader'],
              // 排除 node_modules 目录下的文件
              exclude: path.resolve(__dirname, 'node_modules'),
            },
            {
              // 对非文本文件采用 file-loader 加载
              test: /\.(gif|png|jpe?g|eot|woff|ttf|svg|pdf)$/,
              use: ['file-loader'],
            },
        ],
        // 让 Webpack 忽略对部分没采用模块化的文件的递归解析和处理，这样做的好处是能提高构建性能。 原因是一些库例如 jQuery 、ChartJS 它们庞大又没有采用模块化标准，让 Webpack 去解析这些文件耗时又没有意义。
        noParse: /jquery|chartjs/
    }

### Resolve

    resolve:{
      // 通过别名来把原导入路径映射成一个新的导入路径
      alias:{
        components: './src/components/'
      },
      // 有一些第三方模块会针对不同环境提供几分代码,在 package.json 文件有两个入口文件，会根据 mainFields 的配置去决定优先采用那份代码
      mainFields: ['browser', 'main'],
      // 导入语句没带文件后缀时，Webpack 会自动带上后缀后去尝试访问文件是否存在。 resolve.extensions 用于配置在尝试过程中用到的后缀列表，
      extensions: ['.js', '.json'],
      //  Webpack 去哪些目录下寻找第三方模块
      modules:['./src/components','node_modules']
    }

### Plugins
Plugin 用于扩展 Webpack 功能

    plugins: [
        // 所有页面都会用到的公共代码提取到 common 代码块中
        new CommonsChunkPlugin({
          name: 'common',
          chunks: ['a', 'b']
        }),
      ]

### devServer
提高开发效率的 DevServer ，它提供了一些配置项可以改变 DevServer 的默认行为

    devServer:{
        hot:true,
        inline:true,
        // 配置 DevServer HTTP 服务器的文件根目录。 默认情况下为当前执行目录，通常是项目根目录，所有一般情况下你不必设置它，除非你有额外的文件需要被 DevServer 服务。
        contentBase: path.join(__dirname, 'public'),
        // 单页应用要求服务器在针对任何命中的路由时都返回一个对应的 HTML 文件
        historyApiFallback: true
        // 多个单页应用组成根据不同的请求来返回不同的 HTML 文件
        historyApiFallback: {
        // 使用正则匹配命中路由
        rewrites: [
          // /user 开头的都返回 user.html
          { from: /^\/user/, to: '/user.html' },
          { from: /^\/game/, to: '/game.html' },
          // 其它的都返回 index.html
          { from: /./, to: '/index.html' },
        ]
        },
        // 在 HTTP 响应中注入一些 HTTP 响应头
        headers: {
          'X-foo':'bar'
        },
        host:'',
        port:80
        // DevServer 默认使用 HTTP 协议服务，它也能通过 HTTPS 协议服务。 有
        https: true
    }








































