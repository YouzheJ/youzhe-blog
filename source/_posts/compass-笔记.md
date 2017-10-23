---
title: compass 笔记
date: 2017-10-23 12:03:38
tags:
- 前端
- css
- sass
- compass
- sprite
categories:
- 笔记
- 前端
- css
---

> 和sass一样依赖于ruby, 并且需要在sass或scss中使用

#### 基本使用

<!--more-->

- 安装与初始化项目(已存在的项目为例)

```
gem install compass
cd <myproject>
compass create --sass-dir "static/sass" --css-dir "static/css" --images-dir "static/images"
```

- 监听变化

```
cd <myproject>
compass watch
```

- 生成环境

```
compass compile --production
```

#### 部分模块

[http://www.ruanyifeng.com/blog/2012/11/compass.html](http://www.ruanyifeng.com/blog/2012/11/compass.html)

- reset
> 去除浏览器默认样式, 不是必须使用
```
@import "compass/reset" 
```

- css3

1. 圆角
    
```
@import "compass/css3"
.rounded {
　　@include border-radius(5px);
}
```
    
2. 透明
    
```
@import "compass/css3"
#opacity {
　　@include opacity(0.5); 
}
```

3. 行内区块

```
@import "compass/css3"
#inline-block {
　　@include inline-block;
}
```

...

#### 自动sprite

> 只支持png

1. 将图片放入一个文件夹中(以下为my-icons)

```
images/my-icons/new.png
images/my-icons/edit.png
images/my-icons/save.png
images/my-icons/delete.png
```
> 生成的雪碧图存放在images中

> 以下没有说明的话, 均在scss中使用

2. 基本使用

```
@import "compass/utilities/sprites";
@import "my-icons/*.png"; // my-icons 替换为存放图片的文件夹
@include all-my-icons-sprites; // my-icons 替换为存放图片的文件夹
```
- 编译后的css
```
.my-icons-sprite,
.my-icons-delete,
.my-icons-edit,
.my-icons-new,
.my-icons-save   { background: url('/images/my-icons-s34fe0604ab.png') no-repeat; }

.my-icons-delete { background-position: 0 0; }
.my-icons-edit   { background-position: 0 -32px; }
.my-icons-new    { background-position: 0 -64px; }
.my-icons-save   { background-position: 0 -96px; }
```
> 通过类名使用

- 编译选项

```
compass compile --output-style compressed // 生产
compass compile --force // 编译未修改的文件
```

3. 文件夹嵌套时, 会使用最后的文件名

```
@import "themes/orange/*.png"
@include all-orange-sprites
```

4. 在选择器中使用

```
@import "my-icons/*.png"

.actions {
  .new    { @include my-icons-sprite(new);    }
  .edit   { @include my-icons-sprite(edit);   }
  .save   { @include my-icons-sprite(save);   }
  .delete { @include my-icons-sprite(delete); }
}
```

5. 其他设置

> 在@import 图片前使用

- 排序

```
$my-sprite-sort-by : 'width'
$my-sprite-sort-by : '!width' // 降序
/* 
width
height
size
name
none // 默认值
*/
```

- 排列方式

```
$icon-layout:horizontal // 默认
/*
vertical
horizontal
diagonal // 对角线
smart // 智能模式
*/
```

- 修改分隔符

```
$default-sprite-separator: "_"
```

- 雪碧图的设置

```
// 以下icon为目录名
$icon-spacing: 20px // 图标之间的距离 默认为0
$icon-repeat: repeat-x // 默认为no-repeat
$icon-position: 100% // 默认为0
$icon-sprite-base-class: ".action-icon" // class的开头, 这里默认为icon-sprite
$<map>-clean-up: true // 删除旧图, 默认为false
```

- 具体设置一个图

```
// icon为文件夹, new为具体的图片名
$icon-new-spacing: 20px
$icon-new-repeat: repeat-x
$icon-new-position: 100%
```


6. 使用例子: 根据图片设置宽高

```
@import "my-icons/*.png"
$box-padding: 5px
$height: my-icons-sprite-height(some_icon)
$width: my-icons-sprite-width(some_icon)

.somediv {
  height:$height + $box-padding
  width:$width + $box-padding
}
```

7. 通过命令行生成

```
compass sprite "images/my-icons/*.png"
```