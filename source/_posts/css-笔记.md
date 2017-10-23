---
title: css 笔记
date: 2017-10-23 13:20:35
tags:
- 前端
- css
categories:
- 笔记
- 前端
- css
---

- #### 修改选中文字的颜色，兼容ie9+（包括ie9）

<!--more-->

```css
::selection {
    background:#36C6D3; 
    color:#fff;
}

::-moz-selection {
    background:#36C6D3; 
    color:#fff;
}

::-webkit-selection {
    background:#36C6D3;
    color:#fff;
}
```

- #### flex 兼容写法 [https://segmentfault.com/a/1190000003978624](https://segmentfault.com/a/1190000003978624)

```css
.box{

    display: -webkit-flex;  /* 新版本语法: Chrome 21+ */
    display: flex;          /* 新版本语法: Opera 12.1, Firefox 22+ */
    display: -webkit-box;   /* 老版本语法: Safari, iOS, Android browser, older WebKit browsers. */
    display: -moz-box;      /* 老版本语法: Firefox (buggy) */
    display: -ms-flexbox;   /* 混合版本语法: IE 10 */   

 }

.flex1 {            
    -webkit-flex: 1;        /* Chrome */  
    -ms-flex: 1             /* IE 10 */  
    flex: 1;                /* NEW, Spec - Opera 12.1, Firefox 20+ */
    -webkit-box-flex: 1     /* OLD - iOS 6-, Safari 3.1-6 */  
    -moz-box-flex: 1;       /* OLD - Firefox 19- */       
}
```

- #### 内容不可选

```css
body{
    -webkit-user-select:none;
    -moz-user-select:none;
    -ms-user-select:none;
    user-select:none;
}
```