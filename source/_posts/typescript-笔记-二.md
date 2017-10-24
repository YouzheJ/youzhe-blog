---
title: typescript 笔记(二)
date: 2017-10-24 11:49:01
tags:
- 前端
- typescript
categories:
- 笔记
- 前端
- typescirpt
---

#### 函数

<!--more-->

- 函数类型

    ```js
    function add(x: number, y: number): number {
        return x + y;
    }
    
    let myAdd = function(x: number, y: number): number { return x+y; };
    ```
    - 书写完整函数类型
    
    ```js
    let myAdd: (x: number, y: number) => number = 
        function(x: number, y: number): number { return x + y };
    ```
    > (x: number, y: number) => number 为myAdd指定类型： 两部分
    >   - 参数类型： 只匹配类型，不匹配参数名
    >   - 返回值类型: (=>)符号连接，如果函数没有返回值，必须指定返回值类型为 void
    
    > number { return x + y } number指定function的返回值类型
    
    - 推断类型: 按"上下文归类"
    
    ```js
    let myAdd = function(x: number, y: number): number { return x + y};
    
    let myAdd: (baseValue: number, increment: number) => number = function(x, y) { return x + y };
    ```

- 可选参数和默认参数

    ```js
    function buildName(firstName: string, lastName?: string) {
        if (lastName)
            return firstName + " " + lastName;
        else
            return firstName;
    }
    
    let result1 = buildName("Bob");  // works correctly now
    let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
    let result3 = buildName("Bob", "Adams");  // ah, just right
    ```
    > 可选参数必须在必须参数后面
    
    ```js
    function buildName(firstName: string, lastName = "Smith") {
        return firstName + " " + lastName;
    }
    ```
    > 在所有必须参数后面的带默认初始化的参数都是可选的，与可选参数一样，在调用函数的时候可以省略
    > 带默认值的参数不需要放在必须参数的后面， 必须明确的传入 undefined值来获得默认值
    
    ```js
    function buildName(first = "Will", lastName: string) {
        return firstName + " " + lastName;
    }
    let result = buildName(undefined, "Adams");
    ```
    
    - 剩余参数
    
    ```js
    function buildName(firstName: string, ...restOfName: string[]) {
      return firstName + " " + restOfName.join(" ");
    }
    
    let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
    
    let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
    ```

- this

#### 泛型

- 类型变量： 只用于表示类型而不是值

```js
function identity<T>(arg: T): T {
    return arg;
}
/*
* 给identity添加了类型变量T
* T帮助我们捕获用户传入的类型
* 使用了 T当做返回值类型
* 
* identity函数叫做泛型，因为它可以适用于多个类型
*/

// 使用，两种方法
let output1 = identity<string>("myString"); // 传入所有的参数，包含类型参数
let output2 = identity("myString"); // 类型推论
```

- 使用泛型变量
    - 必须把这些参数当做是任意或所有类型

- 泛型类型

```js
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;

// 使用带有调用签名的对象字面量来定义泛型函数
let myIdentity: {<T>(arg: T): T} = identity;
```
```js
// 泛型接口
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```

- 泛型类

```js
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```
>  泛型类指的是实例部分的类型，所以类的静态属性不能使用这个泛型类型

- 泛型约束

- 定义接口描述约束条件

```js
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}

loggingIdentity(3); // Error, number doesn't have a .length property
loggingIdentity({length: 10, value: 3}); // 传入符合约束类型的值，必须包含必须的属性
```


#### 枚举

> 使用枚举我们可以定义一些有名字的数字常量

> 一个枚举类型可以包含零个或多个枚举成员

```js
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}
```

- 枚举成员具有一个数字值，它可以是 常数或是计算得出的值

```js
enum FileAccess {
    // constant members
    None,
    Read    = 1 << 1,
    Write   = 1 << 2,
    ReadWrite  = Read | Write,
    // computed member
    G = "123".length
}
```
> 枚举是在运行时真正存在的一个对象。 其中一个原因是因为这样可以从枚举值到枚举名进行反向映射

