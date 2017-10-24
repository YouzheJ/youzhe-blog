---
title: typescript 笔记(一)
date: 2017-10-24 11:46:46
tags:
- 前端
- typescript
categories:
- 笔记
- 前端
- typescript
---

入门 [https://www.gitbook.com/book/xcatliu/typescript-tutorial/details](https://www.gitbook.com/book/xcatliu/typescript-tutorial/details)
中文文档 [https://www.tslang.cn/docs/home.html](https://www.tslang.cn/docs/home.html)
结合JSX [https://github.com/techird/blog/issues/3](https://github.com/techird/blog/issues/3)
参考的小例子 [https://github.com/jaysoo/todomvc-redux-react-typescript](https://github.com/jaysoo/todomvc-redux-react-typescript)

<!--more-->

---
### 基础

#### 基础类型

- 布尔型

```js
let isDone: boolean = false;
```

- 数字
```js
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

- 字符串

```js
let name: string = "bob";
```

- 数组

```js
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```

- 元组: 元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同

```js
let x: [string, number] = ['hello', 10];
// 当访问一个越界的元素，会使用联合类型替代
x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型
x[5].toString(); // OK, 'string' 和 'number' 都有 toString
x[6] = true; // Error, 布尔不是(string | number)类型
```

- 枚举: enum类型是对JavaScript标准数据类型的一个补充

```js
enum Color { Red, Green, Blue }
let c: Color = Color.Green;
```
> 默认情况下，从0开始为元素编号， 也可手动赋值

```js
enum Color {Red = 1, Green = 2, Blue}
let c: Color = Color.Green; // 2
let colorName: string = Color[2]; // 'Green'
```

- Any: 不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查
```js
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

- Void: 表示没有任何类型
> 当一个函数没有返回值时，你通常会见到其返回值类型是 void

```js
function warnUser(): Void {
    console.log('This is my warning message');
}
```

```js
// 声明一个void类型的变量只能为它赋予undefined和null：
let unusable: void = undefined;
```

- Null 和 Undefined

```js
let u: undefined = undefined;
let n: null = null;
```
> 默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量

> 当你指定了--strictNullChecks标记(默认关闭)，null和undefined只能赋值给void和它们各自。 这能避免很多常见的问题。 

> 也许在某处你想传入一个 string或null或undefined，你可以使用联合类型string | null | undefined。

-  Never: 永不存在的值的类型
> never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never

```js
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}
// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}
// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```

- 类型断言
    - 使用尖括号
    ```js
    let someValue: any = "this is a string";
    let strLength: number = (<string>someValue).length;
    ```
    - 使用as
    ```js
    let someValue: any = "this is a string";
    let strLength: number = (someValue as string).length;
    ```
    
#### 变量声明
- 对象结构-属性重命名

```js
// 重命名
let { a: newName1, b: newName2 } = o;

// 指定类型
let {a, b}: {a: string, b: number} = o;

// 默认值
function keepWholeObject(wholeObject: { a: string, b?: number }) {
    let { a, b = 1001 } = wholeObject;
}
```
    
#### 接口
> 接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约

- 可选属性
> 在可选属性名字定义的后面加一个?符号

```js
interface SquareConfig {
  color?: string;
  width?: number;
}
```
- 只读属性
> 做为变量使用的话用 const，若做为属性则使用readonly

```js
interface Point {
    readonly x: number;
    readonly y: number;
}

// TypeScript具有ReadonlyArray<T>类型，它与Array<T>相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // error!
ro.push(5); // error!
ro.length = 100; // error!
a = ro; // error!
a = ro as number[]; // ok!
```

- 额外的属性检查

```js
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```
    
- 函数类型

```js
// 为了使用接口表示函数类型，我们需要给接口定义一个调用签名
interface SearchFunc {
    // 调用签名： 一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subSting: string) {
    let result = source.search(subString);
    return result > -1;
}
// 对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
  let result = src.search(sub);
  return result > -1;
}
```

- 可索引类型

```js
interface StringArray {
    // 索引签名，它描述了对象索引的类型，还有相应的索引返回值类型
    // 共有支持两种索引签名：字符串和数字
    [index: number]: string;
    // [index: string]: string;
    // 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```
```js
interface NumberDictionary {
  [index: string]: number;
  length: number;    // 可以，length是number类型
  name: string       // 错误，`name`的类型与索引类型返回值的类型不匹配
}
```
```js
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error!
```

- 类类型

```js
// 接口描述了类的公共部分，而不是公共和私有两部分。 它不会帮你检查类是否具有某些私有成员
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```
> 类的静态部分与实例部分

```js
// 检查类的静态部分应该直接操作类的静态部分
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

- 继承接口

```js
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

- ### 混合类型

```js
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) {};
    counter.interval = 123;
    counter.reset = function() {};
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

- 接口继承类
> 当接口继承了一个类类型时，它会继承类的成员但不包括其实现

```js
class Control {
    private state: any;
}

interface SelectableControl extends Control { // 有state
    select(): void;
}

class Button extends Control { // 有state
    select() { }
}

class Image { // 无state
    select() { }
}
```

#### 类
- 基本

```js
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello" + this.greeting;
    }
}
let greeter = new Greeter("world");
```
- 继承

```js
class Animal {
    name:string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) { // 重写父类中的方法
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) { // 重写父类中的方法
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);

/*
Slithering...
Sammy the Python moved 5m.
Galloping...
Tommy the Palomino moved 34m.
*/
```
> 使用 extends关键字来创建子类

> 包含构造函数的派生类必须调用super()，它会执行基类的构造方法

- 修饰符
    - 默认为public

    ```js
    class Animal {
        public name: string;
        public constructor(theName: string) { this.name = theName; }
        public move(distanceInMeters: number) {
            console.log(`${this.name} moved ${distanceInMeters}m.`);
        }
    }
    ```
    - private 不能在声明它的类的外部访问
    
    ```js
    class Animal {
        private name: string;
        constructor(theName: string) { this.name = theName; }
    }
    new Animal("Cat").name; // Error: 'name' is private;
    ```
    > TypeScript使用的是结构性类型系统。当我们比较两种不同的类型时，并不在乎它们从何处而来，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的
    
    > 如果其中一个类型里包含一个private成员，那么只有当另外一个类型中也存在这样一个private成员，并且它们都是来自同一处声明时，我们才认为这两个类型是兼容的。 对于 protected成员也使用这个规则
    
    ```js
    class Animal {
        private name: string;
        constructor(theName: string) { this.name = theName; }
    }
    
    class Rhino extends Animal {
        constructor() { super("Rhino"); }
    }
    
    class Employee {
        private name: string;
        constructor(theName: string) { this.name = theName; }
    }
    
    let animal = new Animal("Goat");
    let rhino = new Rhino();
    let employee = new Employee("Bob");
    
    animal = rhino;
    animal = employee; // Error: Animal and Employee are not compatible
    ```
    
    - protected 与private类似，但在派生类中仍然可以访问
    
    ```js
    class Person {
        protected name: string;
        constructor(name: string) { this.name = name; }
    }
    
    class Employee extends Person {
        private department: string;
    
        constructor(name: string, department: string) {
            super(name)
            this.department = department;
        }
    
        public getElevatorPitch() {
            return `Hello, my name is ${this.name} and I work in ${this.department}.`;
        }
    }
    
    let howard = new Employee("Howard", "Sales");
    console.log(howard.getElevatorPitch());
    console.log(howard.name); // error
    ```
    > 构造函数也可以被标记成protected。 这意味着这个类不能在包含它的类外被实例化，但是能被继承
    
    ```js
    class Person {
        protected name: string;
        protected constructor(theName: string) { this.name = theName; }
    }
    
    // Employee can extend Person
    class Employee extends Person {
        private department: string;
    
        constructor(name: string, department: string) {
            super(name);
            this.department = department;
        }
    
        public getElevatorPitch() {
            return `Hello, my name is ${this.name} and I work in ${this.department}.`;
        }
    }
    
    let howard = new Employee("Howard", "Sales");
    let john = new Person("John"); // Error: The 'Person' constructor is protected
    ```
    
    - readonly
    > 只读属性必须在声明时或构造函数里被初始化
    
    ```js
    class Octopus {
        readonly name: string;
        readonly numberOfLegs: number = 8;
        constructor (theName: string) {
            this.name = theName;
        }
    }
    let dad = new Octopus("Man with the 8 strong legs");
    dad.name = "Man with the 3-piece suit"; // error! name is readonly.
    ```
    
- 参数属性: 通过给构造函数参数添加一个访问限定符来声明

```js
class Animal {
    constructor(private name: string) { }
    move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

- 存取器

- 静态属性static  这些属性存在于类本身上面而不是类的实例上

```js
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale
console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

- 抽象类

> abstract关键字是用于定义抽象类和在抽象类内部定义抽象方法

> 抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化

> 抽象类中的抽象方法不包含具体实现并且必须在派生类中实现

```js
abstract class Department {
    constructor(public name: string) {}
    printName(): void {
        console.log('Department name: ' + this.name);
    }
    abstract printMeeting(): void; // 必须在派生类中实现
}

class AccountingDepartment extends Department {
    constructor() {
        super('Accounting and Auditing'); // constructors in derived classes must call super()
    }
    printMeeting(): void {
        console.log('The Accounting Department meets each Monday at 10am.');
    }
    generateReports(): void {
        console.log('Generating accounting reports...');
    }
}

let department: Department; // ok to create a reference to an abstract type
department = new Department(); // error: cannot create an instance of an abstract class
department = new AccountingDepartment(); // ok to create and assign a non-abstract subclass
department.printName();
department.printMeeting();
department.generateReports(); // error: method doesn't exist on declared abstract type
```

