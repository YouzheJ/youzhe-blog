---
title: es6 笔记(二)
date: 2017-10-24 11:38:51
tags:
- 前端
- es6
categories:
- 笔记
- 前端
- es6
---

#### 正则

<!--more-->

- RegExp 构造函数
    - es6 中第一个参数是正则，第二个参数 修饰符 会替换原有的修饰符
- 字符串的正则方法
    - 字符串对象共有4个方法，可以使用正则表达式：match()、replace()、search()和split()
- u修饰符
- y修饰符
- sticky属性
- flags属性：返回修饰符
    > source属性返回正则的正文

#### 数值
- 二进制、八进制
    - 分别用前缀0b（或0B）和0o（或0O）表示
    - 使用Number转为十进制
- Number.isFinite()用来检查一个数值是否为有限的（finite）
- Number.isNaN()用来检查一个值是否为NaN
    - 只对数值有效，Number.isFinite()对于非数值一律返回false, Number.isNaN()只有对于NaN才返回true，非NaN一律返回false
- Number.parseInt(), Number.parseFloat()
    - ES6将全局方法parseInt()和parseFloat()，移植到Number对象上
- Number.isInteger()
    - 用来判断一个值是否为整数
    - **注意**：在JavaScript内部，整数和浮点数是同样的储存方法，所以3和3.0被视为同一个值 
- Number.EPSILON
    - 一个极小的常量，为浮点数计算，设置一个误差范围。我们知道浮点数计算是不精确的。
- Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER
    - JavaScript能够准确表示的整数范围在-2^53到2^53之间（不含两个端点），超过这个范围，无法精确表示这个值
    - 这两个常量，用来表示这个范围的上下限
    - Number.isSafeInteger()则是用来判断一个整数是否落在这个范围之内

- **Math对象的扩展**
    - Math.trunc
        - 用于去除一个数的小数部分，返回整数部分
        - 对于空值和无法截取整数的值，返回NaN
    - Math.sign
        - 方法用来判断一个数到底是正数、负数、还是零。
        - 它会返回五种值。
            - 参数为正数，返回+1；
            - 参数为负数，返回-1；
            - 参数为0，返回0；
            - 参数为-0，返回-0;
            - 其他值，返回NaN。
    - Math.cbrt()
        - Math.cbrt方法用于计算一个数的立方根
    - Math.clz32()
        - JavaScript的整数使用32位二进制形式表示，Math.clz32方法返回一个数的32位无符号整数形式有多少个前导0
    - Math.imul()
        - Math.imul方法返回两个数以32位带符号整数形式相乘的结果，返回的也是一个32位的带符号整数
    - Math.fround()
        - Math.fround方法返回一个数的单精度浮点数形式
    - Math.hypot()
        - Math.hypot方法返回所有参数的平方和的平方根
    - 对数方法
    - 三角函数方法
    - 指数运算符
        - **
        ```
        a **= 3 // a = a * a * a
        ```
        
#### 数组
- Array.from()
    - 用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括ES6新增的数据结构Set和Map）
    - Array.from还可以接受第二个参数，作用类似于数组的map方法，用来对每个元素进行处理，将处理后的值放入返回的数组
    - 第三个参数，用来绑定this
    - 其他应用：将**字符串转为数组**，然后返回字符串的长度。因为它能正确处理各种**Unicode**字符，可以避免JavaScript将大于\uFFFF的Unicode字符，算作两个字符的bug
- Array.of()
    ```
    Array.of(3, 11, 8) // [3,11,8] 
    ```
- copyWithin()
    - 数组实例的copyWithin方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会**修改当前数组**
    - 接受三个参数
        - target（必需）：从该位置开始替换数据。
        - start（可选）：从该位置开始读取数据，默认为0。如果为负值，表示倒数。
        - end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数
    ```
    // 将3号位复制到0号位
    [1, 2, 3, 4, 5].copyWithin(0, 3, 4)
    // [4, 2, 3, 4, 5]
    
    // -2相当于3号位，-1相当于4号位
    [1, 2, 3, 4, 5].copyWithin(0, -2, -1)
    // [4, 2, 3, 4, 5]
    ```
    
- find()
    - 用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，然后返回该成员。如果没有符合条件的成员，则返回undefined
    - find方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组
- findIndex()
    - 与find方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1
    - 这两个方法都可以发现NaN，弥补了数组的IndexOf方法的不足
