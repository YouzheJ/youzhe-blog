---
title: typescript 使用笔记
date: 2017-10-24 11:51:15
tags:
- 前端
- typescript
categories:
- 笔记
- 前端
- typescript
---

- #### 声明window中的变量

<!--more-->

```js
namespace window {
    export let xxx: xxx;
}
```

- #### 使用script引入的变量

```js
declare const xxx: xxx;
```

- #### 使用whatwg-fetch

```js
import "whatwg-fetch" // 和平常一样使用, 并且不需要@types/whatwg-fetch
```

- #### 类型断言例子

```js
// 例如在React中使用第三方的库, 通过refs获取其方法
(this.refs.cropper as any).getCroppedCanvas().toDataURL();
```