---
title: webpack 基础笔记
date: 2017-10-24 10:52:57
tags:
- 前端
- webpack
- 打包
categories:
- 笔记
- 前端
- webpack
---

入门Webpack，看这篇就够了 [http://www.jianshu.com/p/42e11515c10f#](http://www.jianshu.com/p/42e11515c10f#)
WebPack 简明学习教程 [http://www.jianshu.com/p/b95bbcfc590d ](http://www.jianshu.com/p/b95bbcfc590d )

<!--more-->

- webpack 是模块打包工具。
    - 与Grunt、Gulp的比较
    - Grunt/Gulp是一种优化前端的开发流程工具。
- 安装
    - 全局安装
    
    ```
    npm install -g webpack
    ```

    - 安装到你的项目目录

    ```
    npm install --save-dev webpack
    ```

    - 创建package.json
    
    ```
    npm init
    ```
    
    - 使用不同版本
    ```
    npm install webpack@1.2.x --save-dev
    ```
    
    - 如果想要安装开发工具
    ```
    npm install webpack-dev-server --save-dev
    ```

> package.json文件已经就绪，在本项目中安装Webpack作为依赖包

- 安装Webpack

```
npm install --save-dev webpack
```

- 创建目录
    - app--原始数据和js模块
    - Greeter.js
    - main.js
    - public--打包后的js文件和一个index.html
    - index.html--加载打包后的js文件
- 编写完后，使用webpack编译
```
webpack app/main.js public/bundle.js//手动编译
```

- 通过配置文件使用

配置好webpack.config.js的入口和出口后，就可以通过webpack命令编译文件了

- webpack 的配置
> 每个项目下都必须配置有一个 webpack.config.js ，它的作用如同常规的 gulpfile.js/Gruntfile.js ，就是一个配置项，告诉 webpack 它需要做什么。

eg:
    
```
var webpack = require('webpack');
var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');
module.exports = {
    //插件项
    plugins: [commonsPlugin],
    //页面入口文件配置
    entry: {
        index : './src/js/page/index.js'
    },
    //入口文件输出配置
    output: {
        path: 'dist/js/page',
        filename: '[name].js'
    },
    module: {
        //加载器配置
        loaders: [
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            { test: /\.js$/, loader: 'jsx-loader?harmony' },
            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
        ]
    },
    //其它解决方案配置
    resolve: {
        root: 'E:/github/flux-example/src', //绝对路径
        extensions: ['', '.js', '.json', '.scss'],
        alias: {
            AppStore : 'js/stores/AppStores.js',
            ActionType : 'js/actions/ActionType.js',
            AppAction : 'js/actions/AppAction.js'
        }
    }
};
```

> - plugins 是插件项，这里我们使用了一个 CommonsChunkPlugin的插件，它用于提取多个入口文件的公共脚本部分，然后生成一个 common.js 来方便多页面之间进行复用。
> - entry 是页面入口文件配置，output 是对应输出项配置 （即入口文件最终要生成什么名字的文件、存放到哪里）
> - module.loaders 是最关键的一块配置。它告知 webpack 每一种文件都需要使用什么加载器来处理。 所有加载器需要使用npm来加载
> - 最后是 resolve 配置，配置查找模块的路径和扩展名和别名（方便书写）


#### webpack的简单使用例子
安装webpack后，新建一下文件

entry.js
```
require("!style-loader!css-loader!./style.css");
document.write( require("./content.js") );
```
content.js
```
module.exports = "This content is from content.js."
```
style.css
```
body {
    background: #252626;
    color:#fff;
}
```
index.html
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>webpack demo</title>
</head>
<body>
<script src="bundle.js"></script>
</body>
</html>
```
webpack.config.js
```
module.exports = {
    entry: "./entry.js",
    output: {
        path: __dirname,
        filename: "bundle.js"
    },
    module: {
        loaders: [
            { test: /\.csss$/, loader: "style!css" }
        ]
    }
};
```
运行：
```
npm install css-loader style-loader   # 安装的时候不使用 -g //安装css加载器

$ npm install webpack-dev-server -g
$ webpack-dev-server --progress --colors //安装服务器
```
> 服务器可以自动生成和刷新，修改代码保存后自动更新画面

> 访问 localhost:8080

#### 功能介绍
- 生成Source Maps(使调试更容易)

    提供了一种对应编译文件和源文件的方法，使得编译后的代码可读性更高，也更容易调试。
```
devtool: 'eval-source-map',//配置生成Source Maps，选择合适的选项
```

- 构建本地服务

让你的浏览器监测你都代码的修改，并自动刷新修改后的结果。

在webpack中进行配置之前需要单独安装它作为项目依赖
```
npm install --save-dev webpack-dev-server
```

- Loaders

    Loaders需要单独安装并且需要在webpack.config.js下的modules关键字下进行配置，Loaders的配置选项包括以下几方面：

    - test：一个匹配loaders所处理的文件的拓展名的正则表达式（必须）
    - loader：loader的名称（必须）
    - include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
    - query：为loaders提供额外的设置选项（可选）
    
- Babel:一个编译JavaScript的平台
    - 新的JavaScript标准（ES6，ES7），这些标准目前并未被当前的浏览器完全的支持；
    - 使用基于JavaScript进行了拓展的语言，比如React的JSX
    - 安装
    ```
    // npm一次性安装多个依赖模块，模块之间用空格隔开
    //			核心	loader		es2015		react的jsx
    npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react
    //要使用react前，要安装react
    npm install --save react react-dom
    ```

- css模块
- 
    css-loader 和 style-loader，css-loader使用类似@import 和 url(...)的方法实现 require()的功能,style-loader将所有的计算后的样式加入页面中，二者组合在一起使你能够把样式表嵌入webpack打包后的JS文件中。

    //安装
    ```
    npm install --save-dev style-loader css-loader
    ```

    ```
    {
        test:/\.css$/,
        loader:'style' //感叹号的作用在于使同一文件能够使用不同类型的loader
    }
    ```
    
- css module

- css 预处理器: Less Loader	Sass Loader	Stylus Loader

    使用PostCSS，我们使用PostCSS来为CSS代码自动添加适应不同浏览器的CSS前缀。
    
    首先安装postcss-loader 和 autoprefixer（自动添加前缀的插件）
    ```
    npm install --save-dev postcss-loader autoprefixer
    ```
- 插件（Plugins）
- 
    Loaders和Plugins的差别，loaders是在打包构建过程中用来处理源文件的（JSX，Scss，Less..），一次处理一个；插件并不直接操作单个文件，它直接对整个构建过程起作用。

- HtmlWebpackPlugin

    这个插件的作用是依据一个简单的模板，帮你生成最终的Html5文件

    安装
    ```
    npm install --save-dev html-webpack-plugin
    ```
    
- Hot Module Replacement

    Hot Module Replacement（HMR）也是webpack里很有用的一个插件，它允许你在修改组件代码后，自动刷新实时预览修改后的效果。
    
    安装react-transform-hmr  (使用react的情况下)
    ```
    npm install --save-dev babel-plugin-react-transform react-transform-hmr
    ```

#### 产品阶段的构建
- 优化插件

    OccurenceOrderPlugin :为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID -- 内置插件
    
    UglifyJsPlugin：压缩JS代码 -- 内置插件

    ExtractTextPlugin：分离CSS和JS文件
    
    ```
    npm install --save-dev extract-text-webpack-plugin
    ```

- 缓存

    缓存无处不在，使用缓存的最好方法是保证你的文件名和文件内容是匹配的（内容改变，名称相应改变）

    webpack可以把一个哈希值添加到打包的文件名中，使用方法如下,添加特殊的字符串混合体（[name], [id] and [hash]）到输出文件名前