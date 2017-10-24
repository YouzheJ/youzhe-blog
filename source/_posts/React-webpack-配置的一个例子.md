---
title: React + webpack 配置的一个例子
date: 2017-10-24 11:19:06
tags:
- 前端
- webpack
- React
categories:
- 笔记
- 前端
- webpack
---

#### devServer.js 参考例子：

<!--more-->

```
var path = require('path');
var express = require('express');
var webpack = require('webpack');
var config = require('./webpack.config.dev.js')
// var proxy = require('proxy-middleware');
var proxy = require('http-proxy-middleware'); //服务器代理


var app = express();
var compiler = webpack(config);

app.use(require('webpack-dev-middleware')(compiler, {
  noInfo: true,
  publicPath: config.output.publicPath
}));

app.use(express.static(path.join(__dirname, '/src')));

app.use(require('webpack-hot-middleware')(compiler));

app.use('/zwd', proxy({target: 'http://tools.17zwd.com/', changeOrigin: true}));

// app.get('/zwd/*', function(req, res) {
//   // res.send('https://m.tools.17zwd.com/al/' + req.params[0])
//   require('http://tools.17zwd.com/zwd/' + req.params[0])
// });

app.get('/', function(req, res) {
  res.sendFile(path.join(__dirname, 'publish/publish.html'));
});
app.get('/upload', function(req, res) {
  res.sendFile(path.join(__dirname, 'publish/upload.html'));
});
app.get('/category', function(req, res) {
  res.sendFile(path.join(__dirname, 'publish/category.html'));
});

// app.use('/api', proxy({target:'http://www.example.org', changeOrigin:true}));

app.listen(3000,  function(err) {
  if (err) {
    console.log(err);
    return;
  }
  console.log('Listening at http://localhost:3000');
});


```

#### package.json 参考例子：
```
{
  "name": "react-transform-boilerplate",
  "version": "1.0.0",
  "description": "A new Webpack boilerplate with hot reloading React components, and error handling on module and component level.",
  "scripts": {
    "clean": "rimraf dist",
    "build:webpack": "cross-env NODE_ENV=production webpack --config webpack.config.prod.js",
    "build": "npm run clean && npm run build:webpack",
    "start": "node devServer.js",
    "lint": "eslint src"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/gaearon/react-transform-boilerplate.git"
  },
  "keywords": [
    "react",
    "reactjs",
    "boilerplate",
    "hot",
    "reload",
    "hmr",
    "live",
    "edit",
    "webpack",
    "babel",
    "react-transform"
  ],
  "author": "Dan Abramov <dan.abramov@me.com> (http://github.com/gaearon)",
  "license": "CC0-1.0",
  "bugs": {
    "url": "https://github.com/gaearon/react-transform-boilerplate/issues"
  },
  "homepage": "https://github.com/gaearon/react-transform-boilerplate",
  "devDependencies": {
    "animate.css": "^3.5.2",
    "babel-core": "^6.6.5",
    "babel-eslint": "^5.0.0-beta4",
    "babel-loader": "^6.2.4",
    "babel-plugin-antd": "^0.5.1",
    "babel-plugin-transform-runtime": "^6.15.0",
    "babel-preset-es2015": "^6.3.13",
    "babel-preset-react": "^6.3.13",
    "babel-preset-react-hmre": "^1.1.1",
    "babel-preset-stage-0": "^6.16.0",
    "babel-runtime": "^6.18.0",
    "cross-env": "^1.0.7",
    "css-loader": "^0.23.0",
    "eslint": "^1.10.3",
    "eslint-plugin-babel": "^3.0.0",
    "eslint-plugin-react": "^3.11.3",
    "eventsource-polyfill": "^0.9.6",
    "express": "^4.13.3",
    "extract-text-webpack-plugin": "^1.0.1",
    "file-loader": "^0.9.0",
    "font-awesome": "^4.7.0",
    "foundation-apps": "^1.2.0",
    "highcharts": "^v5.0.2",
    "http-proxy": "^1.16.2",
    "less": "^2.7.1",
    "less-loader": "^2.2.3",
    "node-sass": "^3.13.1",
    "react-slick": "^0.14.5",
    "rimraf": "^2.4.3",
    "sass-loader": "^3.1.2",
    "slick-carousel": "^1.6.0",
    "style-loader": "^0.13.0",
    "url-loader": "^0.5.7",
    "webpack": "^1.12.9",
    "webpack-dev-middleware": "^1.4.0",
    "webpack-dev-server": "^1.16.2",
    "webpack-hot-middleware": "^2.9.1",
    "whatwg-fetch": "^1.0.0"
  },
  "dependencies": {
    "antd": "^1.3.0",
    "react": "^15.0.2",
    "react-dom": "^15.0.2",
    "react-redux": "^4.0.0",
    "react-router": "^2.4.1",
    "react-slick": "^0.14.5",
    "redux": "^3.0.0",
    "redux-immutable": "^3.0.8",
    "html-withimg-loader": "0.1.16"
  }
}

```