- fill()
    - 用于填充一个数组
    - 第二个和第三个参数，用于指定填充的起始位置和结束位置
    ```
    ['a', 'b', 'c'].fill(7, 1, 2)
    // ['a', 7, 'c']
    ```

- entries()，keys()和values()
    - 用于遍历数组,entries() 对键值对进行遍历
    ```
    for (let index of ['a', 'b'].keys()) {
      console.log(index);
    }
    // 0
    // 1
    
    for (let elem of ['a', 'b'].values()) {
      console.log(elem);
    }
    // 'a'
    // 'b'
    
    for (let [index, elem] of ['a', 'b'].entries()) {
      console.log(index, elem);
    }
    // 0 "a"
    // 1 "b"
    ```
- includes()
    - 返回一个布尔值，表示某个数组是否包含给定的值，与字符串的includes方法类似。该方法属于ES7，但Babel转码器已经支持
    - 第二个参数表示搜索的起始位置，默认为0。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为-4，但数组长度为3），则会重置为从0开始
- 数组的空位
    - 空位不是undefined，一个位置的值等于undefined，依然是有值的。空位是没有任何值
    - ES6则是明确将空位转为undefined
    - 由于空位的处理规则非常不统一，所以建议避免出现空位


#### 函数的扩展
- 函数参数的默认值
    - ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面
    - 与结构赋值默认值结合使用
        - 参数是对象，结构默认值
        ```
        function foo({x, y = 5}) {
          console.log(x, y);
        }
        
        foo({}) // undefined, 5
        foo({x: 1}) // 1, 5
        foo({x: 1, y: 2}) // 1, 2
        foo() // TypeError: Cannot read property 'x' of undefined
        ```
        - 双重默认值
        ```
        function fetch(url, { method = 'GET' } = {}) {
          console.log(method);
        }
        
        fetch('http://example.com')
        // "GET"
        ```
    - 参数的位置：定义了默认值的参数，应该是函数的尾参数，如果非尾部的参数设置默认值，实际上这个参数是没法省略的
    - 传入undefined，将触发该参数等于默认值，null则没有这个效果
    - length属性
        - length属性的返回值，等于函数的参数个数减去指定了默认值的参数个数
        - 含义是，该函数预期传入的参数个数。某个参数指定默认值以后，预期传入的参数个数就不包括这个参数了。同理，rest参数也不会计入length属性
        - 如果设置了默认值的参数不是尾参数，那么length属性也不再计入后面的参数了
    - 作用域：一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的
    - 应用：
        - 利用参数默认值，可以指定某一个参数不得省略，如果省略就抛出一个错误
        - 将参数默认值设为undefined，表明这个参数是可以省略的


- rest参数
    
    - 形式："...变量名"，搭配的变量是一个数组
    - 用于获取函数的多余参数，这样就不需要使用arguments对象了，在创建函数时使用
    ```
    // arguments变量的写法
    function sortNumbers() {
      return Array.prototype.slice.call(arguments).sort();
    }
    
    // rest参数的写法
    const sortNumbers = (...numbers) => numbers.sort();
    ```
    - 注意：
    1. rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错
    ```
    // 报错
    function f(a, ...b, c) {
      // ...
    }
    ```
    2. 函数的length属性，不包括 rest 参数
    ```
    (function(a) {}).length  // 1
    (function(...a) {}).length  // 0
    (function(a, ...b) {}).length  // 1
    ```

- 扩展运算符： ... 
    - 好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列
    - 主要用于函数调用
    - 扩展运算符与正常的函数参数可以结合使用，非常灵活
    ```
    function f(v, w, x, y, z) { }
    var args = [0, 1];
    f(-1, ...args, 2, ...[3]);
    ```
    - 替代数组的apply方法
    - 应用：
        - 合并数组
        ```
        // ES5
        [1, 2].concat(more)
        // ES6
        [1, 2, ...more]
        ```
        - 与结构赋值结合，只能放在参数的最后一位
        ```
        const [first, ...rest] = [1, 2, 3, 4, 5];
        first // 1
        rest  // [2, 3, 4, 5]
        
        const [first, ...rest] = [];
        first // undefined
        rest  // []
        
        const [first, ...rest] = ["foo"];
        first  // "foo"
        rest   // []
        ```
        - 函数的返回值：JavaScript的函数只能返回一个值，如果需要返回多个值，只能返回数组或对象。扩展运算符提供了解决这个问题的一种变通方法
        - 字符串
            - 扩展运算符还可以将字符串转为真正的数组。
            ```
            [...'hello']
            // [ "h", "e", "l", "l", "o" ]
            ```
            > 上面的写法，有一个重要的好处，那就是能够正确识别32位的Unicode字符
            
        - 实现了Iterator接口的对象：任何Iterator接口的对象，都可以用扩展运算符转为真正的数组

