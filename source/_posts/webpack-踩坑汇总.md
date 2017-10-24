---
title: webpack 踩坑汇总
date: 2017-10-24 11:20:53
tags:
- 前端
- webpack
categories:
- 笔记
- 前端
- webpack
---


#### css打包问题
- 需要使用插件 extract-text-webpack-plugin，将css打包成单独的css文件
    
    <!--more-->

    ```
    var ExtractTextPlugin = require('extract-text-webpack-plugin');
    
    module.exports = {
        plugins: [
            new ExtractTextPlugin('styles/[name].css')//打包成多个css 
        ],
        module: {
            loaders: [
              {
                test : /\.(scss|css)$/,
                loader: ExtractTextPlugin.extract('style-loader','css!sass')
              }
            ]
        }
    }
    ```

#### 图片打包问题 
> 还是不能打包到指定的文件夹中 >_<
- 在webpack中引入图片需要依赖url-loader加载器
    
    在webpack.config.js文件中配置：
    ```
    module: {
    　　loaders: [
    　　　　{
    　　　　　　test: /\.(png|jpg)$/,
    　　　　　　loader: 'url-loader?limit=8192&name=images/[hash:8].[name].[ext]'
    　　　　}
    　　]
    }
    ```
> loader 后面 limit 字段代表图片打包限制，而是指当图片大小小于限制时会自动转成 base64 码引用。上例中大于8192字节的图片正常打包，小于8192字节的图片以 base64 的方式引用。

> name 字段指定了在打包根目录（output.path）下生成名为 images 的文件夹，并在原图片名前加上8位 hash 值。

- **publicPath**: output.publicPath 表示资源的发布地址，当配置过该属性后，打包文件中所有通过相对路径引用的资源都会被配置的路径所替换。
    ```
    output: {
    　　path: 'dist',
    　　publicPath: '/assets/',
    　　filename: 'bundle.js'
    }
    ```

- 在js中引用图片

    通过模块化的方式引用图片路径，这样引用的图片就可以成功打包进 bundle 文件夹里了
    
    ```
    //js
    var imgUrl = require('./images/bg.jpg'),
        imgTempl = '<img src="'+imgUrl+'" />';
    document.body.innerHTML = imgTempl;
    
    //react
    render() {
        return (<img src={require('./images/bg.jpg')} />);
    }
    ```

- html 中引用图片

    需要引入html-withimg-loader插件
    
    webpack.config.js添加配置
    ```
    module: {
    　　loaders: [
    　　　　{
    　　　　　　test: /\.html$/,
    　　　　　　loader: 'html-withimg-loader'
    　　　　}
    　　]
    }
    ```
    在bundle.js中引入html文件
    ```
    import '../index.html'
    ```
    
#### Babel 在IE的问题
- 使用babel-polyfill在ie中无效，需要在webpack.config.js 中的entry 加入 babel-polyfill，例：
```
entry: {
    bundle: [ 'babel-polyfill', './src/main' ]
}
```

#### 异步组件

- require.ensure

> 以React为例

```js
// 异步的组件
export class Child extends React.Component {
    ...
}
module.exports = Child;
```
```js
// 触发异步组件
import React, {Component} from 'react';
export default class Container extends Component {
    constructor() {
        super();
        this.state = {
            currentComponent: null
        }
    }
    doSomething = () => {
        require.ensure([], (require) => {
            const Comp = require('./Child');
            this.setState({
                currentComponent: <Comp/>
            })
        })
    };
    render() {
        return (
            <div>
                <span onClick={this.doSomething} style={{border: "1px solid #000"}}>点击后，按需加载如下模块</span>
                {this.state.currentComponent}
            </div>
        )
    }
}
```

> webpack.config.js

```js
...
output: {
    ...
    chunkFilename: "[name].js"
}
...
```

#### 分析打包后的文件

- 安装

```js
npm i -D webpack-bundle-analyzer
```

- 使用

```js
var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
...
plugins: [
    new BundleAnalyzerPlugin(),
]
// 然后正常打包即可, 默认在http://127.0.0.1:8888/中打开
```