- 常数枚举 常数枚举是在 enum关键字前使用const修饰符

> 常数枚举只能使用常数枚举表达式并且不同于常规的枚举的是它们在编译阶段会被删除

- 外部枚举: 外部枚举用来描述已经存在的枚举类型的形状

```js
declare enum Enum {
    A = 1,
    B,
    C = 2
}
```

> 外部枚举和非外部枚举之间有一个重要的区别: 

> - 在正常的枚举里，没有初始化方法的成员被当成常数成员
> - 对于非常数的外部枚举而言，没有初始化方法时被当做需要经过计算的

#### 类型推论

- 最佳通用类型

```js
let x = [0, 1, null];
// 

let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()];
// 没有找到最佳通用类型的话，类型推论的结果是空对象类型，{}
```

- 上下文类型


#### 类型兼容性

> 类型兼容性是基于结构子类型的

```js
interface Named {
    name: string;
}
class Person {
    name: string;
}
let p: Named;
p = new Person(); // ok, 类型兼容性
```

- 基本规则

> 结构化类型系统的基本规则是，如果x要兼容y，那么y至少具有与x相同的属性

- 比较两个函数

    - c参数：只比较参数的类型
    ```js
    let x = (a: number) => 0;
    let y = (b: number, s: string) => 0;
    
    y = x; // ok
    x = y; // error
    ```
    - 返回值：类型系统强制源函数的返回值类型必须是目标函数返回值类型的子类型
    
    ```js
    let x = () => ({name: 'Alice'});
    let y = () => ({name: 'Alice', location: 'Seattle'});
    
    x = y; // OK
    y = x; // Error because x() lacks a location property
    ```
    - 函数参数双向协变
    
    - 可选参数及剩余参数
    
- 枚举：枚举类型与数字类型兼容，并且数字类型与枚举类型兼容。不同枚举类型之间是不兼容的

```js
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };

let status = Status.Ready;
status = Color.Green;  //error
```

- 类: 类与对象字面量和接口差不多，但有一点不同：类有静态部分和实例部分的类型。 

> 比较两个类类型的对象时，只有实例的成员会被比较。 静态成员和构造函数不在比较的范围内

    - 类的私有成员
    
    > 私有成员会影响兼容性判断。 当类的实例用来检查兼容时，如果目标类型包含一个私有成员，那么源类型必须包含来自同一个类的这个私有成员。 这允许子类赋值给父类，但是不能赋值给其它有同样类型的类
    
- 泛型

- 因为TypeScript是结构性的类型系统，类型参数只影响使用其做为类型一部分的结果类型

```js
interface Empty<T> { }
let x: Empty<number>;
let y: Empty<string>;
x = y; // ok
```
- 对于没指定泛型类型的泛型参数时，会把所有泛型参数当成any比较

```js
let identity = function<T>(x: T): T { }
let reverse = function<U>(y: U): U { }

identity = reverse;  // Okay because (x: any)=>any matches (y: any)=>any
```

#### 高级类型

- 交叉类型
> 例如， Person & Serializable & Loggable同时是Person和Serializable和Loggable

- 联合类型

```js
function padLeft(value: string, padding: string | number) {
    // ...
}
```
> 如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员

