---
title: React 基础笔记
date: 2017-10-23 12:00:35
tags:
- 前端
- react
categories:
- 笔记
- 前端
- react
---

中文文档 [https://discountry.github.io/react/?utm_source=qq&utm_medium=social](https://discountry.github.io/react/?utm_source=qq&utm_medium=social)

阮一峰的React技术栈系列教程 [http://www.ruanyifeng.com/blog/2016/09/react-technology-stack.html](http://www.ruanyifeng.com/blog/2016/09/react-technology-stack.html)

<!--more-->

#### React基础
[http://www.ruanyifeng.com/blog/2015/03/react.html](http://www.ruanyifeng.com/blog/2015/03/react.html)

> **React部分知识解析**
[http://mp.weixin.qq.com/s/KAabFW4p3szS7x-_i2okQg](http://mp.weixin.qq.com/s/KAabFW4p3szS7x-_i2okQg)

- 需要引入的库说明
    - react.js React的核心库
    - react-dom.js 提供与dom相关的功能
    - Browser.js 将jsx语法转为js语法
- HTML模板

    使用jsx语法的script标签需要将type设为text/babel
    
- ReactDOM.render是React的最基本方法
```
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('example')
);
```
- JSX语法
    - 语法规则：遇到 HTML 标签（以 < 开头），就用 HTML 规则解析；遇到代码块（以 { 开头）
    - JSX 允许直接在模板插入 JavaScript 变量。如果这个变量是一个数组，则会展开这个数组的所有成员
    - 组件：React.createClass 方法就用于生成一个组件类
    ```
    var HelloMessage = React.createClass({
      render: function() {
        return <h1>Hello {this.props.name}</h1>;
      }
    });
    
    ReactDOM.render(
      <HelloMessage name="John" />,
      document.getElementById('example')
    );
    ```
    - **组件类的第一个字母必须大写**
    - **组件类只能包含一个顶层标签**
    - this.props 可以获取组件上的属性
        > 注意：class 属性需要写成 className ，for 属性需要写成 htmlFor ，这是因为 class 和 for 是 JavaScript 的保留字
    
- this.props.children
    表示组件的所有子节点
    - 当前组件没有子节点，它就是 undefined
    - 有一个子节点，数据类型是 object
    - 有多个子节点，数据类型就是 array
    > React 提供一个工具方法 React.Children 来处理 this.props.children，用 React.Children.map 来遍历子节点，而不用担心 this.props.children 的数据类型是 undefined 还是 object

- PropTypes
    
    用于验证属性的值是否符合要求
    ```
    var MyTitle = React.createClass({
      propTypes: {
        title: React.PropTypes.string.isRequired,
      },
    
      render: function() {
         return <h1> {this.props.title} </h1>;
       }
    });
    ```
- getDefaultProps
    
    createClass中的方法，设置属性的默认值
    ```
    getDefaultProps : function () {
        return {
          title : 'Hello World'
        };
      }
      ```
      
- 获取真实的DOM节点
 
    - 组件是存于内存中的一种数据结构，叫**虚拟DOM**，只有当它插入文档后才会变成真实的dom。

    - react的设计中，所有的dom变化，都先在虚拟dom上发生，然后再将变动的部分反映到真实dom上，这种算法叫做**DOM diff**。
    
    - 使用ref属性获取真实DOM节点
        > 在dom上添加ref 属性，然后 this.refs.[refName] 就会返回这个真实的 DOM 节点。
        
        > 注意：由于 this.refs.[refName] 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性
        
- this.state
    React 的一大创新，就是将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染 UI 

    1. 初始化状态

    ```
    getInitialState: function() {
        return {liked: false};
     }
     ```
     2. this.state.liked 读取状态
     3. this.setState({value: value}) 设置状态值
     
     > this.setState({value:this.state.value})修改state是异步的，setState的第二个参数里
     是一个回调函数 this.setState({..}, function() {..})
     
     > setState的处理过程：调用setState时，React会将传递过来的对象合并到当前的状态，然后进行一致化处理 -- 以最有效的方式，根据这个新的状态来更新UI
     
     > 
     
- 表单

    用户在表单填入的内容，属于用户跟组件的互动，所以不能用 this.props
    
    用 event.target.value 获取表单的数据
    
- 样式

    React 组件样式是一个对象，所以第一重大括号表示这是 JavaScript 语法，第二重大括号表示样式对象
    ```
    style={{opacity: this.state.opacity}}
    ```
    
- 组件的生命周期
[http://react-china.org/t/react/1740](http://react-china.org/t/react/1740)
    - 生命周期的三个状态
        - Mounting: 已插入真实DOM
        - Updating：正在被重新渲染
        - Unmounting：已移出真实DOM
    - 处理函数--will：函数在进入状态之前调用，did：函数在进入状态之后调用
        > 三种状态的五种处理函数

        - componentWillMount()
        - componentDidMount()
        - componentWillUpdate(object nextProps, object nextState)
        - componentDidUpdate(object prevProps, object prevState)
        - componentWillUnmount()
        
        > 两种特殊状态的处理函数
        
        - componentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用
        - shouldComponentUpdate(object nextProps, object nextState)：组件判断是否重新渲染时调用
        
- Ajax
    
    组件的数据来源，通常是通过 Ajax 请求从服务器获取，可以使用 componentDidMount 方法设置 Ajax 请求，等到请求成功，再用 this.setState 方法重新渲染 UI

- ### PropTypes 补充

```js
// Hello 组件为例
Hello.propTypes = { // 校验属性
    content: PropTypes.string
}
Hello.defaultProps = { // 设置默认值
    content: "Hello World"
}
```

- 内置propsTypes 校验器

#### js基元校验器

校验器 | 描述
---|---
PropTypes.array | 属性必须是一个数组
PropTypes.bool | 属性必须是一个布尔值(true/false)
PropTypes.func | 属性必须是一个函数
PropTypes.number | 属性必须是一个数字(或者可以解析成数字的值)
PropTypes.object | 属性必须是一个对象
PropTypes.string | 属性必须是一个字符串

#### 组合类型校验

校验器 | 描述
---|---
PropTypes.oneOfType | 属性必须属于指定的一组数据类型中的一种, 例: PropsTpyes.oneOfType([ PropTypes.string, PropTypes.number])
PropTypes.arrayOf | 属性必须是一种指定类型数据的数组, 例: PropTypes.arrayOf(PropTypes.number)
PropTypes.objectOf | 属性必须是一个带有指定类型值的属性值的对象, 例: PropTypes.objectOf(PropTypes.number)
PropTypes.shape | 属性必须是一个符合特定格式的对象, 它需要拥有同一组属性 例: PropTypes.shape({color: PropTypes.string, fontSize: PropTypes.number })

#### 特定的校验器

校验器 | 描述
---|---
PropTypes.node | 属性必须是一个可以渲染的值: 数字/字符串/元素或数组
PropTypes.element | 属性必须是一个React元素
PropTypes.instanceOf | 属性必须是指定类的实例(js的操作符instanceOf)
PropTypes.oneOf | 确保属性被限制为一组枚举值中的一项, 例: PropTypes.oneOf(['News', 'Photos'])

- 自定义propTypes校验器
校验器本质上是一个函数, 例: 检验字符长度

```js
let titlePropType = (props, propName, componentName) => {
    if(props[propName]) {
        let value = props[propName];
        if(typeof value !== string || value.length > 80) {
            return new Error(
            `${propName} in ${componentName} is longer than 80 characters`
            );
        }
    }
}

Hello.propTypes = {
    title: titlePropType
}
```

---

#### Redux
##### Redux使用场景：多交互、多数据源，如果项目简单，不需要使用

##### 设计思想：
- web应用是一个状态机，视图与状态一一对应
- 所有的状态，保存在一个对象里面

##### 基础
- store：保存数据的地方，整个应用中只能有一个

    Redux 提供createStore这个函数，用来生成 Store。
    ```
    import { createStore } from 'redux';
    const store = createStore(fn);
    ```
    上面代码中，createStore函数接受另一个函数作为参数，返回新生成的 Store 对象。
- state：store包含所有数据，通过state获取当前时刻的数据集合

    当前时刻的 State，可以通过store.getState()拿到。
    ```
    import { createStore } from 'redux';
    const store = createStore(fn);
    
    const state = store.getState();
    ```
    Redux 规定， 一个 State 对应一个 View。只要 State 相同，View 就相同。你知道 State，就知道 View 是什么样，反之亦然。
- Action：State的变化会导致View的变化，但是用户接触不到State，所以State的变化必须是View导致的。Action就是View发出的通知，表示State应该要发生变化了。
    ```
    const action = {
      type: 'ADD_TODO', // 必须，表示Action的名称
      payload: 'Learn Redux'
    };
    ```
    Action可以理解为描述当前发生的事，改变State的唯一方法，就是使用Action，它会运送数据到Store。
- Action Creator
    
    定义一个函数来生成Action
    ```
    const ADD_TODO = '添加 TODO';

    function addTodo(text) {
      return {
        type: ADD_TODO,
        text
      }
    }
    
    const action = addTodo('Learn Redux');
    ```
- store.dispatch()
    
    是view发出Action的唯一方法

- #### Reducer

    Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State

- 纯函数
    - 同样的输入必定会得到同样的输出
    - 遵守的约束
        - 不得改写参数
        - 不能调用系统I/O 的API
        - 不能调用Date.now()或Math.random()等不纯的方法，因为每次的结果不一样
- store.subscribe()
    
    - 使用store.subscribe设置监听函数，一旦State发生变化，就自动执行这个函数
    ```
    import { createStore } from 'redux';
    const store = createStore(reducer);
    
    store.subscribe(listener);
    ```
    > 只要把view的更新函数（render或setState方法）放入listen，就会自动渲染
    
    - store.subscribe方法返回一个函数，调用这个函数就可以解除监听
    ```
    let unsubscribe = store.subscribe(() =>
      console.log(store.getState())
    );
    
    unsubscribe();
    ```
##### Store的实现

createStore方法还可以接受第二个参数，表示 State 的最初状态。这通常是服务器给出的。

##### Reducer的拆分

多个属性之间没有联系，可以将Reducer函数拆分，不同函数负责处理不同属性，最终合并为一个大的Reducer

用combineReducers方法进行合并
```
import { combineReducers } from 'redux';

const chatReducer = combineReducers({
  chatLog,
  statusMessage,
  userName
})

export default todoApp;
```
> 上面的写法，State属性名必须与子Reducer同名，否则，以键值对的形式

##### 进阶：中间件与异步操作

- 中间件

    中间件就是一个函数，对store.dispatch方法进行了改造，在发出 Action 和执行 Reducer 这两步之间，添加了其他功能

    用法：eg：使用redux-logger模块，加入中间件
    
    ```
    import { applyMiddleware, createStore } from 'redux';
    import createLogger from 'redux-logger';
    const logger = createLogger();
    
    const store = createStore(
      reducer,
      applyMiddleware(logger)
    );
    ```
    **注意：**
    
    1.createStore方法可以接受整个应用的初始状态作为参数，那样的话，applyMiddleware就是第三个参数了
    
    2.中间件的次序有讲究
    
- applyMiddleWares()

    Redux 的原生方法，作用是将所有中间件组成一个数组，依次执行
    
- 异步操作
    - 操作开始时，送出一个 Action，触发 State 更新为"正在操作"状态，View 重新渲染
    - 操作结束后，再送出一个 Action，触发 State 更新为"操作结束"状态，View 再一次重新渲染

- redux-thunk中间件

(***)

- redux-promise中间件

(***)


- #### React-Redux [使用教程](http://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html)
    - UI组件
        - 只负责 UI 的呈现，不带有任何业务逻辑
        - 没有状态（即不使用this.state这个变量）
        - 所有数据都由参数（this.props）提供
        - 不使用任何 Redux 的 API
    - 容器组件
        - 负责管理数据和业务逻辑，不负责 UI 的呈现
        - 带有内部状态
        - 使用 Redux 的 API
        > 一个组件既有 UI 又有业务逻辑：将它拆分成下面的结构：外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。

    - connect() 
    
        用于从UI组件生成容器组件
        ```
        import { connect } from 'react-redux'

        const VisibleTodoList = connect(
          mapStateToProps,//负责输入逻辑，即将state映射到 UI 组件的参数（props），
                            如果省略，Store 的更新不会引起 UI 组件的更新
          mapDispatchToProps//负责输出逻辑，即将用户对 UI 组件的操作映射成 Action
        )(TodoList)
        ```
    - mapStateToProps()
    
        建立一个从（外部的）state对象到（UI 组件的）props对象的映射关系
        
    - mapDispatchToProps()
    
        建立 UI 组件的参数到store.dispatch方法的映射
        
- <Provider>组件
    - React-Redux 提供Provider组件，可以让容器组件拿到state
    ```
    import { Provider } from 'react-redux'
    import { createStore } from 'redux'
    import todoApp from './reducers'
    import App from './components/App'
    
    let store = createStore(todoApp);
    
    render(
      <Provider store={store}>
        <App />
      </Provider>,
      document.getElementById('root')
    )
    ```
    > App的所有子组件就默认都可以拿到state了
    
    
---

#### React Router


#### Flux


#### CSS Modules

---
#### React 同构应用

> 服务端渲染React组件: React和React-DOM通过ReactDOMServer.renderToString方法内置支持在服务器端渲染组件, 该方法会对所需组件进行渲染, 并生成带有注释的HTML然后发送到浏览器, 在浏览器中, React会识别这些注释, 并只进行事件处理程序的加载.

1. 与纯客户端相比, 不再需要使用React App Boilerplate, 使用新的项目
2. 在服务器端渲染React组件
- 安装依赖: Express/EJS/React...
- 使用ejs模板

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>Isomorphic React</title>
    </head>
    <body>
        <div id="root"><%- content %></div>
    </body>
</html>
```
- 渲染React组件
> 在Express中不会使用JSX, 为在JSX之外实例化React组件, 我们需要在调用之前使用工厂方法对组件进行封装.

```js
import express from 'express'
import React from 'react'
import { renderToString } from 'react-dom/server'
import Hello from './app/components/Hello'

const app = express();
app.set('views', './');
app.set('view engine', 'ejs');
ap.use(express.static(__dirname + '/public'));

/* 获取数据之类的操作 */
...

const HelloFactory = React.createFactory(Hello);

app.get('/', (request, response) => {
    let componentInstance = HellowFactory({initialData: contacts});
    response.render('index', {
        content: renderToString(componentInstance)
    });
});

app.listen(3000, () => {
    console.log('Express app listening on port 3000');
});
```

- 在客户端中挂载React
> 打包js, 和平常的操作相同
> 读取初始化时的数据

```html
<div><%- content %></div>
<script id="initial-data" type="application/json">
    <%- reactInitialData %>
</script>
<script type="text/javascript" src="bundle.js"></script>
```

```js
app.get('/', (request, response) => {
    let componentInstance = HellowFactory({initialData: contacts});
    response.render('index', {
        reactInitialData: JSON.stringify(contacts),
        content: renderToString(componentInstance)
    });
});
```

```js
import React from 'react'
import { render } from 'react-dom'
import Hello from 'app/components/Hello'

let initialData = document.getElementById('initial-data').textContent;
if(initailData.lnegth > 0) {
    initialData = JSON.parse(initialData);
}
render(<Hello initialData={initialData} />, document.getElementById('root'));
```

3. 路由

---
#### 测试

- jest
jest.autoMockOff(): autoMock可以让一个模块独立于其依赖的项, 但是部分代码需要禁用, 以更好的测试