- 严格模式
    - 规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错

- name属性：返回该函数的函数名
    - 这个属性早就被浏览器广泛支持，但是直到 ES6，才将其写入了标准
    - Function构造函数返回的函数实例，name属性的值为anonymous
    - bind返回的函数，name属性值会加上bound前缀

- 箭头函数
    ```
    var f = v => v;//v是参数
    ```
    等同于
    ```
    var f = function(v) {
      return v;
    };
    ```
    如果箭头函数不需要参数或需要多个参数，就使用一个圆括号代表参数部分。
    ```
    var f = () => 5;
    // 等同于
    var f = function () { return 5 };
    
    var sum = (num1, num2) => num1 + num2;
    // 等同于
    var sum = function(num1, num2) {
      return num1 + num2;
    };
    ```
    如果代码块多分多于一条语句，就要使用大括号，并使用return返回
    ```
    var sum = (num1, num2) => { return num1 + num2; }
    ```
    如果直接返回一个对象，需要在对象外面加上括号
    ```
    var getTempItem = id => ({ id: id, name: "Temp" });
    ```
    结合变量解构
    ```
    const full = ({ first, last }) => first + ' ' + last;
    
    // 等同于
    function full(person) {
      return person.first + ' ' + person.last;
    }
    ```
    注意：
    1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象，即this的指向是固定的
    ```
    function foo() {
      setTimeout(() => {
        console.log('id:', this.id);
      }, 100);
    }
    
    var id = 21;
    
    foo.call({ id: 42 });
    // id: 42
    ```
    > 箭头函数中没有自己的this，而是引用外层的this，所以不能使用call()/apply()/bind()改变this的指向
    > 此外：arguments/super/new.target 都是指向外层函数的对应变量
    
    2. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误
    3. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替
    4. 不可以使用yield命令，因此箭头函数不能用作Generator函数
    
    - 嵌套的箭头函数
    ```
    let insert = (value) => ({into: (array) => ({after: (afterValue) => {
      array.splice(array.indexOf(afterValue) + 1, 0, value);
      return array;
    }})});
    
    insert(2).into([1, 3]).after(1); //[1, 2, 3]
    ```

- 绑定this

    - es7：函数绑定运算符是并排的两个双冒号（::），双冒号左边是一个对象，右边是一个函数。该运算符会自动将左边的对象，作为上下文环境（即this对象），绑定到右边的函数上面。
    ```
    foo::bar;
    // 等同于
    bar.bind(foo);
    
    foo::bar(...arguments);
    // 等同于
    bar.apply(foo, arguments);
    
    const hasOwnProperty = Object.prototype.hasOwnProperty;
    function hasOwn(obj, key) {
      return obj::hasOwnProperty(key);
    }
    ```
    - 如果双冒号左边为空，右边是一个对象的方法，则等于将该方法绑定在该对象上面
    ```
    var method = obj::obj.foo;
    // 等同于
    var method = ::obj.foo;
    
    let log = ::console.log;
    // 等同于
    var log = console.log.bind(console);
    ```
    - 由于双冒号运算符返回的还是原对象，因此可以采用链式写法
    ```
    // 例一
    import { map, takeWhile, forEach } from "iterlib";
    
    getPlayers()
    ::map(x => x.character())
    ::takeWhile(x => x.strength > 100)
    ::forEach(x => console.log(x));
    
    // 例二
    let { find, html } = jake;
    
    document.querySelectorAll("div.myClass")
    ::find("p")
    ::html("hahaha");
    ```
        
- 尾调用的优化
    - 某个函数的最后一步是调用另一个函数
    ```
    function f(x){
      return g(x);
    }
    ```
    - 尾调用不一定出现在函数尾部，只要是最后一步操作即可
    ```
    function f(x) {
      if (x > 0) {
        return m(x)
      }
      return n(x);
    }
    ```
