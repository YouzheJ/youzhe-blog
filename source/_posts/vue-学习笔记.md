---
title: vue 学习笔记
date: 2017-10-23 11:52:16
tags:
- 前端
- vue
categories:
- 笔记
- 前端
- vue
---

> #### ESLint  代码检查工具

- function () {} ,function后的 () 需要前后空格
- 字符串要用 '',不能用 ""
- 逗号 , 后需要一个空格
- 每一级的缩进都需要两个空格

<!--more-->

> ### 基础

- #### 实例
    - 每个vue.js应用都是通过构造函数Vue创建一个vue的根实例启动
    - vue没有完全遵循MVVN模式
    - 每个vue实例都会代理其data对象里所有的属性
    - vue.js中没有控制器的概念，组建的自定义逻辑可以分布在生命周期的钩子中
     ## 生命周期图示：
    ![生命周期图示](http://cn.vuejs.org/images/lifecycle.png)
- #### 模板
    - 底层上，vue将模板编译成虚拟DOM渲染函数，结合响应系统，在应用状态改变时，vue计算出重新渲染组件的最小代价，并应用到DOM上
    - 数据绑定使用Mustache语法（即双大括号），里面的数据会被解析为纯文本，而非HTML，若需输出html，需要使用v-html，但是这很容易导致xss攻击，**因此绝对不要对用户提供的内容插值**
    - 属性不能用Mustache，应使用v-bind，eg：v-bind:id="dynamicId"
    - Mustache支持js表达式
    - 指令支持参数和修饰符 eg： v-on:submit.prevent="onSubmit"
    - 过滤器只能在Mustache绑定和v-bind表达式中使用
    - 缩写：v-bind:href -- :href , v-on:click -- @click
- #### 计算属性
    - 计算属性是基于他们的依赖进行缓存，只有依赖改变时才会重新计算
    - methods 每次都会执行，不会产生缓存，消耗较大
- #### class与style绑定
    - v-bind:class=“{ active:isActive }”，可以与普通class共存
    - 可以使用数组 v-bind:class="[activeClass, errorClass]"
    - 内联v-bind:style，与class相似。属性的命名可以使用驼峰式或短横分隔
    - v-bind:style 特定的属性会补全兼容性的前缀
- #### 条件渲染
    - v-if，若想切换多个元素，可以使用<template>元素做包装
    - vue通常会复用已有的元素，而不是从头开始渲染，若不想复用需要给不复用的元素添加key属性
    - v-show 只是切换display，不支持<template>语法
    - v-for 比 v-if 有更高的优先级
- #### 列表渲染
    - v-for=“(item, index) in items” , 在v-for块中拥有对父作用域属性有完全访问权限
    - 支持<template>
    - v-for="(value, key) in object" 可以用于遍历一个对象的所有属性
    - v-for 更新已渲染的元素列表时，默认使用就地复用，若需要重新排序，需要指定一个唯一的key属性
    - 数组更新检测：push() pop() shift() unshift() splice() sort() reverse() 都会触发视图更新，filter(), concat(), slice() 为非变异的方法，不会改变原数组
    -  vue不能检测的情况，可以使用右侧的方法
    
    不能检测 |解决
    ---|---
    vm.items[indexOfItem] = newValue | Vue.set(example1.items, indexOfItem, newValue)Vue.set(example1.items, indexOfItem, newValue)  
    vm.items.length = newLengthvm.items.length = newLength | example1.items.splice(newLength)

- #### 事件处理器
    - v-on 监听事件 v-on:click
    - 事件修饰符 v-on:click.stop 在事件后添加，可以串联
        - .stop 阻止冒泡
        - .prevent 阻止默认行为
        - .capture 在事件捕获阶段监听事件
        - .self 只在该元素本身触发改事件（非子元素）
        - .once 事件只触发一次
    - 按键修饰符，如：v-on:keyup.13
        - .enter
        - .tab
        - .delete (捕获 “删除” 和 “退格” 键)
        - .esc
        - .space
        - .up
        - .down
        - .left
        - .right
        - .ctrl
        - .alt
        - .shift
        - .meta win键
        > 可通过 如：Vue.config.keyCodes.f1 = 112 添加自定义别名
- 表单控件绑定
    - 在表单元素上使用v-model创建双向数据绑定
    - 修饰符
        - .lazy 在change事件中同步
        - .number 将输入值转为number
        - .trim 过滤空格首尾空格
- #### 组件
    - 使用组件
        - 注册一个全局组件 Vue.component(tagName, options) 
        
        ```html
        <div id="example">
          <my-component></my-component>
        </div>
        ```
        ```js
        // 注册
        Vue.component('my-component', {
          template: '<div>A custom component!</div>'
        })
        // 创建根实例
        new Vue({
          el: '#example'
        })
        ```
        > w3c自定义标签的命名规则，小写、包含短杠
    
        - 局部注册
        ```js
        var Child = {
          template: '<div>A custom component!</div>'
        }
        new Vue({
          // ...
          components: {
            // <my-component> 将只在父模板可用
            'my-component': Child
          }
        })
        ```
        - DOM模板解析说明：Vue 只有在浏览器解析和标准化 HTML 后才能获取模版内容。在<ul> <table>等之中使用自定义组件，在渲染是会导致错误：
        ```
        <table>
          <my-row>...</my-row>
        </table>
        ```
        可通过is属性解决：
        ```
        <table>
          <tr is="my-row"></tr>
        </table>
        ```
        也可通过字符串模板（包括 .vue 组件）解决
    
        - data 必须是函数
        - 构成组件，父子组件间的数据通信
    - **Prop**
        - 组件实例的作用域是孤立的
        - 父组件通过props传递数据给子组件
        - HTML 特性是不区分大小写的。所以，当使用的不是字符串模版，camelCased (驼峰式) 命名的 prop 需要转换为相对应的 kebab-case (短横线隔开式) 命名
        - 动态地绑定数据到子模板的props，需要使用v-bind
        - 在模板中prop是字面量，即（如字符串），需要使用v-bind（如传入number类型）
        - prop是单向数据流
    - 自定义事件
        - 子组件通过自定义事件将数据传递给父组件
        - 使用v-on绑定自定义事件，Vue实例中，使用$on(eventName)监听事件，使用$emit(eventName)触发事件
        - 父组件可以在使用子组件的地方直接用 v-on 来监听子组件触发的事件
        - 使用 .native 修饰 v-on，绑定原生事件
        - 非父子组件间的通信，在简单的场景下，可以使用一个空的 Vue 实例作为中央事件总线，在复杂的场景下，应使用状态管理模式
    - 使用slot分发内容
        - 混合父组件的内容与子组件自己的模板，使用<slot>元素作为原始内容的插槽
        - 单个slot：
            父组件中的内容会插入并替换<slot>，如：
            - my-component组件下的模板
            ```
            <div>
              <h2>我是子组件的标题</h2>
              <slot>
                只有在没有要分发的内容时才会显示。
              </slot>
            </div>
            ```
            - 父组件模版
            ```
            <div>
              <h1>我是父组件的标题</h1>
              <my-component>
                <p>这是一些初始内容</p>
                <p>这是更多的初始内容</p>
              </my-component>
            </div>
            ```
            - 渲染结果
            ```
            <div>
              <h1>我是父组件的标题</h1>
              <div>
                <h2>我是子组件的标题</h2>
                <p>这是一些初始内容</p>
                <p>这是更多的初始内容</p>
              </div>
            </div>
            ```
        - 具名slot：
        slot可以用name属性来配置分发内容，匿名的slot作为备用插槽，父组件中用slot属性匹配相应name的<slot>
        - 作用域插槽：在父级中，具有特殊属性 scope 的 <template> 元素，表示它是作用域插槽的模板。scope 的值对应一个临时变量名，此变量接收从子组件中传递的 prop 对象
    - 动态组件
        - 通过使用保留的 <component> 元素，动态地绑定到它的 is 特性，我们让多个组件可以使用同一个挂载点，并动态切换，也可以直接绑定到组件对象上
        - <keep-alive>包裹<component>，可以把切换出去的组件保留在内存中，保留它的状态或避免重新渲染
    - 杂项
        - 可复用的组件：一次性组件跟其它组件紧密耦合没关系，但是可复用组件应当定义一个清晰的公开接口
        - 子组件索引：可以使用 ref 为子组件指定一个索引 ID ，直接访问子组件，eg：parent.$refs.profile
            > $refs 只在组件渲染完成后才填充，并且它是非响应式的。它仅仅作为一个直接访问子组件的应急方案——应当避免在模版或计算属性中使用 $refs
            
        - 异步组件，将组件定义为一个工厂函数，动态地解析组件的定义
        - 组件命名约定：注册组件（或者 props），使用字符串模版，可以使用 kebab-case ，camelCase ，或 TitleCase；在html中使用kebab-case 形式；
            > "/" 在字符串模版中有效。因为自闭的自定义元素是无效的 HTML ，浏览器原生的解析器也无法识别它。
        
        - 递归组件：有name属性时，在组件的模板内递归的调用自己
            > 注意避免死循环
        
        - 组件间的循环引用：在beforeCreate生命周期钩子中注册：
            ```
            beforeCreate: function () {
                  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue')
                }
            ```
        - 内联模板：如果子组件有 inline-template 特性，组件将把它的内容当作它的模板，而不是把它当作分发内容。但是这让模板的作用域难以理解。
        - X-Templates：另一种定义模板的方式，在script标签中使用text-template类型，并指定id。
        - 对低开销的静态组件使用v-once：尽管在 Vue 中渲染 HTML 很快，不过当组件中包含大量静态内容时，可以考虑使用 v-once 将渲染结果缓存起来。
        
> ### 进阶

- #### 深入响应式原理
    - 追踪变化：
            
> ### vue loader

- Vue的单文件组件.Vue，由三部分组成，<template></template> <script></script> <style></style>
- template和script只能有一个，但style可以有多个