- 类型保护与区分类型

    - 自定义类型保护: 类型保护就是一些表达式，它们会在运行时检查以确保在某个作用域里的类型。要定义一个类型保护，我们只要简单地定义一个函数，它的返回值是一个 类型谓词
    ```js
    interface Bird {
        fly();
        layEggs();
    }
    interface Fish {
        swim();
        layEggs();
    }
    function isFish(pet: Fish | Bird): pet is Fish { // pet is Fish 就是类型谓词(parameterName is Type)
        return (<Fish>pet).swim !== undefined;
    }
    
    // 'swim' 和 'fly' 调用都没有问题了
    if (isFish(pet)) { //pet是Fish类型
        pet.swim();
    }
    else { // 一定是Bird类型
        pet.fly();
    }
    ```
    - typeof 类型保护
    
    ```js
    function padLeft(value: string, padding: string | number) {
        if(typeof padding === 'number') {
            return Array(padding + 1).join(' ') + value;
        }
        if(typeof padding === 'string') {
            return padding + value;
        }
        throw new Error(`Expected string or number, got '${padding}'.`);
    }    
    ```
    > 只能使用 === 或 !==
    
    > 类型必须为 "number", "string", "boolean" 或 "symbol"
    
    - instanceof 类型保护
    > instanceof的右侧要求是一个构造函数，TypeScript将细化为：
    >   - 此构造函数的prototype属性的类型，如果它的类型不为any的话
    >   - 构造签名所返回的类型的联合
    
- 可以为null的类型
    > null与undefined可以赋值给任何类型
    
    > --strictNullChecks标记可以解决此错误：当你声明一个变量时，它不会自动地包含null或undefined
    
    ```js
    let s = "foo";
    s = null; // error, 'null'不能赋值给'string'
    let sn: string | null = "bar";
    sn = null; // ok
    sn = undefined; // error 'undefined‘不能赋值给'string | null'
    ```
    - 可选参数和可选属性: 使用了--strictNullChecks，可选参数会被自动地加上| undefined
    - 类型保护和类型断言: 去除null
    
    ```js
    // 类型保护
    function f(sn: string | null): string {
        return sn || "default";
    }
    
    // 类型断言
    function fixed(name: string | null): string { // 编译器无法去除嵌套函数的null
        function psotfix(epithet: string) {
            return name!.charAt(0) + '. the ' + epithet; // ok, 添加!后缀，手动去除null或undefined
            
        }
        name = name || "Bob";
        return postfix("great");
    }
    ```
    
- 类型别名: 类型别名会给一个类型起个新名字

    > 类型别名有时和接口很像，但是可以作用于原始值，联合类型，元组以及其它任何你需要手写的类型
    
    > 别名不会新建一个类型，只是引用
    
    ```js
    type Name = string;
    type NameResolver = () =>　string;
    type NameOrResolver = Name | NameResolver;
    function getName(n: NameOrResolver): Name {
        if(typeof n === 'string') {
            return n;
        }else {
            return n();
        }
    }
    
    type Container<T> = { vlaue: T }; // 泛型
    
    // 使用类型别名来在属性里引用自己
    type Tree<T> ={
        value: T;
        left: Tree<T>;
        right: Tree<T>;
    }
    ```
    > 类型别名不能出现在声明右侧的任何地方
    
    ```js
    type Yikes = Array<Yikes>; // error
    ```
    
    > 类型别名不能被extends和implements
    
- 字符串字面量类型

```js
type Easing = "ease-in" | "ease-out" | "ease-in-out";
```

- 可辨识联合: 合并字符串字面量类型，联合类型，类型保护和类型别名来创建一个叫做可辨识联合的高级模式，它也称做标签联合或代数数据类型

```js
interface Square {
    kind: 'square';
    size: number;
}
interface Rectangle {
    kink: 'rectangel';
    width: number;
    height: number;
}
interface Circle {
    kind: 'circle';
    radius：number;
}
type Shape = Square | Rectangle | Circle | Triangle;
function assertNever(x: never): never {
    throw new Error('Unexpected oject: ' + x);
}
function area(s: Shape) {
    switch (s.kind) {
        case 'square': return s.size * s.size;
        case 'rectangle': return s.height * s.width;
        case 'circle': return Math.PI * s.radius * 2;
        default: return assertNever(s);
    }
}
```
 - this类型