- 尾调用优化：即只保留内层函数的调用帧
    - ES6 的尾调用优化只在严格模式下开启，正常模式是无效的
- 尾递归：函数调用自身，称为递归。如果尾调用自身，就称为尾递归
    - 柯里化（currying），意思是将多参数的函数转换成单参数的形式
- 函数参数的尾逗号
    - ES2017 允许函数的最后一个参数有尾逗号（trailing comma）
    

#### 对象的扩展

- 属性的简洁表示法，方法也可以
    - 允许直接写入变量和函数
    ```
    var birth = '2000/01/01';

    var Person = {
    
      name: '张三',
    
      //等同于birth: birth
      birth,
    
      // 等同于hello: function ()...
      hello() { console.log('我的名字是', this.name); }
    
    };
    
    // 这种写法用于函数的返回值，将会非常方便。
    function getPoint() {
      var x = 1;
      var y = 10;
      return {x, y};
    }
    
    getPoint()
    // {x:1, y:10}
    ```
    
    > 简写的属性名总是字符串
    
    ```
    var obj = {
      class () {}
    };// 不会报错
    
    // 等同于
    
    var obj = {
      'class': function() {}
    };
    ```
    
- 属性名表达式
    - js定义对象的属性：方法一是直接用标识符作为属性名，方法二是用表达式作为属性名，这时要将表达式放在方括号之内
    ```
    var lastWord = 'last word';
    
    var a = {
      'first word': 'hello',
      [lastWord]: 'world'
    };
    
    a['first word'] // "hello"
    a[lastWord] // "world"
    a['last word'] // "world"
    ```
    - 方法名也可
    ```
    let obj = {
      ['h' + 'ello']() {
        return 'hi';
      }
    };
    
    obj.hello() // hi
    ```
    - 属性名表达式与简洁表示法，不能同时使用，会**报错**。
    ```
    // 报错
    var foo = 'bar';
    var bar = 'abc';
    var baz = { [foo] };
    
    // 正确
    var foo = 'bar';
    var baz = { [foo]: 'abc'};
    ```
    - 属性名表达式如果是一个**对象**，默认情况下会自动将对象转为字符串[object Object]，这一点要特别小心
    
- 方法的name属性
    - 对象方法也是函数，因此也有name属性，返回函数名
    - 如果对象的方法使用了取值函数（getter）和存值函数（setter），则name属性不是在该方法上面，而是该方法的属性的描述对象的get和set属性上面，返回值是方法名前加上get和set。
    ```
    const obj = {
      get foo() {},
      set foo(x) {}
    };
    
    obj.foo.name
    // TypeError: Cannot read property 'name' of undefined
    
    const descriptor = Object.getOwnPropertyDescriptor(obj, 'foo');
    
    descriptor.get.name // "get foo"
    descriptor.set.name // "set foo"
    ```
    - 如果对象的方法是一个 Symbol 值，那么name属性返回的是这个 Symbol 值的描述。
    ```
    const key1 = Symbol('description');
    const key2 = Symbol();
    let obj = {
      [key1]() {},
      [key2]() {},
    };
    obj[key1].name // "[description]"
    obj[key2].name // ""
    ```
    - 特殊：bind方法创造的函数，name属性返回bound加上原函数的名字；Function构造函数创造的函数，name属性返回anonymous
    
- Object.is()
    - 用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致
    - 不同之处只有两个：一是+0不等于-0，二是NaN等于自身
    ```
    Object.is('foo', 'foo')
    // true
    Object.is({}, {})
    // false
    
    +0 === -0 //true
    NaN === NaN // false
    
    Object.is(+0, -0) // false
    Object.is(NaN, NaN) // true
    ```
    
