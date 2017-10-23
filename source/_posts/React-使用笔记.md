---
title: React 使用笔记
date: 2017-10-23 11:58:39
tags:
- 前端
- react
categories:
- 笔记
- 前端
- react
---

#### react点击事件传参，通过bind传递
- this.handleclick.bind（this，要传的参数）handleclick（要传的参数，event）

<!--more-->

#### 表单的约束和非约束组件
- 约束组件：由React管理他的value
```
<input type='text' defaultValue='a'/>
```
- 非约束组件：由原生的DOM管理
```
<input type="text" value={this.state.name} onChange={this.handleChange} />

//...省略部分代码
handleChange: function(e) {
  this.setState({name: e.target.value});
}
```

#### 父组件将state的数据通过props传递给子组件，但是父组件使用setState改变数据后，子组件并没有更新props
- 在子组件中的生命周期 componentWillReceiveProps 中接收父组件的props
    > componentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用

#### react阻止事件冒泡
暂时遇到的问题是：一般出现冒泡，使用e.stopPropagation()即可；

但是当有事件是手动绑定到document上时，使用e.stopPropagation()不能阻止事件冒泡，此时需要使用e.nativeEvent.stopImmediatePropagation()，此方法不但阻止冒泡，还能阻止在当前事件触发元素上，触发其它事件

> 说明：

> 1. React为了提高效率，把事件都委托给了document，所以如果事件直接绑定到body可能会出现冲突

> 2. stopImmediatePropagation会在调用对应的回调函数之后屏蔽对应元素对应事件所有回调函数的调用，并且禁止冒泡，而stopPropagation函数只屏蔽冒泡，并不屏蔽自身的事件

#### react中的redux
```
 render(){
    const { value } = this.props;
    ...
```
这里定义value时使用const是因为
1. render中是不能直接修改value的，必须通过dispatch修改，所以定义为常量
2. 每次redux更新数据时，会重新渲染，然后value重新定义

#### react中的setState方法
不保证是同步执行，一般看作是异步处理，setState中可以接收第二个参数，第二个参数是一个回调函数，可以在里面获取到最新的state
> 在render中获取到的state是最新的，因为在state更新后都会再次执行render

#### react性能优化

[https://segmentfault.com/a/1190000006254212](https://segmentfault.com/a/1190000006254212)

#### immutable
[http://facebook.github.io/immutable-js](http://facebook.github.io/immutable-js)
[https://github.com/camsong/blog/issues/3](https://github.com/camsong/blog/issues/3)

#### 由于引用类型和一些复杂的数据结构存在，数据的可变性会导致组件间数据通讯的错乱，因此使用immutable能够优化react的性能，减少bug的出现

#### API:
- fromJS(): 深度解析转换js对象和数组为Immutable的Maps和Lists
- is(v1, v2)
- isIndexed()
- List: 一个有序索引的集合，类似于js中的数组
    - size
    - set(idx, val): 设置或修改一个值，返回一个新的List
    - delete(idx): 删除一个值，返回一个不包含这个值的List
    - insert(idx, val): 返回一个新的List
    - clear(): 返回一个空的List
    - push(val)/pop()/unshift(val)/shift(): 返回一个新的List
    - ...
- Map: 一个无序的集合，允许使用任意值作为key，包括NaN


#### 错误提示 
**例如：Warning: ** is changing a controlled input of type checkbox to be uncontrolled. Input elements should not switch from controlled to uncontrolled (or vice versa). Decide between using a controlled or uncontrolled input element for the lifetime of the component.**

> 此错误为input中的value或checked等属性没有做为空时的处理，解决方法 eg：value={ val||'' }  checked={ check||false }

#### rudux
> redux中的数据尽量保持原来的结构，手动设置redux的数据为不可变，会有增加性能损耗的可能，并且可能会导致一直重复渲染的bug。

#### 默认props
```
// 方式1: 组件内部
class Hello extends Component {
    static defaultProps = {
        
    }
    ...
}

// 方式2: 组件外部
Hello.defaultProps = {
    name: 'xxx'
}

```

#### props 类型检查和约束
```
import { PropTypes } from 'react'

static propsTypes = {
    name: PropTypes.string,
    age: PropTypes.string.isRequired,
}
```

### this.props.children
表示组件的所有子节点
- 当前组件没有子节点：undefined
- 有一个子节点：object
- 多个子节点：array

react的工具方法：React.Children，避免类型问题
```
// 例子：
<ul>
{
    React.Children.map(this.props.children, function(child) {
        return <li>{child}</li>
    })
}
</ul>
```

#### Super expression must either be null or a function, not undefined
说明extends出了问题

#### React 页面过度动画
[https://segmentfault.com/a/1190000010539892](https://segmentfault.com/a/1190000010539892)

#### 一些工具

- Immutability Helpers: 简单的不可变数据工具
- React Transition Group: react的动画工具
- React DnD: react拖放工具
- React Addons Perf: react性能分析工具
- shallowCompare: 浅比较插件, 可配合shouldComponentUpdate使用

#### React通过refs获取组件中的dom元素

```js
ReactDom.findDOMNode(this.refs.xxx)
```