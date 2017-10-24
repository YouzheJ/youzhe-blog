---
title: es6 笔记(三)
date: 2017-10-24 11:40:15
tags:
- 前端
- es6
categories:
- 笔记
- 前端
- es6
---

#### Symbol

<!--more-->

- 概述
    - 保证对象的属性名都是独一无二的，不会冲突
    - 一种新的**原始数据类型**Symbol，表示独一无二的值是js的第七种数据类型，undefined、null、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）
    - Symbol 值通过Symbol函数生成
    ```
    let s = Symbol()
     typeof s
     // "symbol"
    ```
    - Symbol函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分
    - Symbol 值不能与其他类型的值进行运算，会报错
    - 但是，Symbol 值可以显式转为字符串，也可以转为布尔值，但是不能转为数值
    ```
    var sym = Symbol('My symbol');
    
    String(sym) // 'Symbol(My symbol)'
    sym.toString() // 'Symbol(My symbol)'
    
    var sym = Symbol();
    Boolean(sym) // true
    !sym  // false
    
    if (sym) {
      // ...
    }
    
    Number(sym) // TypeError
    sym + 2 // TypeError
    ```
    
- 作为属性名
    - 能保证不会出现同名的属性
    ```
    var mySymbol = Symbol();

    // 第一种写法
    var a = {};
    a[mySymbol] = 'Hello!';
    
    // 第二种写法
    var a = {
      [mySymbol]: 'Hello!'
    };
    
    // 第三种写法
    var a = {};
    Object.defineProperty(a, mySymbol, { value: 'Hello!' });
    
    // 以上写法都得到同样结果
    a[mySymbol] // "Hello!"
    ```
    - Symbol 值作为对象属性名时，不能用点运算符
    - 在对象的内部，使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中
    - 用于定义一组常量，保证这组常量的值都是不相等的
    
- 魔术字符串
    - 在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值


- 属性名的遍历
    - Symbol 作为属性名，该属性不会出现在for...in、for...of循环中，也不会被Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()返回。但是，它也不是私有属性，有一个**Object.getOwnPropertySymbols**方法，可以获取指定对象的所有 Symbol 属性名
    - Object.getOwnPropertySymbols方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值


- Symbol.for()，Symbol.keyFor()
    - 希望重新使用同一个Symbol值，Symbol.for方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的Symbol值。如果有，就返回这个Symbol值，否则就新建并返回一个以该字符串为名称的Symbol值
    ```
    var s1 = Symbol.for('foo');
    var s2 = Symbol.for('foo');
    
    s1 === s2 // true
    ```
    - Symbol.for()不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的key是否已经存在，如果不存在才会新建一个值
    - Symbol.for为Symbol值**登记**的名字，是**全局环境**的，可以在不同的 iframe 或 service worker 中取到同一个值
    - Symbol.keyFor方法返回一个已登记的 Symbol 类型值的key，未登记的Symbol值，返回undefined
    
    
- Singleton模式
    - 调用一个类，任何时候返回的都是同一个实例


- 内置的Symbol值（属性）
    - Symbol.hasInstance
    - Symbol.isConcatSpreadable
        - 属性等于一个布尔值，表示该对象使用Array.prototype.concat()时，是否可以展开
        - 数组的默认行为是可以展开。Symbol.isConcatSpreadable属性等于true或undefined，都有这个效果
    - Symbol.species：指向当前对象的构造函数
    - Symbol.match：指向一个函数。当执行str.match(myObject)时，如果该属性存在，会调用它，返回该方法的返回值
    - Symbol.replace：指向一个方法，当该对象被String.prototype.replace方法调用时，会返回该方法的返回值
    - Symbol.search：指向一个方法，当该对象被String.prototype.search方法调用时，会返回该方法的返回值
    - Symbol.split：指向一个方法，当该对象被String.prototype.split方法调用时，会返回该方法的返回值
    - Symbol.iterator：指向该对象的默认遍历器方法
    - Symbol.toPrimitive：指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值
    - Symbol.toStringTag：指向一个方法。在该对象上面调用Object.prototype.toString方法时，如果这个属性存在，它的返回值会出现在toString方法返回的字符串之中，表示对象的类型。也就是说，这个属性可以用来定制[object Object]或[object Array]中object后面的那个字符串
    - Symbol.unscopables：指向一个对象。该对象指定了使用with关键字时，哪些属性会被with环境排除


#### Proxy
- 用于修改某些操作的默认行为，可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写
- 用法:
```
var proxy = new Proxy(target, handler)
```
> new Proxy()表示生成一个Proxy实例，target参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为

- 使用：Proxy 实例也可以作为其他对象的原型对象
```
var proxy = new Proxy({}, {
  get: function(target, property) {
    return 35;
  }
});

let obj = Object.create(proxy);
obj.time // 35
```
- Proxy 支持的拦截操作
1. get(target, propKey, receiver)
    - 拦截对象属性的读取，比如proxy.foo和proxy['foo']。
    - 最后一个参数receiver是一个对象，可选，参见下面Reflect.get的部分。