- Object.assign()
    - 用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）
    - 第一个参数是目标对象，后面的参数都是源对象，如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性
    ```
    var target = { a: 1, b: 1 };
    
    var source1 = { b: 2, c: 2 };
    var source2 = { c: 3 };
    
    Object.assign(target, source1, source2);
    target // {a:1, b:2, c:3}
    ```
    - 如果只有一参数，且该参数不是对象，则会先转成对象，然后返回，由于undefined和null无法转成对象，所以如果它们作为参数，就会报错
    - **注意**：如果非对象参数出现在源对象的位置（即非首参数），那么处理规则有所不同。首先，这些参数都会转成对象，如果无法转成对象，就会跳过。这意味着，如果undefined和null不在首参数，就不会报错
    ```
    Object.assign(undefined) // 报错
    Object.assign(null) // 报错
    
    let obj = {a: 1};
    Object.assign(obj, undefined) === obj // true
    Object.assign(obj, null) === obj // true
    ```
    - 其他类型的值（即数值、字符串和布尔值）不在首参数，也不会报错。但是，除了字符串会以数组形式，拷贝入目标对象，其他值都不会产生效果
    ```
    var v1 = 'abc';
    var v2 = true;
    var v3 = 10;
    
    var obj = Object.assign({}, v1, v2, v3);
    console.log(obj); // { "0": "a", "1": "b", "2": "c" }
    ```
    
    > 布尔值、数值、字符串分别转成对应的包装对象，可以看到它们的原始值都在包装对象的内部属性[[PrimitiveValue]]上面，这个属性是不会被Object.assign拷贝的。只有字符串的包装对象，会产生可枚举的实义属性，那些属性则会被拷贝
    
    ```
    Object(true) // {[[PrimitiveValue]]: true}
    Object(10)  //  {[[PrimitiveValue]]: 10}
    Object('abc') // {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}
    ```
    - **限制**，只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性（enumerable: false）
    - 属性名为Symbol值的属性，也会被Object.assign拷贝
    - 注意：
        - Object.assign方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用
        - 对于这种嵌套的对象，一旦遇到同名属性，Object.assign的处理方法是替换，而不是添加
        - Object.assign可以用来处理数组，但是会把数组视为对象
        ```
        Object.assign([1, 2, 3], [4, 5])
        // [4, 5, 3]
        ```
    - 常见用途
        - 为对象添加属性
        - 为对象添加方法
        - 克隆对象
        ```
        function clone(origin) {
          return Object.assign({}, origin);
        }
        ```
        > 不过，采用这种方法克隆，只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码
        
        ```
        function clone(origin) {
          let originProto = Object.getPrototypeOf(origin);
          return Object.assign(Object.create(originProto), origin);
        }
        ```
        - 合并多个对象
        - 为属性指定默认值


- 属性的可枚举性
    - 对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。Object.getOwnPropertyDescriptor方法可以获取该属性的描述对象
    ```
    let obj = { foo: 123 };
    Object.getOwnPropertyDescriptor(obj, 'foo')
    //  {
    //    value: 123,
    //    writable: true,
    //    enumerable: true,
    //    configurable: true
    //  }
    ```
    - 描述对象的enumerable属性，称为”可枚举性“，如果该属性为false，就表示某些操作会忽略当前属性
    - ES5有三个操作会忽略enumerable为false的属性。
        - for...in循环：只遍历对象自身的和 **继承的** 可枚举的属性
        - Object.keys()：返回对象自身的所有可枚举的属性的键名
        - JSON.stringify()：只串行化对象自身的可枚举的属性
    - ES6新增了一个操作Object.assign()，会忽略enumerable为false的属性，只拷贝对象自身的可枚举的属性
    - 引入enumerable的最初目的，就是让某些属性可以规避掉for...in操作。比如，对象原型的toString方法，以及数组的length属性，就通过这种手段，不会被for...in遍历到
    - ES6规定，所有Class的原型的方法都是不可枚举的
    - 操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，尽量不要用for...in循环，而用Object.keys()代替
    
    
- 属性的遍历
    - ES6共5中方法可遍历对象的属性
        1. for...in：for...in循环遍历对象自身的和继承的可枚举属性（不含Symbol属性）
        2. Object.keys(obj)：Object.keys返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含Symbol属性）
        3. Object.getOwnPropertyNames(obj)：Object.getOwnPropertyNames返回一个数组，包含对象自身的所有属性（不含Symbol属性，但是包括不可枚举属性）
        4. Object.getOwnPropertySymbols(obj)：Object.getOwnPropertySymbols返回一个数组，包含对象自身的所有Symbol属性
        5. Reflect.ownKeys(obj)：Reflect.ownKeys返回一个数组，包含对象自身的所有属性，不管属性名是Symbol或字符串，也不管是否可枚举
    - 以上的5种方法遍历对象的属性，都遵守同样的属性遍历的次序规则
        - 首先遍历所有属性名为**数值**的属性，按**照数字排序**。
        - 其次遍历所有属性名为**字符串**的属性，按照**生成时间**排序。
        - 最后遍历所有属性名为**Symbol值**的属性，按照**生成时间**排序。
        

