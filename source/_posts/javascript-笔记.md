---
title: javascript 笔记
date: 2017-10-24 11:25:51
tags:
- 前端
- JavaScript
categories:
- 笔记
- 前端
- javascript
---

> 分类目录

<!--more-->

---
1. #### 正则

[28. RegExp.$1](#RegExp.$1)

[26. 正则 贪婪模式/惰性模式](#正则的贪婪模式和惰性模式)

[40. 正则 正则的一些使用](#正则的一些使用)

[41. 正则 匹配html中的内容](#正则匹配html中的内容)

2. #### js操作

[1. js中数组的一些方法](#js中数组的一些方法)

[6. change的触发bug](#change的触发bug)

[7. 字符串的一些操作](#字符串的一些操作)

[8. js获取当前时间及时间戳的相互转换](#js获取当前时间及时间戳的相互转换)

[9. ES6中Object.assign()进行对象的拷贝](#ES6中进行对象的拷贝)

[15. 获取鼠标的当前位置](#获取鼠标的当前位置)

[16. 运算符优先级](#运算符优先级)

[18. 元素是对象的js数组排序](#元素是对象的js数组排序)

[21. window.onerror](#window.onerror)

[39. 字符串数字转为数字](#字符串数字转为数字)

[42. 颜色的一些操作](#颜色的一些操作)

[43. js进制转换](#js进制转换)

3. #### 兼容

[0. 低版本IE兼容性](#低版本IE兼容性)

[19. 兼容ie8的indexOf](#兼容ie8的indexOf)

[23. javascript:;/javascript:void(0);的兼容性问题](#javascript:;/javascript:void(0);的兼容性问题)

4. #### 一些例子

[10. input的file样式建议](#input的file样式建议)

[11. 一个简单的使用input的file上传图片并预览的例子](#一个简单的使用input的file上传图片并预览的例子)

[12. 使用js获取页面元素的位置](#使用js获取页面元素的位置)

[14. 浏览器页面关闭以及刷新时弹框提示](#浏览器页面关闭以及刷新时弹框提示)

[17. 使用canvas将图片转为base64](#使用canvas将图片转为base64)

[22. 兼容ie9的图片上传](#兼容ie9的图片上传)

[29. 引号的使用](#引号的使用)

[31. 缓动动画Tween.js](#缓动动画Tween.js)

[30. setInterval的楼层滚动（兼容ie9）](#setInterval的楼层滚动（兼容ie9）)

[32. 拖拽排序的一种实现](#拖拽排序的一种实现)

[35. fixed滚动的一个例子](#fixed滚动的一个例子)

[36. 通过post跳转页面隐藏参数](#通过post跳转页面隐藏参数)

5. #### 一些问题

[24. chrome的bug:mouseup后会执行一次mousemove](#chrome的bug:mouseup后会执行一次mousemove)

[25. window.open被浏览器拦截不能打开](#window.open被浏览器拦截不能打开)

[27. localStroge在不同域名下不能访问（不同子域）](#localStroge在不同域名下不能访问（不同子域）)

[34. window.location.href的问题](#window.location.href的问题)

6. #### 其他

[2. 前端插件库](#前端插件库)

[3. throttle](#throttle)

[4. fetch](#fetch)

[5. 调试跨域](#调试跨域)

[13. 原生动画优化](#原生动画优化)

[20. cookie的使用](#cookie的使用)

[38. 图片base64字符串的使用说明](#图片base64字符串的使用说明)

[33. checkbox的不确定状态](#checkbox的不确定状态)

[37. rem结合js的使用](#rem结合js的使用)

---

0. 低版本IE兼容性
    - IE8/9默认没有console，所以如果代码里残留了类似console.log的引用就会报错，或者加入如下代码：

    ```js
    // Avoid `console` errors in browsers that lack a console.
    (function() {
        var method;
        var noop = function () {};
        var methods = [
            'assert', 'clear', 'count', 'debug', 'dir', 'dirxml', 'error',
            'exception', 'group', 'groupCollapsed', 'groupEnd', 'info', 'log',
            'markTimeline', 'profile', 'profileEnd', 'table', 'time', 'timeEnd',
            'timeline', 'timelineEnd', 'timeStamp', 'trace', 'warn'
        ];
        var length = methods.length;
        var console = (window.console = window.console || {});
    
        while (length--) {
            method = methods[length];
    
            // Only stub undefined methods.
            if (!console[method]) {
                console[method] = noop;
            }
        }
    }());
    ```
1. #### js中数组的一些方法
- filter(): array.filter(callback[, thisObject])
    - thisObject ： 在执行回调函数时定义的this对象
    - callback函数的第一个参数为当前元素，第二个为下标，第三个为数组
    - 对数组中的每个元素都执行一次指定的函数（callback），并且创建一个新的数组，该数组元素是所有回调函数执行时返回值为 true 的原数组元素。
        > **不会改变原数组。**
- map(): 返回一个由原数组中的每个元素调用一个指定方法后的返回值组成的新数组
- some()/every()：判断数组时使用，返回true/false
- forEach()：对数组中的每个元素都执行forEach()中的方法
- indexOf()：正向搜索
- lastIndexOf()：反向搜索

2. #### 前端插件库 

> [awesomes](https://www.awesomes.cn/)

3. #### throttle
    throttle：函数节流，控制函数的调用频度，连续执行的时间间隔控制

    debounce：是空闲时间必须大于或等于 一定值的时候，才会执行调用方法。debounce是空闲时间的间隔控制
参考例子：

```js
/*
* 频率控制 返回函数连续调用时，fn 执行频率限定为每多少时间执行一次
* @param fn {function}  需要调用的函数
* @param delay  {number}    延迟时间，单位毫秒
* @param immediate  {bool} 给 immediate参数传递false 绑定的函数先执行，而不是delay后后执行。
* @return {function}实际调用函数
*/
var throttle = function (fn,delay, immediate, debounce) {
   var curr = +new Date(),//当前事件
       last_call = 0,
       last_exec = 0,
       timer = null,
       diff, //时间差
       context,//上下文
       args,
       exec = function () {
           last_exec = curr;
           fn.apply(context, args);
       };
   return function () {
       curr= +new Date();
       context = this,
       args = arguments,
       diff = curr - (debounce ? last_call : last_exec) - delay;
       clearTimeout(timer);
       if (debounce) {
           if (immediate) {
               timer = setTimeout(exec, delay);
           } else if (diff >= 0) {
               exec();
           }
       } else {
           if (diff >= 0) {
               exec();
           } else if (immediate) {
               timer = setTimeout(exec, -diff);
           }
       }
       last_call = curr;
   }
};
 
/*
* 空闲控制 返回函数连续调用时，空闲时间必须大于或等于 delay，fn 才会执行
* @param fn {function}  要调用的函数
* @param delay   {number}    空闲时间
* @param immediate  {bool} 给 immediate参数传递false 绑定的函数先执行，而不是delay后后执行。
* @return {function}实际调用函数
*/
 
var debounce = function (fn, delay, immediate) {
   return throttle(fn, delay, immediate, true);
};
```

4. #### fetch
[https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalFetch/fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalFetch/fetch)

**fetch() 方法用于发起获取资源的请求。它返回一个 promise，这个 promise 会在请求响应后被 resolve，并传回 Response 对象**
 
参数:
- 获取资源的路径，或request对象
- 配置对象，可选参数有：
    - method: 请求使用的方法，如 GET、POST。
    - headers: 请求的头信息，形式为 Headers 的对象或包含 ByteString 值的对象字面量。
    - body: 请求的 body 信息：可能是一个 Blob、BufferSource、FormData、URLSearchParams 或者 USVString 对象。注意 GET 或 HEAD 方法的请求不能包含 body 信息。
    - mode: 请求的模式，如 cors、 no-cors 或者 same-origin。
    - credentials: 请求的 credentials，如 omit、same-origin 或者 **include**。为了在当前域名内**自动发送 cookie** ， 必须提供这个选项， 从 Chrome 50 开始， 这个属性也可以接受 FederatedCredential 实例或是一个 PasswordCredential 实例。
    - cache:  请求的 cache 模式: default 、 no-store 、 reload 、 no-cache 、 force-cache 或者 only-if-cached。
    - redirect: 可用的 redirect 模式: follow (自动重定向), error (如果产生重定向将自动终止并且抛出一个错误), 或者 manual (手动处理重定向). 在Chrome中，Chrome 47之前的默认值是 follow，从 Chrome 47开始是 manual。
    - referrer: 一个 USVString 可以是 no-referrer、client或一个 URL。默认是 client。
    - referrerPolicy: Specifies the value of the referer HTTP header. May be one of no-referrer、 no-referrer-when-downgrade、 origin、 origin-when-cross-origin、 unsafe-url。
    - integrity: 包括请求的  subresource integrity 值 （ 例如： sha256-BpfBw7ivV8q2jLiT13fxDYAe2tJllusRSZ273h2nFSE=。

返回值：
resolve回传response对象, 对象中的方法有：
- arrayBuffer()
- blob()
- formData()
- json()
- text()

```js
fetch('url', options)
.then((response) => { if (response.ok) return res.json(); })
.then((response) => {
    if (/* *** */) {
        
    }
    resolve(response)
})
.catch((e) => { console.log(e.message) })
```

5. #### 调试跨域

> 调试时遇到跨域问题，如果浏览器是chrome，可以使用插件 'allow-control-allow-origin'

6. #### change的触发bug

> input的value是通过js改变的，使用change事件是触发不了的

7. #### 字符串的一些操作
- 只保留数字 str.replace(/[^\d]/g,'')
- 从头开始，保留9个字符 str.substr(0,9)

8. #### js获取当前时间及时间戳的相互转换
- 获取当前时间戳

```js
Date.parse(new Date())
```
> 结果：1486347562000
- 获取当前时间
```
new Date()
```
> 结果：Mon Feb 06 2017 10:19:42 GMT+0800 (CST)
- 将时间戳转换为时间
```
new Date(1486347562000)
```
> 结果：Mon Feb 06 2017 10:19:22 GMT+0800 (CST)
- 将时间转换为时间戳
```
Date.parse(new Date("2017-02-06 10:30:50"))
```
> 结果：1486348250000

9. #### ES6中进行对象的拷贝

> Object.assign()

```js
// Cloning an object
var obj = { a: 1 };
var copy = Object.assign({}, obj);
console.log(copy); // { a: 1 }
```
```js
// Merging objects
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };

var obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 2, c: 3 }, target object itself is changed.
```
#### 注意：Object.assign() 只是一级属性复制，比浅拷贝多深拷贝了一层而已

> 附：一个投机的简单方法
```
const obj1 = JSON.parse(JSON.stringify(obj));
```

10. #### input的file样式建议

> input的file类型，可以将其设为透明，然后使用label标签的for关联其id，以此实现自定义的样式。

11. #### 一个简单的使用input的file上传图片并预览的例子

```js
var file = e.target.files[0];
if(file) {// 如果不做判断，取消时会报错
    // 那么我们可以做一下诸如文件大小校验的动作
    if(file.size > 1024 * 1024 * 3) {
        alert('图片大小不能超过 3MB!');
        return false;
    }
    // !!!!!!
    // 下面是关键的关键，通过这个 file 对象生成一个可用的图像 URL
    // 获取 window 的 URL 工具
    var URL = window.URL || window.webkitURL;
    // 通过 file 生成目标 url
    var imgURL = URL.createObjectURL(file);
    
    转为formData，以blob上传
    var formData = new FormData();
	formData.append(file.name, file);
    
    //获取图片的真实尺寸
    var img = new Image();
	img.src = imgURL;
	img.onload = () => {
	    console.log('wdth: ',img.width, 'height: ', img.height)
	}
}
```


12. #### 使用js获取页面元素的位置
- clientHeight和clientWidth属性。这两个属性指元素的内容部分再加上padding的所占据的视觉面积，不包括border和滚动条占用的空间
- document对象的scrollHeight和scrollWidth属性就是网页的大小，意思就是滚动条滚过的所有长度和宽度
- 每个元素都有offsetTop和offsetLeft属性，表示该元素的左上角与父容器（offsetParent对象）左上角的距离。所以，只需要将这两个值进行累加，就可以得到该元素的绝对坐标

> 一个获取元素相对于整个页面位置的例子：

```js
function getElementLeft(element){ // 获取元素相对于页面的left
    var actualLeft = element.offsetLeft;
    var current = element.offsetParent;
    while (current !== null){
      actualLeft += current.offsetLeft;
      current = current.offsetParent;
    }
    return actualLeft;
  }
  function getElementTop(element){  // 获取元素相对于页面的top
    var actualTop = element.offsetTop;
    var current = element.offsetParent;
    while (current !== null){
      actualTop += current.offsetTop;
      current = current.offsetParent;
    }
    return actualTop;
  }
```

13. #### 原生动画优化
- 一般使用css的transition或@keyframes
- 使用setTimeout制作动画时，会低效的强迫文档进行迅速重排，并且结构的抖动会很快使页面卡顿
> 替代方案：用window.requestAnimationFrame()同步页面的更新，把当前的所有改变安排到下一次浏览器重绘，详情可参考 [https://www.sitepoint.com/quick-tip-game-loop-in-javascript/](https://www.sitepoint.com/quick-tip-game-loop-in-javascript/)

```js
const start = window.performance.now()
const duration = 2000

window.requestAnimationFrame(function fadeIn (now) {
  const progress = now - start
  myElement.style.opacity = progress / duration

  if (progress < duration) {
    window.requestAnimationFrame(fadeIn)
  }
})
```

14. #### 浏览器页面关闭以及刷新时弹框提示
- 不兼容firefox

```js
window.onbeforeunload = function(){ 
    return '确认退出?';
} 
```
- 兼容firefox

```js
window.onbeforeunload = function checkLeave(e){  
    var evt = e ? e : (window.event ? window.event : null);  //此方法为了在firefox中的兼容  
    if(true) // 可在此做是否提示的判断
        evt.returnValue='确认离开?'; 
}  
```

15. #### 获取鼠标的当前位置

```js
// scrollTop 兼容性
// scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop 
getXY = (e) => {// 获取鼠标相对于body的位置
    var ev = e || window.event;
    var x=0,y=0;
    if(ev.pageX){
        x = ev.pageX;
        y = ev.pageY;
    }else{
        //拿到scrollTop 和scrollLeft
        var sleft = 0,stop = 0;
        //ie678---
    if(document.documentElement){
        stop =document.documentElement.scrollTop;
        sleft = document.documentElement.scrollLeft;
    }else{
        //ie9+ 谷歌
        stop = document.body.scrollTop;
        sleft = document.body.scrollLeft;
    }	
        x = ev.clientX + sleft;
        y = ev.clientY + stop;
    }
    return {x:x,y:y}
}
```

16. #### 运算符优先级

运算符 | 描述
---|---
. [] () | 字段访问、数组下标、函数调用以及表达式分组
++ -- - ~ ! delete new typeof void |	一元运算符、返回数据类型、对象创建、未定义值
* / % |	乘法、除法、取模
+ - + |	加法、减法、字符串连接
<< >> >>> |	移位
< <= > >= instanceof	| 小于、小于等于、大于、大于等于、instanceof
== != === !==	| 等于、不等于、严格相等、非严格相等
&	| 按位与
^	| 按位异或
\|	| 按位或
&&	| 逻辑与
\|\|	| 逻辑或
?:	| 条件
= oP=	| 赋值、运算赋值
,	| 多重求值

17. #### 使用canvas将图片转为base64

```js
let img = new Image();// 生成或者获取图片对象
img.src = imgURL;
let canvas = document.createElement('canvas');// 创建canvas
canvas.width = img.width;// 将canvas的宽高设置与图片相等
canvas.height = img.height;
let ctx = canvas.getContext('2d');
ctx.drawImage(img, 0, 0);// 将图片在canvas中绘制，可调节大小和位置，详情可查API，此处默认铺满
let baseString = canvas.toDataURL();// 编码为base64字符串
```

18. #### 元素是对象的js数组排序

- 可以进行如下操作：

```js
var compare = function (prop) { // 数组排序，按照prop排序
	return function (obj1, obj2) {
		var val1 = obj1[prop];
		var val2 = obj2[prop];
		return val1 - val2 // 由小到大排序
	}
}

var arr = [{val:2332,order:1}, {val:8634,order:2}, {val:5342,order:0}];
arr.sort(compare('order')) // arr中每一项都是对象，根据order属性排序
```

19. #### 兼容ie8的indexOf

```js
if (!Array.prototype.indexOf) {
  Array.prototype.indexOf = function(elt /*, from*/) {
    var len = this.length >>> 0;
    var from = Number(arguments[1]) || 0;
    from = (from < 0)
        ? Math.ceil(from)
        : Math.floor(from);
    if (from < 0)
      from += len;
    for (; from < len; from++) {
      if (from in this &&
          this[from] === elt)
        return from;
    }
    return -1;
  };
}
```

20. #### cookie的使用

```js
// 设置cookie和过期时间
function setCookie(c_name,value,expiredays) {
    var exdate=new Date()
    exdate.setDate(exdate.getDate()+expiredays)
    // escape() 现应使用 decodeURI() 和 decodeURIComponent() 代替
    document.cookie=c_name+ "=" +escape(value)+ 
    ((expiredays==null) ? "" : ";expires="+exdate.toGMTString())
}

// 从cookie中取出特定值
function getCookie(c_name) {
    if (document.cookie.length>0) {
        var c_start=document.cookie.indexOf(c_name + "=")
        if (c_start!=-1) { 
            c_start=c_start + c_name.length+1 
            var c_end=document.cookie.indexOf(";",c_start)
            if (c_end==-1) c_end=document.cookie.length
            //  unescape() 现应用 encodeURI() 和 encodeURIComponent()替代
            return unescape(document.cookie.substring(c_start,c_end))
        } 
    }
    return ""
}
```

21. #### window.onerror
考虑到兼容性，监听方式使用window.onerror即可。

```js
window.onerror = function(errorMessage, scriptURI, lineNumber, columnNumber, error) {
 if (error) {
  reportError(error);
 } else {
  reportError({
    message: errorMessage,
    script: scriptURI,
    line: lineNumber,
    column: columnNumber
  });
 }
}

function reportError(err) {
  console.log('error log: ',err);
}
```

22. #### 兼容ie9的图片上传

> mOxie 使用

- flash使用注意事项：
    - flash 不能放在动态的弹框中，不管是显示隐藏、挂载销毁，都会使flash失效；

- 使用mOxie的解决方案：
1. 通过mOxie的FileInput 以html5/flash 获取file对象
2. 进行完格式和大小验证后， 通过mOxie的FileReader将file对象转换为base64字符串
3. 通过base64上传

```html
<script src="state/plugin/moxie.js"></script>
<script>mOxie.Env.swf_url = 'state/plugin/Moxie.swf';</script>

<div id="file-container">
	<span id="file-picker">请选择图片上传</span>
</div>
```
```js
let fileInput = new mOxie.FileInput({
    browse_button: 'file-picker', // or document.getElementById('file-picker')
    constainer: 'file-container',
    accept: [
    {title: 'Images', extensions: 'jpg,gif,png,jpeg'} // accept only images
    ],
    runtime_order: 'html5,flash',
    });
    fileInput.onchange = async function(e) {
    // console.info(e.target.files); // or this.files or fileInput.files
    let file = e.target.files[0];
    if(file) {// 如果不判断，取消的时候回报错
    if(file.size > 1024 * 1024 * 3) {
    	// alert('图片大小不能超过 5MB!');
    	$this.props.setMask({loading:false, msg: '图片大小不能超过 3MB!'});
    	return ;
    }
    // 验证图片格式
    switch(file.type) { // jpg会识别为jpeg
    	case 'image/png':
    	case 'image/jpg': 
    	case 'image/jpeg':
    	case 'image/gif': break;
    	default:
    			alert('图片格式必须为jpg、jpeg、png、gif!');
    			return;
    }
    let fr = new mOxie.FileReader();
    fr.onload = async (e) => {
		// console.log(e.target, arr); // e.target.result   base64
		let baseString = e.target.result;
		let result = await fetchs({
        		url: `/image/upload/base`, 
        		method:'post', 
        		body: JSON.stringify({imageBase:baseString}) 
		})
	}
```

23. #### javascript:;/javascript:void(0);的兼容性问题
- 在普通浏览器中使用 javascript:; / javascript:void(0); 可以禁止掉a标签href的跳转功能
- 在ie低版本中，用上述方法可能会无效，导致bug的出现，可以使用以下的解决方式：
    - 将href指定为不存在的锚点，如 '###' （推荐使用此方法）


24. #### chrome的bug:mouseup后会执行一次mousemove

解决：判断鼠标是否真的移动了

```js
var obj = {};
body.onmousemove = function(e) {
    if(e.pageX === obj.x && e.pageY === obj.y) 
        return false
}
body.onmouseup = function(e) {
    obj.x = e.pageX;
    obj.y = e.pageY;
}
```

25. #### window.open被浏览器拦截不能打开
> 当window.open 是由用户触发或者是普通的执行时，浏览器不会拦截，但是当open代码是在ajax或者异步代码时，会被浏览器拦截。

解决：...

26. #### 正则的贪婪模式和惰性模式
- 量词？、*、+的意义，可以指定相关模式出现的次数，默认的情况下我们使用的是贪婪量词，**匹配的过程是从后往前**，最大长度匹配

```js
var s ="abbbaabbbaaabbb1234";
var re1=/.*bbb/g;//*是贪婪量词
re1.exec(s); // abbbaabbbaaabbb
```
- 在贪婪量词的后面加多一个?就变成了惰性量词，**匹配过程从前往后**，最小长度匹配

```js
var s ="abbbaabbbaaabbb1234";
var re1=/.*?bbb/g;//*?是惰性量词
re1.exec(s); // abbb
```

27. #### localStroge在不同域名下不能访问（不同子域）
- 解决：在两个页面下都指定相同的根域名

```js
// 设置localStorage
localStorage['aaa'] = bbb;
document.domain = 'xxx.com';

// 访问localStorage
document.domain = 'xxx.com';
var ccc = localStorage['aaa'];
```

28. #### RegExp.$1
- RegExp的一个属性,指的是与正则表达式匹配的第一个 子匹配(以括号为标志)字符串，以此类推，RegExp.$2，RegExp.$3，..RegExp.$99总共可以有99个匹配

```js
// 例子
/(\d*)-([a-zA-Z]*)-(\d*)/.test('2222222-hhhhhhh-3333333')

RegExp.$1
"2222222"

RegExp.$2
"hhhhhhh"

RegExp.$3
"3333333"
```

29. #### 引号的使用
- js 使用单引号
- html/css 使用双引号
> 可以保证js中嵌套的html不会冲突

30. #### setInterval的楼层滚动（兼容ie9）

```html
    <a href="###" onClick="scrollFloor('id')">xxx</a>
```
```js
scrollFloor = (id, e) => {
  e.preventDefault();
  let scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
  let target = getHeight(id);
  let interval = scrollTop - target;
  let step = interval / 10;
  // console.log(scrollTop, target, interval)
  let timer = setInterval(() => {
    // console.log('scrolling...')
    if(Math.abs(scrollTop - target) < Math.abs(step)) step = scrollTop - target;
    scrollTop -= step;
    window.scrollBy(0, -step);
    if(interval > 0 ? scrollTop <= target : scrollTop >= target) {
      clearInterval(timer);
      timer = null;
      // console.log('scroll end', scrollTop);
    }
  }, 30);
}
getHeight = (id) => {
  let el = document.getElementById(id);
  if(el)
    return el.offsetTop;
  else
    return 0;
}
```

31. #### 缓动动画Tween.js

```js
/*
 * Tween.js
 * t: current time（当前时间）
 * b: beginning value（初始值）
 * c: change in value（变化量）
 * d: duration（持续时间）
*/
var Tween = {
    Linear: function(t, b, c, d) { return c*t/d + b; },
    Quad: {
        easeIn: function(t, b, c, d) {
            return c * (t /= d) * t + b;
        },
        easeOut: function(t, b, c, d) {
            return -c *(t /= d)*(t-2) + b;
        },
        easeInOut: function(t, b, c, d) {
            if ((t /= d / 2) < 1) return c / 2 * t * t + b;
            return -c / 2 * ((--t) * (t-2) - 1) + b;
        }
    },
    Cubic: {
        easeIn: function(t, b, c, d) {
            return c * (t /= d) * t * t + b;
        },
        easeOut: function(t, b, c, d) {
            return c * ((t = t/d - 1) * t * t + 1) + b;
        },
        easeInOut: function(t, b, c, d) {
            if ((t /= d / 2) < 1) return c / 2 * t * t*t + b;
            return c / 2*((t -= 2) * t * t + 2) + b;
        }
    },
    Quart: {
        easeIn: function(t, b, c, d) {
            return c * (t /= d) * t * t*t + b;
        },
        easeOut: function(t, b, c, d) {
            return -c * ((t = t/d - 1) * t * t*t - 1) + b;
        },
        easeInOut: function(t, b, c, d) {
            if ((t /= d / 2) < 1) return c / 2 * t * t * t * t + b;
            return -c / 2 * ((t -= 2) * t * t*t - 2) + b;
        }
    },
    Quint: {
        easeIn: function(t, b, c, d) {
            return c * (t /= d) * t * t * t * t + b;
        },
        easeOut: function(t, b, c, d) {
            return c * ((t = t/d - 1) * t * t * t * t + 1) + b;
        },
        easeInOut: function(t, b, c, d) {
            if ((t /= d / 2) < 1) return c / 2 * t * t * t * t * t + b;
            return c / 2*((t -= 2) * t * t * t * t + 2) + b;
        }
    },
    Sine: {
        easeIn: function(t, b, c, d) {
            return -c * Math.cos(t/d * (Math.PI/2)) + c + b;
        },
        easeOut: function(t, b, c, d) {
            return c * Math.sin(t/d * (Math.PI/2)) + b;
        },
        easeInOut: function(t, b, c, d) {
            return -c / 2 * (Math.cos(Math.PI * t/d) - 1) + b;
        }
    },
    Expo: {
        easeIn: function(t, b, c, d) {
            return (t==0) ? b : c * Math.pow(2, 10 * (t/d - 1)) + b;
        },
        easeOut: function(t, b, c, d) {
            return (t==d) ? b + c : c * (-Math.pow(2, -10 * t/d) + 1) + b;
        },
        easeInOut: function(t, b, c, d) {
            if (t==0) return b;
            if (t==d) return b+c;
            if ((t /= d / 2) < 1) return c / 2 * Math.pow(2, 10 * (t - 1)) + b;
            return c / 2 * (-Math.pow(2, -10 * --t) + 2) + b;
        }
    },
    Circ: {
        easeIn: function(t, b, c, d) {
            return -c * (Math.sqrt(1 - (t /= d) * t) - 1) + b;
        },
        easeOut: function(t, b, c, d) {
            return c * Math.sqrt(1 - (t = t/d - 1) * t) + b;
        },
        easeInOut: function(t, b, c, d) {
            if ((t /= d / 2) < 1) return -c / 2 * (Math.sqrt(1 - t * t) - 1) + b;
            return c / 2 * (Math.sqrt(1 - (t -= 2) * t) + 1) + b;
        }
    },
    Elastic: {
        easeIn: function(t, b, c, d, a, p) {
            var s;
            if (t==0) return b;
            if ((t /= d) == 1) return b + c;
            if (typeof p == "undefined") p = d * .3;
            if (!a || a < Math.abs(c)) {
                s = p / 4;
                a = c;
            } else {
                s = p / (2 * Math.PI) * Math.asin(c / a);
            }
            return -(a * Math.pow(2, 10 * (t -= 1)) * Math.sin((t * d - s) * (2 * Math.PI) / p)) + b;
        },
        easeOut: function(t, b, c, d, a, p) {
            var s;
            if (t==0) return b;
            if ((t /= d) == 1) return b + c;
            if (typeof p == "undefined") p = d * .3;
            if (!a || a < Math.abs(c)) {
                a = c; 
                s = p / 4;
            } else {
                s = p/(2*Math.PI) * Math.asin(c/a);
            }
            return (a * Math.pow(2, -10 * t) * Math.sin((t * d - s) * (2 * Math.PI) / p) + c + b);
        },
        easeInOut: function(t, b, c, d, a, p) {
            var s;
            if (t==0) return b;
            if ((t /= d / 2) == 2) return b+c;
            if (typeof p == "undefined") p = d * (.3 * 1.5);
            if (!a || a < Math.abs(c)) {
                a = c; 
                s = p / 4;
            } else {
                s = p / (2  *Math.PI) * Math.asin(c / a);
            }
            if (t < 1) return -.5 * (a * Math.pow(2, 10* (t -=1 )) * Math.sin((t * d - s) * (2 * Math.PI) / p)) + b;
            return a * Math.pow(2, -10 * (t -= 1)) * Math.sin((t * d - s) * (2 * Math.PI) / p ) * .5 + c + b;
        }
    },
    Back: {
        easeIn: function(t, b, c, d, s) {
            if (typeof s == "undefined") s = 1.70158;
            return c * (t /= d) * t * ((s + 1) * t - s) + b;
        },
        easeOut: function(t, b, c, d, s) {
            if (typeof s == "undefined") s = 1.70158;
            return c * ((t = t/d - 1) * t * ((s + 1) * t + s) + 1) + b;
        },
        easeInOut: function(t, b, c, d, s) {
            if (typeof s == "undefined") s = 1.70158; 
            if ((t /= d / 2) < 1) return c / 2 * (t * t * (((s *= (1.525)) + 1) * t - s)) + b;
            return c / 2*((t -= 2) * t * (((s *= (1.525)) + 1) * t + s) + 2) + b;
        }
    },
    Bounce: {
        easeIn: function(t, b, c, d) {
            return c - Tween.Bounce.easeOut(d-t, 0, c, d) + b;
        },
        easeOut: function(t, b, c, d) {
            if ((t /= d) < (1 / 2.75)) {
                return c * (7.5625 * t * t) + b;
            } else if (t < (2 / 2.75)) {
                return c * (7.5625 * (t -= (1.5 / 2.75)) * t + .75) + b;
            } else if (t < (2.5 / 2.75)) {
                return c * (7.5625 * (t -= (2.25 / 2.75)) * t + .9375) + b;
            } else {
                return c * (7.5625 * (t -= (2.625 / 2.75)) * t + .984375) + b;
            }
        },
        easeInOut: function(t, b, c, d) {
            if (t < d / 2) {
                return Tween.Bounce.easeIn(t * 2, 0, c, d) * .5 + b;
            } else {
                return Tween.Bounce.easeOut(t * 2 - d, 0, c, d) * .5 + c * .5 + b;
            }
        }
    }
}
Math.tween = Tween;
```

32. #### 拖拽排序的一种实现

```html
<div className="ui-dialog ui-draggable" style={{display: show?'block':'none'}} onMouseMove={this.handleMove} onMouseUp={this.handleUp}>
    <ul className="insert-pool fd-clr ui-portlets">
    {
    	uploadList.map((val, i) => {
    		if(val)
    			return <li className="image-item" key={i} 
        			onMouseDown={this.handleDown.bind(this, i)} 
        			onMouseEnter={this.handleEnter.bind(this, i)} 
        			onMouseLeave={this.handelLeave.bind(this, i)} 
        			>
        		<div title={''}>
        			<img alt="" src={val.src} width={val.width>val.height?64:val.width/val.height*64} height={val.width>val.height?val.height/val.width*64:64}/>
        		</div>
        		<a title="点击删除该图片" href="###" onClick={this.handleDel.bind(this, i)} onMouseDown={this.handleDelDown}></a>
        	</li>
    	})
    }
    </ul>
</div>
```

```js
handleDown = (index, e) => { // 鼠标按下
	e.preventDefault();
	// console.log('down ', index);
	this.drag = true;
	this.dragTag = index;
	let target = document.querySelectorAll('.image-item')[index];
	target && (target.style.borderColor = '#E6E5E3'); // #E6E5E3
}
handleUp = (e) => { // 鼠标放开
	this.drag = false;
	if(this.dragTag !== undefined && this.dragEnd !== -1) {
		// console.log('up ');
		let arr = this.state.uploadList.concat();
		/*------- 换位法 ------*/
		// let tag = arr[this.dragTag];
		// arr[this.dragTag] = arr[this.dragEnd];
		// arr[this.dragEnd] = tag;
		/*--------------------*/
		/*------- 插值法 ------*/
		let start = 0, end = 0, tmp = [];
		let tag = arr.slice(this.dragTag, this.dragTag + 1);
		if(this.dragTag < this.dragEnd) {
			start = this.dragTag;
			end = this.dragEnd;
			tmp = arr.slice(start + 1, end + 1).concat(tag);
		}else {
			start = this.dragEnd;
			end = this.dragTag;
			tmp = tag.concat(arr.slice(start, end));
		}
		arr.splice(start, end - start + 1, ...tmp); // 删除后的数组
		/*--------------------*/
		this.setState({uploadList: arr.concat()});
		// this.dragTag = index;
		let tarEnd = document.querySelectorAll('.image-item')[this.dragEnd];
		tarEnd && (tarEnd.style.borderColor = '#E6E5E3'); // #E6E5E3
	}
	let target = document.querySelectorAll('.image-item')[this.dragTag];
	let box = document.getElementById('sys-ibank');
	box.style.cursor = 'default';
	if(target) {
		target.style.left = 0 + 'px';
		target.style.top = 0 + 'px';
		target.style.opacity = 1;
		target.style.zIndex = 100;
	}
}
handleMove = (e) => { // 鼠标移动
	let index = this.dragTag;
	let target = document.querySelectorAll('.image-item')[index];
	let box = document.getElementById('sys-ibank');
	let p = {
		x: e.clientX - box.offsetLeft - 31 - index*75,
		y: e.clientY - box.offsetTop - 361 + 5,
	}
	// console.log('move ', index, this.drag, p);
	if(target && this.drag) {
		box.style.cursor = 'move';
		target.style.left = p.x + 'px';
		target.style.top = p.y + 'px';
		target.style.opacity = 0.5;
		target.style.zIndex = 10000;
	}
}
handleEnter = (index, e) => {// 鼠标移入
	// index !== this.dragTag && console.log('enter ', index);
	if(index !== this.dragTag) {
		this.dragEnd = index;
		if(this.drag) {
			let tarEnd = document.querySelectorAll('.image-item')[index];
			tarEnd.style.borderColor = '#f40'; // #E6E5E3
		}
	}
}
handelLeave = (index, e) => {// 鼠标移出
	// index !== this.dragTag && console.log('leave ', index);
	if(index !== this.dragTag) {
		let tarEnd = document.querySelectorAll('.image-item')[index];
		tarEnd.style.borderColor = '#E6E5E3'; // #E6E5E3
		this.dragEnd = -1;
	}
}
```

33. #### checkbox的不确定状态
> 通过js设置复选框的indeterminate属性，在标签中设置此属性无效

34. window.location.href的问题

> 问题: 使用window.loaction.href跳转到xxx.com/a/b.html时, 会出现跳转到 xxx.com/c/xxx.com/a/b.html的情况(当前url为xxx.com/c/d.html)

> 解决: 此问题可能是浏览器在使用window.location.href时, 如果值为一个相对路径(需要理解清楚), 会默认替换当前位置
>       因此这里需要使用一个完整的url, 如: http://xxx.com/a/b.html, 即可得到想要的结果.

35. #### fixed滚动的一个例子

```js
// 到底部时不动
window.onscroll = function () {
    let position = 'static';
    let scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
    let docHeight = document.getElementById('content').clientHeight;
    let diff = Math.ceil(docHeight - scrollTop) - parseInt(window.innerHeight ? window.innerHeight : document.body.clientHeight);
    if (diff > 0) {
        position = 'fixed';
    }
    // 给需要fixed的元素设置position的值
}
```

36. #### 通过post跳转页面隐藏参数

```js
var form = document.createElement('form');
form.action = 'xxx.htm';
form.method = 'post';
var input = document.createElement('input');
input1.name = 'a';
input1.value = 222;
form.name = 'param';
document.body.appendChild(form);
param.submit();
```

37. #### rem结合js的使用

> rem是相对于HTML中设置的font-size的

```css
html {
    font-size: 20px; /* 1rem == 20px */
}
h1 {
    font-size: 1rem; /* 即20px */
}
```

```js
/* 兼容ie8及以上 */
// 获取 html 元素  
var html = document.documentElement;

function fontS(){
  // 获取 html 的宽  
  var hW = html.offsetWidth;
  // 计算字体大小，这里是 html 宽的五十分之一  
  var hS = hW / 50;
  html.style.fontSize = hS + "px";
}  
// 给刚开始时的 html 设置字体大小     
fontS();
// 当窗口大小改变时执行函数   
window.onresize = function(){
  fontS();
}
```

38. #### 图片base64字符串的使用说明

- 页面显示
    - 一段图片的base64字符串在页面显示可以放在img标签的src中进行显示
    - 不过字符串前面需要标识: "data:image/png;base64," (png可以更换为相应的格式)

- 获取img中的base64字符串
    - 获取纯图片的base64字符串, 需要去掉标识: str.replace(/^data:image\/.*\;base64\,/, '')

39. #### 字符串数字转为数字

```js
var one = '1'; // '1'
var numberOne = +one; // 1
var one = '1'; // '1'
var numberOne = -one; // -1
```

40. #### 正则的一些使用

- 匹配除换行外的字符: '.'
- 匹配包括换行的所有字符: '\s\S'

> \s 匹配空白字符, \S 匹配非空白字符

- 字符串中 '\n' 无效: 在 '\n' 前后加空格 --> ' \n '


41. #### 正则匹配html中的内容

> 一种简单的实现

> 匹配html中的内容, 去除注释/style/script, 并保留一个换行

```js
html.replace(/(<\!.*-->)|(<script.*?>[\s\S]*?<\/script>)|(<style.*?>[\s\S]*?<\/style>)|(<.*?>)/ig, '').replace(/\s{2,}/g, ' \n ')
```

42. #### 颜色的一些操作

- 生成随机颜色

```js
/* ====== 简单的例子 ====== */
function getNum (max, min) { // 生成包含min 和 max的数
    return Math.round(Math.random() * (max - min) + min);
}

function getColor () {
    return `rgba(${getNum(0, 255)}, ${getNum(0, 255)}, ${getNum(0, 255)}, ${getNum(0, 1)})`
}

/* ====== 大神的例子(经过了转化) ====== */
function getSoftColor (x = 0) { // x: 0 - 2 * Math.PI
    let c = Math.cos;
    let t = Math.PI * 2;
    let r = Math.round;
    x = limitNum(x); // 过滤部分颜色
    return `rgb(${r(c(x) * 127 + 128)}, ${r(c(x + t / 3) * 127 + 128)}, ${r(c(x + t / 3 * 2) * 127 + 128)})`;
}
// 限定范围, 去掉较暗的颜色
function limitNum (num, lit = Math.PI / 16) {
    const p = Math.PI;
    const points = [p / 3, p, p / 3 * 5];
    if(num > p * 2) {
        return 0;
    }
    for(let i = 0, len = points.length; i < len; i++) {
        const item = points[i];
        if(num > item - lit && num < item + lit)  {
            return item + lit;
        }
    }
    return num;
}
```

- 颜色渐变

```js
// 将颜色转为数组
function parseColor (color) { // 返回一个十进制表示的颜色数组
    function getSub (index, len = 1) {
        return color.substr(index, len)
    }
    var testHex = '\\d|1{0,1}\\d\\d|2[0-4]\\d|25[0-5]'; // 匹配 0 - 255 之间的数
    var regHex = new RegExp('^rgb\\(\\s*(' + testHex + ')\\s*\\,\\s*(' + testHex + ')\\s*\\,\\s*(' + testHex + ')\\s*\\)$', 'i');
    if(/^#[0-9a-f]{3}$/i.test(color)) { // 颜色格式为: #333
        let r = getSub(1)
        let g = getSub(2)
        let b = getSub(3)
        return [parseInt(r + r, 16), parseInt(g + g, 16), parseInt(b + b, 16)];
    }else if(/^#[0-9a-f]{6}$/i.test(color)) { // 颜色格式为: #ffffff
        return [parseInt(getSub(1, 2), 16), parseInt(getSub(2, 2), 16), parseInt(getSub(3, 2), 16)];
    }else if(regHex.test(color)) {
        let result = color.match(regHex);
        return [Number(RegExp.$1), Number(RegExp.$2), Number(RegExp.$3)]
    }else {
        console.log('颜色: ' + color + ' 格式不对.');
    }
}
function addColor(color, max = 255, min = 0) {
    let arr = parseColor(color);
    arr = arr.map((val, i) => {
        return val >= max ? min : ++val
    });
    return `rgb(${arr[0]}, ${arr[1]}, ${arr[2]})`
}
```

43. #### js进制转换

- 任意转换为十进制

```js
parseInt('11', 2) // 转为2进制
parseInt('af', 16) // 转为16进制
```

- 十进制转为其他进制

```js
(152).toString(2)  // "10011000" ;  先用括号将152转换“包”成一个对象， 或者如下写法;
152..toString(2)   // 这里第一个点将152转换成float类型的小数，第二个点是引出对象方法;
152..toString(16)  // "98" : 十进制转16进制
152..toString(32)  // "4o" ：十提制转32进制
```