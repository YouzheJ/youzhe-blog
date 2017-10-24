---
title: es6 笔记(四)
date: 2017-10-24 11:41:22
tags:
- 前端
- es6
categories:
- 笔记
- 前端
- es6
---

#### Promise 对象

<!--more-->
    
- 特点：

    - 对象的状态不受外界影响
        三种状态，只有异步的结果可以决定当前的状态：
        - Pending：进行中
        - Resolved：已完成，又称Fullfilled
        - Rejected：已失败
    - 一旦状态改变就不会再变，任何时候都可以得到这个结果
        - 从Pending到Resolved
        - 从Pending到Rejected
    
- 缺点：

    - 无法取消promise
    - Promise内部抛出的错误，不会反应到外部
    - 当处于Pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）


- 基本用法
    - Promise对象是一个构造函数，用来生成Promise实例
    ```
    var promise = new Promise(function(resolve, reject) {
      // ... some code
    
      if (/* 异步操作成功 */){
        resolve(value);
      } else {
        reject(error);
      }
    });
    ```
    - Promise实例生成以后，可以用then方法分别指定Resolved状态和Reject状态的回调函数
    ```
    promise.then(function(value) {
      // success
    }, function(error) {
      // failure
    });
    ```
    > 第二个函数是可选的，不一定要提供
    
    - Promise新建后就会立即执行
    ```
    let promise = new Promise(function(resolve, reject) {
      console.log('Promise');
      resolve();
    });
    
    promise.then(function() {
      console.log('Resolved.');
    });
    
    console.log('Hi!');
    
    // Promise
    // Hi!
    // Resolved
    ```
    - 下面是异步加载图片的例子
    ```
    function loadImageAsync(url) {
      return new Promise(function(resolve, reject) {
        var image = new Image();
    
        image.onload = function() {
          resolve(image);
        };
    
        image.onerror = function() {
          reject(new Error('Could not load image at ' + url));
        };
    
        image.src = url;
      });
    }
    ```
    - 一个用Promise对象实现的Ajax操作的例子
    ```
    var getJSON = function(url) {
      var promise = new Promise(function(resolve, reject){
        var client = new XMLHttpRequest();
        client.open("GET", url);
        client.onreadystatechange = handler;
        client.responseType = "json";
        client.setRequestHeader("Accept", "application/json");
        client.send();
    
        function handler() {
          if (this.readyState !== 4) {
            return;
          }
          if (this.status === 200) {
            resolve(this.response);
          } else {
            reject(new Error(this.statusText));
          }
        };
      });
    
      return promise;
    };
    
    getJSON("/posts.json").then(function(json) {
      console.log('Contents: ' + json);
    }, function(error) {
      console.error('出错了', error);
    });
    ```
    - resolve函数的参数除了正常的值以外，还可能是另一个Promise实例，表示异步操作的结果有可能是一个值，也有可能是另一个异步操作
    
- Promise.prototype.then()
    - then方法返回的是一个新的Promise实例

- Promise.prototype.catch()
    - 是.then(null, rejection)的别名，用于指定发生错误时的回调函数
    - then方法指定的回调函数，如果运行中抛出错误，也会被catch方法捕获
    - 如果Promise状态已经变成Resolved，再抛出错误是无效的
    - Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个catch语句捕获
    ```
    getJSON('/post/1.json').then(function(post) {
      return getJSON(post.commentURL);
    }).then(function(comments) {
      // some code
    }).catch(function(error) {
      // 处理前面三个Promise产生的错误
    });
    ```
    - 一般来说，不要在then方法里面定义Reject状态的回调函数（即then的第二个参数），总是使用catch方法
    - catch方法返回的还是一个 Promise 对象，因此后面还可以接着调用then方法
    
- Promise.all()
    - 用于将多个Promise实例，包装成一个新的Promise实例
    ```
    var p = Promise.all([p1, p2, p3]);
    ```
    - 如果有一个参数不是Promise对象的实例，就会调用Promise.resolve方法，将参数转为Promise实例
    - p的状态由p1、p2、p3决定，分成两种情况
        - 只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数
        - 只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数
        > 这全部实例的状态都变成fulfilled，或者其中有一个变为rejected，才会调用Promise.all方法后面的回调函数
        
    - 应用例子
        - 多个异步请求完成才触发后面的回调

- Promise.race()
    - 同样是将多个Promise实例，包装成一个新的Promise实例
    ```
    var p = Promise.race([p1, p2, p3]);
    ```
    - 只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数

- Promise.resolve()
    - 将现有对象转为Promise对象

- Promise.reject()
    - 返回一个新的 Promise 实例，该实例的状态为rejected

#### Iterator
- 概念
    - 它是一种接口，为各种不同的数据结构提供统一的访问机制
    - 作用：
        - 为各种数据结构，提供一个统一的、简便的访问接口
        - 使得数据结构的成员能够按某种次序排列
        - ES6创造了一种新的遍历命令for...of循环，Iterator接口主要供for...of消费
    - Iterator的遍历过程：
        - 创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象
        - 不断调用指针对象的next方法，直到它指向数据结构的结束位置
        - 每一次调用next方法，都会返回数据结构的当前成员的信息，就是返回一个包含value和done两个属性的对象。其中，value属性是当前成员的值，done属性是一个布尔值，表示遍历是否结束

- 数据结构的默认Iterator接口
    - 一个数据结构只要具有Symbol.iterator属性，就可以认为是“可遍历的”（iterable）
    - Symbol.iterator属性本身是一个函数，就是当前数据结构默认的遍历器生成函数
    - 在ES6中，有三类数据结构原生具备Iterator接口：数组、某些类似数组的对象、Set和Map结构

- 调用Iterator接口的场合
    - 解构赋值
    - 扩展运算符
    - yield*
    - 其他场合：由于数组的遍历会调用遍历器接口，所以任何接受数组作为参数的场合，其实都调用了遍历器接口

- 字符串的Iterator接口
    - 字符串是一个类似数组的对象，也原生具有Iterator接口

- 遍历器对象的return()，throw()
    - return方法的使用场合是，for...of循环提前退出

- for...of循环
    - 遍历所有数据结构的统一的方法
    - 具有iterator接口，就可以用for...of循环遍历它的成员
    - for...of循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象（比如arguments对象、DOM NodeList 对象）、后文的 Generator 对象，以及字符串
    - ##### 数组：
        - JavaScript原有的for...in循环，只能获得对象的键名，不能直接获取键值。ES6提供for...of循环，允许遍历获得键值
        - for...of循环调用遍历器接口，数组的遍历器接口只返回具有数字索引的属性。这一点跟for...in循环也不一样
    - ##### Set和Map：
        - 遍历的顺序是按照各个成员被添加进数据结构的顺序
        - Set 结构遍历时，返回的是一个值，而 Map 结构遍历时，返回的是一个数组，该数组的两个成员分别为当前 Map 成员的键名和键值
    - ##### 类数组对象：
    - ##### 对象：
        - 必须部署了 Iterator 接口后才能使用
        - 一种解决方法是，使用Object.keys方法将对象的键名生成一个数组，然后遍历这个数组
        ```
        for (var key of Object.keys(someObject)) {
          console.log(key + ': ' + someObject[key]);
        }
        ```
        - 另一个方法是使用 Generator 函数将对象重新包装一下
    
#### Generator

    
#### 扩展运算符

- ... : 展开数组
```
let arr = ['b', 'c'];
['a', ...arr, 'd']
// ['a', 'b', 'c', 'd']
```

    
#### async 函数