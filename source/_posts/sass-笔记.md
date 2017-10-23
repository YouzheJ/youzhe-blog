---
title: sass 笔记
date: 2017-10-23 17:52:50
tags:
- 前端
- css
- sass
categories:
- 笔记
- 前端
- css
---

sass入门 [http://www.w3cplus.com/sassguide/index.html](http://www.w3cplus.com/sassguide/index.html)

#### 安装

<!--more-->

- 因为sass依赖于ruby环境，所以先下载ruby
- 安装时，请勾选Add Ruby executables to your PATH这个选项，添加环境变量，不然以后使用编译软件的时候会提示找不到ruby环境
- 安装后，在开始菜单中，找到刚才我们安装的ruby，打开Start Command Prompt with Ruby
    - 命令行中输入
    ```
    gem install sass
    ```
    - 如果要安装beta版
    ```
    gem install sass --pre
    ```
    - 升级sass版本命令
    ```
    gem update sass
    ```
    - 查看sass版本
    ```
    sass -v
    ```
    - 帮助命令
    ```
    sass -h
    ```

##### 文件后缀
sass有两种后缀名文件：一种后缀名为sass，不使用大括号和分号；另一种就是我们这里使用的scss文件，这种和我们平时写的css文件格式差不多，使用大括号和分号。

#### 导入
@import

#### 注释
一种是标准的css注释方式/* */，另一种则是//双斜杆形式的单行注释，不过这种单行注释不会被转译出来。

#### 语法
- Sass用两个空格缩进来定义代码的嵌套
- 变量：$red 变量必须以$开头，在值后面加上 !default 则表示默认值
    - 特殊变量：必须以 #{variables}形式
    ```
    //sass style
    //-------------------------------
    $borderDirection:       top !default; 
    $baseFontSize:          12px !default;
    $baseLineHeight:        1.5 !default;
    
    //应用于class和属性
    .border-#{$borderDirection}{
      border-#{$borderDirection}:1px solid #ccc;
    }
    //应用于复杂的属性值
    body{
        font:#{$baseFontSize}/#{$baseLineHeight};
    }
    
    //css style
    //-------------------------------
    .border-top{
      border-top:1px solid #ccc;
    }
    body {
      font: 12px/1.5;
    }
    ```
    - 多值变量
        - list：(似数组) list数据可通过空格，逗号或小括号分隔多个值，可用nth($var,$index)取值。关于list数据操作还有很多其他函数如length($list)，join($list1,$list2,[$separator])，append($list,$value,[$separator])等
        - map：(似对象) map数据以key和value成对出现，其中value又可以是list。格式为：$map: (key1: value1, key2: value2, key3: value3);。可通过map-get($map,$key)取值。关于map数据还有很多其他函数如map-merge($map1,$map2)，map-keys($map)，map-values($map)等
        ```
        //sass style
        //-------------------------------
        $headings: (h1: 2em, h2: 1.5em, h3: 1.2em);
        @each $header, $size in $headings {
          #{$header} {
            font-size: $size;
          }
        }
        
        //css style
        //-------------------------------
        h1 {
          font-size: 2em; 
        }
        h2 {
          font-size: 1.5em; 
        }
        h3 {
          font-size: 1.2em; 
        }
        ```
    
- 嵌套
    - 选择器嵌套
    - 属性嵌套
    ```
    $fontsize: 12px

    .speaker
      .name
        font:
          weight: bold
          size: $fontsize + 10px
      .position
        font:
          size: $fontsize
    ```
- 混合 @mixin
    ```
    @mixin border-radius($amount: 5px)
      -moz-border-radius: $amount
      -webkit-border-radius: $amount
      border-radius: $amount
    
    h1
      @include border-radius(2px)
    
    .speaker
      @include border-radius
     ```
 
    - @content：@content在sass3.2.0中引入，可以用来解决css3的@media等带来的问题。它可以使@mixin接受一整块样式，接受的样式从@content开始。
    ```
     //sass style
    //-------------------------------                     
    @mixin max-screen($res){
      @media only screen and ( max-width: $res )
      {
        @content;
      }
    }
    
    @include max-screen(480px) {
      body { color: red }
    }
    
    //css style
    //-------------------------------
    @media only screen and (max-width: 480px) {
      body { color: red }
    }   
    ```
    > ps：在@media中暂时不能@extend @media外的代码片段，以后将会可以。
    
 - 选择器继承 @extend
 ```
 h1
  border: 4px solid #ff9aa9

.speaker
  @extend h1
  border-width: 2px
```
- 占位选择器 % ：如果不调用则不会有任何多余的css文件，避免了以前在一些基础的文件中预定义了很多基础的样式，然后实际应用中不管是否使用了@extend去继承相应的样式，都会解析出来所有的样式。占位选择器以%标识定义，通过@extend调用。
- 函数
    - @function
    ```
    $baseFontSize:      10px !default;
    $gray:              #ccc !defualt;        
    
    // pixels to rems 
    @function pxToRem($px) {
      @return $px / $baseFontSize * 1rem;
    }
    
    body{
      font-size:$baseFontSize;
      color:lighten($gray,10%);
    }
    .test{
      font-size:pxToRem(16px);
      color:darken($gray,10%);
    }
    
    //css style
    //-------------------------------
    body{
      font-size:10px;
      color:#E6E6E6;
    }
    .test{
      font-size:1.6rem;
      color:#B3B3B3;
    }
    ```
    
    - 常用：颜色函数中又以lighten减淡和darken加深为最，其调用方法为lighten($color,$amount)和darken($color,$amount)，它们的第一个参数都是颜色值，第二个参数都是百分比。
- 运算
    - 加减乘除四则运算，请注意运算符前后请留一个空格
    - @if,@else if,@else
    ```
    p {
      @if $type == ocean {
        color: blue;
      } @else if $type == matador {
        color: red;
      } @else if $type == monster {
        color: green;
      } @else {
        color: black;
      }
    }
    ```
    - 三目运算
    ```
    if(true, 1px, 2px)
    ```
    - for: for循环有两种形式，分别为：@for $var from <start> through <end>和@for $var from <start> to <end>。$i表示变量，start表示起始值，end表示结束值，这两个的区别是关键字through表示包括end这个数，而to则不包括end这个数。
    ```
    @for $i from 1 through 3 {
      .item-#{$i} { width: 2em * $i; }
    }
    ```
    - @each循环: @each $var in <list or map>。其中$var表示变量，而list和map表示list类型数据和map类型数据。