- 索引类型: 检查使用了动态属性名的代码

    ```js
    function pluck<T, K extends keyof T>(o: T, names: K[]): T[K][] {
        return names.map(n => o[n]);
    }
    interface Person {
        name: string;
        age: number;
    }
    let person: Person = {
        name: 'Jarid',
        age: 35
    };
    let strings: string[] = pluck(person, ['name']); // ok
    ```
    - 索引类型查询操作符 keyof
    
    ```js
    let personProps: keyof Person; // 'name' | 'age'
    ```

- 映射类型: 从旧类型中创建新类型

```js
type Nullable<T> = { [P in keyof T]: T[P] | null }
// 三个部分  类型变量P(绑定到每个属性上)  属性列表keyof T  属性的结果类型T[p]
type Partial<T> = { [P in keyof T]?: T[P] }
```
 #### symbol
 
 #### 迭代器和生成器
 
 > 可迭代： 一个对象实现了Symbol.iterator属性
 
 - for...of: 迭代对象的键对应的值
 - for...in：迭代对象的键
 
#### 模块

> 模块： 外部模块， 命名空间： 内部模块

- 导出
> 任何声明（比如变量，函数，类，类型别名或接口）都能够通过添加export关键字来导出

- 导入

- 'export = '

```js
export = xxx;

import xxx = require("xxx");
```

- 使用非TypeScript编写的类库： 编写 .d.ts 文件

    ```js
    declare module "url" {
        export interface Url {
            protocol?: string;
            hostname?: string;
            pathname?: string;
        }
    
        export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
    }
    
    declare module "path" {
        export function normalize(p: string): string;
        export function join(...paths: any[]): string;
        export let sep: string;
    }
    ```
    
    - 也可以使用简写： 所有导出的类型为any
    
    ```js
    declare module "hot-new-module";
    ```
    
#### 命名空间

#### 命名空间和模块

#### 模块解析

#### 声明合并
- 合并接口

```js
interface Box {
    height: number;
    width: number;
}
interface Box {
    scale: number;
}
let box: Box = { height: 4, width: 3, scale: 10 };
```
> 非函数的成员必须是唯一的。 如果两个接口中同时声明了同名的非函数成员编译器则会报错

> 对于函数成员，每个同名函数声明都会被当成这个函数的一个重载。 同时需要注意，当接口 A与后来的接口A合并时，后面的接口具有更高的优先级

- 合并命名空间
    - 模块导出的同名接口进行合并
    - 值的合并，如果当前已经存在给定名字的命名空间，那么后来的命名空间的导出成员会被加到已经存在的那个模块里

- 命名空间与类和函数和枚举类型合并

- 非法的合并
    - 类不能与其它类或变量合并

#### JSX

#### 装饰器
> 一种特殊类型的声明，能够被附加到类声明，方法， 访问符，属性或参数上

- 装饰器工厂

```js
// 装饰器工厂函数
function color(value: string) { // 装饰器工厂
    return function(target) { // 装饰器
        // ...
    }
}
```

- 装饰器组合

```js
@f @f x
// or
@f
@g
x
// 相当于 f(g(x))
```

- 装饰器求值（顺序）：
    1. 参数装饰器，然后依次是方法装饰器，访问符装饰器，或属性装饰器应用到每个实例成员
    2. 参数装饰器，然后依次是方法装饰器，访问符装饰器，或属性装饰器应用到每个静态成员
    3. 参数装饰器应用到构造函数
    4. 类装饰器应用到类

- 类修饰器
> 在运行时当作函数被调用，类的构造函数作为其唯一的参数

> 如果类装饰器返回一个值，它会使用提供的构造函数来替换类的声明

> 类装饰器不能用在声明文件中( .d.ts)，也不能用在任何外部上下文中（比如declare的类） 以下修饰器均相同

- 方法修饰器

- 访问器修饰器

- 属性修饰器

- 参数修饰器

- 元数据

#### Mixins

#### 三斜线指令

