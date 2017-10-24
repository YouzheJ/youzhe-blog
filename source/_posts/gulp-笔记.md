---
title: gulp 笔记
date: 2017-10-24 13:11:13
tags:
- 前端
- gulp
categories:
- 笔记
- 前端
---

##### 中文网站：[http://www.gulpjs.com.cn/](http://www.gulpjs.com.cn/)

<!--more-->

#### 入门
- 全局安装gulp
```
npm install --global gulp
```
- 作为项目的开发依赖
```
npm install --save-dev gulp
```
- 在项目的根目录下创建 gulpfile.js
```
var gulp = require('gulp');

gulp.task('default', function() {
  // 将你的默认的任务代码放在这
});
```
- 运行
```
gulp
```
> 默认的名为 default 的任务（task）将会被运行。

> 想要单独执行特定的任务（task），请输入 gulp <task> <othertask>。

#### API
- gulp.src(globs[, options])
- gulp.dest(path[, options])
- gulp.task(name[, deps], fn)
- gulp.watch(glob [, opts], tasks)
- gulp.watch(glob[, opts, cb])

#### 添加版本号
1. 安装gulp和gulp插件
```
npm install --save-dev gulp
npm install --save-dev gulp-rev
npm install --save-dev gulp-rev-collector
npm install --save-dev gulp-asset-rev
npm install --save-dev gulp-sequence
```

2. 编写gulpfile.js
```
//引入gulp和gulp插件
var gulp = require('gulp'),
  assetRev = require('gulp-asset-rev'),
  runSequence = require('run-sequence'),
  rev = require('gulp-rev'),
  revCollector = require('gulp-rev-collector');
 
//定义css、js源文件路径
var cssSrc = 'css/*.css',
  jsSrc = 'js/*.js';
 
//为css中引入的图片/字体等添加hash编码
gulp.task('assetRev', function(){
  return gulp.src(cssSrc)  //该任务针对的文件
   .pipe(assetRev()) //该任务调用的模块
   .pipe(gulp.dest('src/css')); //编译后的路径
});
 
//CSS生成文件hash编码并生成 rev-manifest.json文件名对照映射
gulp.task('revCss', function(){
  return gulp.src(cssSrc)
    .pipe(rev())
    .pipe(rev.manifest())
    .pipe(gulp.dest('rev/css'));
});
 
 
//js生成文件hash编码并生成 rev-manifest.json文件名对照映射
gulp.task('revJs', function(){
  return gulp.src(jsSrc)
    .pipe(rev())
    .pipe(rev.manifest())
    .pipe(gulp.dest('rev/js'));
});
 
 
//Html替换css、js文件版本
gulp.task('revHtml', function () {
  return gulp.src(['rev/**/*.json', 'View/*.html'])
    .pipe(revCollector())
    .pipe(gulp.dest('View'));
});
 
 
//开发构建
gulp.task('default', function (done) {
  condition = false;
  runSequence(    //需要说明的是，用gulp.run也可以实现以上所有任务的执行，只是gulp.run是最大限度的并行执行这些任务，而在添加版本号时需要串行执行（顺序执行）这些任务，故使用了runSequence.
    ['assetRev'],
    ['revCss'],
    ['revJs'],
    ['revHtml'],
    done);
});

```

3. 执行gulp
> default --> default-803a7fe4ae.css

4. 更改gulp-rev和gulp-rev-collector
> 打开node_modules\gulp-rev\index.js
> 第144行 manifest[originalFile] = revisionedFile;
> 更新为: manifest[originalFile] = originalFile + '?v=' + file.revHash;
> 打开nodemodules\gulp-rev\nodemodules\rev-path\index.js
> 10行 return filename + '-' + hash + ext;
> 更新为: return filename + ext;


打开node_modules\gulp-rev-collector\index.js


31行 if ( !_.isString(json[key]) || path.basename(json[key]).replace(new RegExp( opts.revSuffix ), '' ) !==  path.basename(key) ) {


更新为: if ( !_.isString(json[key]) || path.basename(json[key]).split('?')[0] !== path.basename(key) ) {


打开node_modules\gulp-assets-rev\index.js


78行 var verStr = (options.verConnecter || "-") + md5;


更新为：var verStr = (options.verConnecter || "") + md5;


80行 src = src.replace(verStr, '').replace(/(\.[^\.]+)$/, verStr + "$1");


更新为：src=src+"?v="+verStr;