- __proto__属性，Object.setPrototypeOf()， Object.getPrototypeOf()
    - __proto__属性（前后各两个下划线）
        - 用来读取或设置当前对象的prototype对象。目前，所有浏览器（包括 IE11）都部署了这个属性
        - 标准明确规定，只有浏览器必须部署这个属性，其他运行环境不一定需要部署，而且新的代码最好**认为**这个属性是**不存在**的。因此，无论从语义的角度，还是从兼容性的角度，都**不要使用**这个属性，而是使用下面的Object.setPrototypeOf()（写操作）、Object.getPrototypeOf()（读操作）、Object.create()（生成操作）代替
        
    - Object.setPrototypeOf()
        - 作用与__proto__相同，用来设置一个对象的prototype对象，返回参数对象本身。它是 ES6 正式推荐的设置原型对象的方法
        ```
        let proto = {};
        let obj = { x: 10 };
        Object.setPrototypeOf(obj, proto);
        
        proto.y = 20;
        proto.z = 40;
        
        obj.x // 10
        obj.y // 20
        obj.z // 40
        ```
        - 如果第一个参数不是对象，会自动转为对象。但是由于返回的还是第一个参数，所以这个操作不会产生任何效果。
        ```
        Object.setPrototypeOf(1, {}) === 1 // true
        Object.setPrototypeOf('foo', {}) === 'foo' // true
        Object.setPrototypeOf(true, {}) === true // true
        ```
        - 由于undefined和null无法转为对象，所以如果第一个参数是undefined或null，就会报错
    - Object.getPrototypeOf()
        - 该方法与Object.setPrototypeOf方法配套，用于读取一个对象的原型对象
        ```
        // 等同于 Object.getPrototypeOf(Number(1))
        Object.getPrototypeOf(1)
        // Number {[[PrimitiveValue]]: 0}
        
        // 等同于 Object.getPrototypeOf(String('foo'))
        Object.getPrototypeOf('foo')
        // String {length: 0, [[PrimitiveValue]]: ""}
        
        // 等同于 Object.getPrototypeOf(Boolean(true))
        Object.getPrototypeOf(true)
        // Boolean {[[PrimitiveValue]]: false}
        
        Object.getPrototypeOf(1) === Number.prototype // true
        Object.getPrototypeOf('foo') === String.prototype // true
        Object.getPrototypeOf(true) === Boolean.prototype // true
        
        //如果参数是undefined或null，它们无法转为对象，所以会报错。
        
        Object.getPrototypeOf(null)
        // TypeError: Cannot convert undefined or null to object
        
        Object.getPrototypeOf(undefined)
        // TypeError: Cannot convert undefined or null to object
        ```