#### 开发环境的webpack.config.js 参考例子：
```
var path = require('path');
var webpack = require('webpack');
var ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  // or devtool: 'eval' to debug issues with compiled output:
  devtool: 'cheap-module-eval-source-map',
  entry: {
    vender: ['whatwg-fetch', 'eventsource-polyfill', 'webpack-hot-middleware/client'], // 额外插件打包成vender
    publish: './src/publish',
    category: './src/category',
    upload: './src/upload',
    // template: './src/containers/template',
  },
  output: {
    path: path.join(__dirname, 'static'),
    filename: 'scripts/[name].js',
    publicPath: '/static/'
  },
  babel: {
    presets: [ 'react', 'es2015', 'stage-0'],
    plugins: ['transform-runtime']
    //plugins: ['transform-runtime', ["antd",  { "style": "css" }]]
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NoErrorsPlugin(),
    new ExtractTextPlugin('static/styles/styles.css')
  ],
  resolve: {
      extensions: ['', '.js', '.jsx']
  },
  module: {
    loaders: [
      {
        test: /\.js|jsx?$/,
        loaders: ['babel?sourceMap'],
        include: path.join(__dirname, 'src')
      },
      {   
        test: /\.css$/, 
        loader: 'style-loader!css-loader?sourceMap' 
      },
      {   
        test: /\.scss$/,
        loader: 'style!css!sass?sourceMap'
        //loader: ExtractTextPlugin.extract("style-loader", "css!sass", 'css?modules&importLoaders=1&localIdentName=[name]__[local]__[hash:base64:8]')
      },
      {   
        test: /\.less$/, 
        loader: 'style!css!less?sourceMap'
      },
      { test: /\.(png|jpg|gif)$/, loader: 'file-loader' },
      { 
        test: /\.(woff|svg|eot|ttf)\??.*$/,
        loader: 'url-loader?limit=50000&name=[path][name].[ext]'
      }
    ]
  }
};

```

#### 生产环境的webpack.config.js 参考例子:
```
var path = require('path');
var webpack = require('webpack');
var ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  //devtool: 'source-map',
  entry: {
    publish: './src/publish',
    upload: './src/upload',
    category: './src/category',
    vendors: ['react','whatwg-fetch', 'react-dom']
  },
  output: {
    path: path.join(__dirname, 'static/'),
    filename: 'scripts/[name].js?v=[hash]',
    publicPath: ''
    //chunkFilename: "[name].min.js" 按需打包
  },
  babel: {
    presets: ['es2015', 'stage-0', 'react'],
    plugins: ['transform-runtime']
  },
  plugins: [
    new webpack.optimize.OccurrenceOrderPlugin(),
    new webpack.DefinePlugin({
      'process.env': {
        'NODE_ENV': JSON.stringify('production')
      }
    }),
    new webpack.optimize.UglifyJsPlugin({
      compressor: {
        warnings: false
      }
    }),
    new AssetsPlugin({ // 生成一个asset.js的文件用于存放版本号
    /* 在 html/jsp 等页面中使用
    <script>
      document.write('<script src="static/asset.js?v=' + Math.random() + '"><\/script>');
    </script>
    <script>
      document.write('<script src="static/' + window.WEBPACK_ASSETS['vendors'].js + '"><\/script>');
      document.write('<script src="static/' + window.WEBPACK_ASSETS['category'].js + '"><\/script>');
    </script>
    */
      filename: 'static/asset.js',
      processOutput: function (assets) {
        return 'window.WEBPACK_ASSETS = ' + JSON.stringify(assets);
      }
    }),
    new ExtractTextPlugin('styles/[name].css?v=[hash]'),
    new webpack.optimize.CommonsChunkPlugin('vendors', 'scripts/vendors.js?v=[hash]', Infinity) // 这是第三方库打包生成的文件
  ],
  resolve: {
      extensions: ['', '.js', '.jsx'],
  },
  module: {
    loaders: [
      {
        test: /\.js|jsx$/,
        loaders: ['babel'],
        include: path.join(__dirname,'/src')
      },
      {
        test: /\.(gif|jpg|png|woff|svg|eot|ttf)$/,
        loader: 'url-loader?limit=50000&name=images/[hash:8].[name].[ext]'
      },
      {
        test : /\.(scss|css)$/,
        loader: ExtractTextPlugin.extract('style-loader','css!sass')
      },
      {
        test: /\.html$/,
        loader: 'html-withimg-loader'
      }
    ]
  }
};

```