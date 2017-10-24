---
title: es6 笔记(一)
date: 2017-10-24 11:36:26
tags:
- 前端
- es6
categories:
- 笔记
- 前端
- es6
---

[http://es6.ruanyifeng.com/#README](http://es6.ruanyifeng.com/#README)

<!--more-->

#### ES6 与 ES2015 的关系
在每年的6月份，标准委员会会发布一个新版本，如ECMAScript 2015，并在下一年的6月之前不断修改


#### Babel
Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在现有环境执行。

---

#### ES6的内容

- ##### let
    > 声明块级作用域的变量
    
    - 一个例子：
    
    ```
    var a = [];
    for (let i = 0; i < 10; i++) {
      a[i] = function () {
        console.log(i);
      };
    }
    a[6](); // 6
    ```
    > 每一次的循环都是一个新的i
    
    - 在for中使用let，循环语句为父作用域，循环体内部为单独的子作用域
    - 不存在声明提前
    - 暂时性死区
    > **ES6明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。**
    
    ```
    var tmp = 123;

    if (true) {
      tmp = 'abc'; // ReferenceError
      let tmp;
    }
    ```
    - 不允许重复声明
    
- ##### 块级作用域
    - 外层作用域与内层作用域相互独立
    - 函数不能再块级作用域中声明，函数只能在顶层作用域和函数作用域之中声明
    
- ##### const
    - 声明一个常量，常量不可改变 --> 变量一旦声明，必须立即初始化
    - 在块级作用域内有效
    - 与let类似
    - 本质：**const声明的变量指向的内存地址不可变，对于引用类型的数据，变量指向的内存地址，保存的只是一个指针，而它指向的数据则不能控制**
    > 如要想将对象冻结，应该使用Object.freeze

    ```
    const foo = Object.freeze({});
    ```
    > 将对象的属性冻结，eg：将对象彻底冻结的函数
    
    ```
    var constantize = (obj) => {
      Object.freeze(obj);
      Object.keys(obj).forEach( (key, i) => {
        if ( typeof obj[key] === 'object' ) {
          constantize( obj[key] );
        }
      });
    };
    ```
    
- ##### 顶层对象的属性
    - 顶层对象，在浏览器环境指的是window对象，在Node指的是global对象。ES5之中，顶层对象的属性与全局变量是等价的。
    > **顶层对象的属性与全局变量挂钩，被认为是JavaScript语言最大的设计败笔之一。**

    - let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。

- ##### 变量的解构赋值

1. 数组的解构赋值
    
    - 数组的结构赋值：ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构
    ```
    let [a, b, c] = [1, 2, 3];
    ```
    
    - 如果解构不成功，变量的值就等于undefined。
    
    ```
    let [foo] = [];
    let [bar, foo] = [1];
    ```

    - 不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。
    
    ```
    let [x, y] = [1, 2, 3];
    x // 1
    y // 2
    ```
    - 如果等号的右边不是数组,将会报错
    
    ```
    // 报错
    let [foo] = 1;
    let [foo] = false;
    let [foo] = NaN;
    let [foo] = undefined;
    let [foo] = null;
    let [foo] = {};
    ```
    - 只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。
    
- 默认值
    
    - ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，如果一个数组成员不严格等于undefined，默认值是不会生效的。
    ```
    let [x, y = 'b'] = ['a']; // x='a', y='b'
    let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
    ```
    - 如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。
    ```
    function f() {
      console.log('aaa');
    }
    
    let [x = f()] = [1];
    ```
2. 对象的解构赋值

```
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```
- 对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性**没有次序**，变量必须与属性**同名**，才能取到正确的值。
- 
- 如果变量名与属性名不一致，必须写成下面这样。
```
var { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"
```
> foo 是匹配的模式，baz才是变量，真正被赋值的是变量baz，而不是模式foo。

- 对象的解构也可以指定默认值
```
var {x:y = 3} = {x: 5};
y // 5
```
> 默认值生效的条件是，对象的属性值严格等于undefined。

3. 字符串的解构赋值

- 此时，字符串被转换成了一个类似数组的对象。
```
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```

- 类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。
```
let {length : len} = 'hello';
len // 5
```

4. 数值和布尔值的解构赋值

- 解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。

- 解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。

5. 函数参数的解构赋值

- 函数的参数也可以使用解构赋值。
```
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3
```

- 函数参数的解构也可以使用默认值。
```
function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```

6. 圆括号问题

- 不能使用圆括号
    - 变量声明语句中，不能带有圆括号。
    - 函数参数中，模式不能带有圆括号。
    - 赋值语句中，不能将整个模式，或嵌套模式中的一层，放在圆括号之中。
- 可以使用圆括号的情况只有一种：赋值语句的非模式部分，可以使用圆括号。
```
[(b)] = [3]; // 正确
({ p: (d) } = {}); // 正确
[(parseInt.prop)] = [3]; // 正确
```

7. 用途
- 交换变量的值
```
let x = 1;
let y = 2;
[x, y] = [y, x];
```
- 从函数返回多个值
```
// 返回一个数组

function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```
- 函数参数的定义
```
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```
- 提取JSON数据
```
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```
- 函数参数的默认值
```
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};
```
- 遍历Map结构
```
var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
    console.log(key + "is" + value);
}
// first is hello
// second is world
```
- **输入模块的指定方法**
```
const { SourceMapConsumer, SourceNode } = require("source-map");
```

- ##### 字符串

- 字符的 Unicode 表示法
    - JavaScript 允许采用\uxxxx形式表示一个字符，其中xxxx表示字符的 Unicode 码点。只要将码点放入大括号，就能正确解读该字符。
```
    "\u{20BB7}"
// "𠮷"

"\u{41}\u{42}\u{43}"
// "ABC"

let hello = 123;
hell\u{6F} // 123

'\u{1F680}' === '\uD83D\uDE80'
// true
```

- codePointAt()
    - JavaScript内部，字符以UTF-16的格式储存，每个字符固定为2个字节。对于那些需要4个字节储存的字符（Unicode码点大于0xFFFF的字符），JavaScript会认为它们是两个字符。
```
var s = "𠮷";

s.length // 2
s.charAt(0) // ''
s.charAt(1) // ''
s.charCodeAt(0) // 55362
s.charCodeAt(1) // 57271
```

- ES6提供了codePointAt方法，能够正确处理4个字节储存的字符，返回一个字符的码点。
```
var s = '𠮷a';

s.codePointAt(0) // 134071
s.codePointAt(1) // 57271

s.codePointAt(2) // 97
```

- String.fromCodePoint()
    - 用于从码点返回对应字符， 可以识别大于0xFFFF的字符
- 字符串的遍历器接口
    - 可以识别大于0xFFFF的字符
- at()
- normalize()
    - 用来将字符的不同表示方法统一为同样的形式，这称为Unicode正规化
- includes(), startsWith(), endsWith() 
    - includes()：返回布尔值，表示是否找到了参数字符串。
    - startsWith()：返回布尔值，表示参数字符串是否在源字符串的头部。
    - endsWith()：返回布尔值，表示参数字符串是否在源字符串的尾部。
    > 使用第二个参数n时，endsWith的行为与其他两个方法有所不同。它针对前n个字符，而其他两个方法针对从第n个位置直到字符串结束。

- repeat()
    - repeat方法返回一个新字符串，表示将原字符串重复n次。

- padStart()，padEnd()
    - 补全字符串长度
    - 第一个参数用来指定字符串的最小长度，第二个参数是用来补全的字符串
    - 如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串

- 模板字符串
    - 用反引号（`）标识
    - 表示多行字符串，所有的空格和缩进都会被保留在输出之中
    - 变量名写在${}之中
    - 大括号内部可以放入任意的JavaScript表达式，可以进行运算，以及引用对象属性
- 标签模板
    - 标签模板其实不是模板，而是函数调用的一种特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数
    ```
    alert`123`
    // 等同于
    alert(123)
    ```
    - 如果模板字符里面有变量，就不是简单的调用了，而是会将模板字符串先处理成多个参数，再调用函数 **???**
    
- String.raw()
    - 往往用来充当模板字符串的处理函数，返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，对应于替换变量后的模板字符串。

- ##### Class
    - 与es5语法对比
    
    es5
    ```
    function Point(x, y) {
      this.x = x;
      this.y = y;
    }
    
    Point.prototype.toString = function () {
      return '(' + this.x + ', ' + this.y + ')';
    };
    
    var p = new Point(1, 2);
    ```
    es6
    ```
    //定义类
    class Point {
        constructor(x, y) {//相当于es5的构造函数
            this.x = x;
            this.y = y;
        }
        
        toString() {
            return '(' + this.x + ',' + this.y + ')';
        }
    }
    ```
    - 类的数据类型就是函数，类本身就指向构造函数
    - 类的所有方法都定义在类的prototype属性上面
    ```
    class Point {
      constructor(){
        // ...
      }
    
      toString(){
        // ...
      }
    
      toValue(){
        // ...
      }
    }
    
    // 等同于
    
    Point.prototype = {
      toString(){},
      toValue(){}
    };
    ```
    - 类的内部所有定义的方法，都是不可枚举的（non-enumerable）
- constructor
    - 类的默认方法，通过new生成实例时，自动调用该方法
    - 一个类必须有constructor方法，否则会默认添加一个空的
    - 默认返回实例对象，即this
    - 不使用new是没法调用的，会报错
    
- 实例
    - 使用new生成实例
    - 实例的原型都是**.prototype，所以__proto__属性是相等的

- 不存在声明提升
    - class不存在变量提升

- class表达式
    - 定义一个MyClass类
    ```
    const MyClass = class Me {
      getClassName() {
        return Me.name;
      }
    };
    ```
    Me只是内部代码可用，可以省略
    - 使用class表达式，可以写出立即执行的class
    ```
    let person = new class {
      constructor(name) {
        this.name = name;
      }
    
      sayName() {
        console.log(this.name);
      }
    }('张三');
    
    person.sayName(); // "张三"
    ```
- 私有方法
- this的指向

- extends