- Object.keys()，Object.values()，Object.entries()
    - Object.keys()
        - 返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名
        ```
        let {keys, values, entries} = Object;
        let obj = { a: 1, b: 2, c: 3 };
        
        for (let key of keys(obj)) {
          console.log(key); // 'a', 'b', 'c'
        }
        
        for (let value of values(obj)) {
          console.log(value); // 1, 2, 3
        }
        
        for (let [key, value] of entries(obj)) {
          console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
        }
        ```
    - Object.values()
        - Object.values方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值
        ```
        var obj = { 100: 'a', 2: 'b', 7: 'c' };
        Object.values(obj)
        // ["b", "c", "a"]
        ```
        - Object.create方法的**第二个参数**添加的对象属性（属性p），如果不显式声明，**默认是不可遍历**的，因为p的属性描述对象的enumerable默认是false，Object.values不会返回这个属性。只要把enumerable改成true，Object.values就会返回属性p的值
        ```
        var obj = Object.create({}, {p:
          {
            value: 42,
            enumerable: true
          }
        });
        Object.values(obj) // [42]
        ```
        - 如果Object.values方法的参数是一个字符串，会返回各个字符组成的一个数组
        ```
        Object.values('foo')
        // ['f', 'o', 'o']
        ```
        - 如果参数不是对象，Object.values会先将其转为对象。由于数值和布尔值的包装对象，都不会为实例添加非继承的属性。所以，Object.values会返回空数组。
        ```
        Object.values(42) // []
        Object.values(true) // []
        ```
    - Object.entries()
        - 返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组
        ```
        var obj = { foo: 'bar', baz: 42 };
        Object.entries(obj)
        // [ ["foo", "bar"], ["baz", 42] ]
        ```
        - 将对象转为真正的Map结构
        ```
        var obj = { foo: 'bar', baz: 42 };
        var map = new Map(Object.entries(obj));
        map // Map { foo: "bar", baz: 42 }
        ```
    - 对象的扩展运算符
        - 解构赋值
            - 对象的解构赋值用于从一个对象取值，相当于将所有可遍历的、但尚未被读取的属性，分配到指定的对象上面。所有的键和它们的值，都会拷贝到新对象上面
            - 解构赋值的拷贝是浅拷贝
            - 解构赋值不会拷贝继承自原型对象的属性
            ```
            var o = Object.create({ x: 1, y: 2 });
            o.z = 3;
            
            let { x, ...{ y, z } } = o;
            x // 1
            y // undefined
            z // 3
            ```
        - 扩展运算符
            - 用于取出参数对象的所有可遍历属性，拷贝到当前对象之中
            - 如果用户自定义的属性，放在扩展运算符后面，则扩展运算符内部的同名属性会被覆盖掉
            ```
            let aWithOverrides = { ...a, x: 1, y: 2 };
            // 等同于
            let aWithOverrides = { ...a, ...{ x: 1, y: 2 } };
            // 等同于
            let x = 1, y = 2, aWithOverrides = { ...a, x, y };
            // 等同于
            let aWithOverrides = Object.assign({}, a, { x: 1, y: 2 });
            ```
            - 如果把自定义属性放在扩展运算符前面，就变成了设置新对象的默认属性值
            - 如果扩展运算符的参数是null或undefined，这两个值会被忽略，不会报错
            - 扩展运算符的参数对象之中，如果有取值函数get，这个函数是会执行的
            
            
- Object.getOwnPropertyDescriptor()  (es5)
    - 返回某个对象属性的描述对象（descriptor）
    ```
    var obj = { p: 'a' };

    Object.getOwnPropertyDescriptor(obj, 'p')
    // Object { value: "a",
    //   writable: true,
    //   enumerable: true,
    //   configurable: true
    // }
    ```
- Object.getOwnPropertyDescriptors()
    - 返回指定对象所有自身属性（非继承属性）的描述对象
    ```
    const obj = {
      foo: 123,
      get bar() { return 'abc' }
    };
    
    Object.getOwnPropertyDescriptors(obj)
    // { foo:
    //    { value: 123,
    //      writable: true,
    //      enumerable: true,
    //      configurable: true },
    //   bar:
    //    { get: [Function: bar],
    //      set: undefined,
    //      enumerable: true,
    //      configurable: true } }
    ```
    - 该方法的引入目的，主要是为了解决Object.assign()无法正确拷贝get属性和set属性的问题
    ```
    const source = {
      set foo(value) {
        console.log(value);
      }
    };
    
    const target2 = {};
    Object.defineProperties(target2, Object.getOwnPropertyDescriptors(source));
    Object.getOwnPropertyDescriptor(target2, 'foo')
    // { get: undefined,
    //   set: [Function: foo],
    //   enumerable: true,
    //   configurable: true }
    ```
    - 上面合并的逻辑提炼为：
    ```
    const shallowMerge = (target, source) => Object.defineProperties(
      target,
      Object.getOwnPropertyDescriptors(source)
    );
    ```
    - 配合Object.create方法，将对象属性克隆到一个新对象。这属于浅拷贝
    ```
    const clone = Object.create(Object.getPrototypeOf(obj),
      Object.getOwnPropertyDescriptors(obj));
    
    // 或者
    
    const shallowClone = (obj) => Object.create(
      Object.getPrototypeOf(obj),
      Object.getOwnPropertyDescriptors(obj)
    );
    ```
    
- Null传导运算符
    -  判断对象或属性是否存在
    ```
    const firstName = message?.body?.user?.firstName || 'default';
    // 只要其中一个返回null或undefined，就不再往下运算，而是返回undefined
    
    //相当于
    const firstName = (message
    && message.body
    && message.body.user
    && message.body.user.firstName) || 'default';
    ```
    - 四种用法
        - obj?.prop // 读取对象属性
        - obj?.[expr] // 同上
        - func?.(...args) // 函数或对象方法的调用
        - new C?.(...args) // 构造函数的调用