2. set(target, propKey, value, receiver)
    - 拦截对象属性的设置，比如proxy.foo = v或proxy['foo'] = v，返回一个布尔值。

3. has(target, propKey)
    - 拦截propKey in proxy的操作，返回一个布尔值。

4. deleteProperty(target, propKey)
    - 拦截delete proxy[propKey]的操作，返回一个布尔值。

5. ownKeys(target)
    - 拦截Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而Object.keys()的返回结果仅包括目标对象自身的可遍历属性。

6. getOwnPropertyDescriptor(target, propKey)
    - 拦截Object.getOwnPropertyDescriptor(proxy, propKey)，返回属性的描述对象。

7. defineProperty(target, propKey, propDesc)
    - 拦截Object.defineProperty(proxy, propKey, propDesc）、Object.defineProperties(proxy, propDescs)，返回一个布尔值。

8. preventExtensions(target)
    - 拦截Object.preventExtensions(proxy)，返回一个布尔值。

9. getPrototypeOf(target)
    - 拦截Object.getPrototypeOf(proxy)，返回一个对象。

10. isExtensible(target)
    - 拦截Object.isExtensible(proxy)，返回一个布尔值。

11. setPrototypeOf(target, proto)
    - 拦截Object.setPrototypeOf(proxy, proto)，返回一个布尔值。
    - 如果目标对象是函数，那么还有两种额外操作可以拦截。

12. apply(target, object, args)
    - 拦截 Proxy 实例作为函数调用的操作，比如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)。
13. construct(target, args)
    - 拦截 Proxy 实例作为构造函数调用的操作，比如new proxy(...args)。

- Proxy.revocable
    - 返回一个可取消的 Proxy 实例
    ```
    let target = {};
    let handler = {};
    
    let {proxy, revoke} = Proxy.revocable(target, handler);
    
    proxy.foo = 123;
    proxy.foo // 123
    
    revoke();
    proxy.foo // TypeError: Revoked
    ```
    - Proxy.revocable的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问
    
- this 问题
    - Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理，即不做任何拦截的情况下，也无法保证与目标对象的行为一致。主要原因就是在 Proxy 代理的情况下，目标对象内部的this关键字会**指向** Proxy 代理
    - 有些原生对象的内部属性，只有通过正确的this才能拿到，所以 Proxy 也无法代理这些原生对象的属性
    ```
    const target = new Date();
    const handler = {};
    const proxy = new Proxy(target, handler);
    
    proxy.getDate();
    // TypeError: this is not a Date object.
    ```
    ```
    // 绑定this可以解决    
    const target = new Date('2015-01-01');
    const handler = {
      get(target, prop) {
        if (prop === 'getDate') {
          return target.getDate.bind(target);
        }
        return Reflect.get(target, prop);
      }
    };
    const proxy = new Proxy(target, handler);
    
    proxy.getDate() // 1
    ```


#### Reflect
- 概述
    - 将Object对象的一些明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上
    - 修改某些Object方法的返回结果，让其变得更合理。比如，Object.defineProperty(obj, name, desc)在无法定义属性时，会抛出一个错误，而Reflect.defineProperty(obj, name, desc)则会返回false
    - 让Object操作都变成函数行为。某些Object操作是命令式，比如name in obj和delete obj[name]，而Reflect.has(obj, name)和Reflect.deleteProperty(obj, name)让它们变成了函数行为
    - Reflect对象的方法与Proxy对象的方法一一对应，只要是Proxy对象的方法，就能在Reflect对象上找到对应的方法，不管Proxy怎么修改默认行为，你总可以在Reflect上获取默认行为

- 静态方法
    > Reflect对象一共有13个静态方法。

    - Reflect.construct(target,args)
    - Reflect.apply(target,thisArg,args)
    - Reflect.get(target,name,receiver)
    - Reflect.set(target,name,value,receiver)
    - Reflect.defineProperty(target,name,desc)
    - Reflect.deleteProperty(target,name)
    - Reflect.has(target,name)
    - Reflect.ownKeys(target)
    - Reflect.isExtensible(target)
    - Reflect.preventExtensions(target)
    - Reflect.getOwnPropertyDescriptor(target, name)
    - Reflect.getPrototypeOf(target)
    - Reflect.setPrototypeOf(target, prototype)

- 使用 Proxy 实现观察者模式
    - 观察者模式（Observer mode）指的是函数自动观察数据对象，一旦对象有变化，函数就会自动执行
    - 简单的例子：
    ```
    const person = observable({
      name: '张三',
      age: 20
    });
    
    function print() {
      console.log(`${person.name}, ${person.age}`)
    }
    
    observe(print);
    person.name = '李四';
    // 输出
    // 李四, 20
    ```
    ```
    const queuedObservers = new Set();
    
    const observe = fn => queuedObservers.add(fn);
    const observable = obj => new Proxy(obj, {set});
    
    function set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      queuedObservers.forEach(observer => observer());
      return result;
    }
    ```