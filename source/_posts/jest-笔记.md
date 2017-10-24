---
title: jest 笔记
date: 2017-10-24 11:53:30
tags:
- 前端
- 测试
- jest
categories:
- 笔记
- 前端
- 测试
---

[http://facebook.github.io/jest/zh-Hans/](http://facebook.github.io/jest/zh-Hans/)
[https://segmentfault.com/a/1190000009220530#articleHeader0](https://segmentfault.com/a/1190000009220530#articleHeader0)

<!--more-->

#### 参考：
- [http://www.aliued.com/?p=4095](http://www.aliued.com/?p=4095)
- [https://github.com/superman66/react-test-demo](https://github.com/superman66/react-test-demo)
---

### 基础

- 使用匹配器
    - 普通匹配器
    
    > toBe ： ===
    > toEqual : 递归检查对象或数组的每个字段
    > 前面加 not 表示不相等
    
    ```js
    test('two plus two is four', () => {
        expect(2 + 2).toBe(4);
        // expect(2 + 2).not.toBe(2);
    }
    ```
    
    - 区分 undefined、 null，和 false
        - toBeNull 只匹配 null
        - toBeUndefined 只匹配 undefined
        - toBeDefined 与 toBeUndefined 相反
        - toBeTruthy 匹配任何 if 语句为真
        - toBeFalsy 匹配任何 if 语句为假
        
    - 数字
        - toBeGreaterThan 大于
        - toBeGreaterThanOrEqual 大于等于
        - toBeLessThan 小于
        - toBeLessThanOrEqual 小于等于
        - toBeCloseTo 比较浮点数相等
        
    - 字符串
        - toMatch(/xxx/) 匹配正则
        
    - 数组
        - toContain 数组是否包含特定子项
        
    - 例外
        - 测试的特定函数抛出一个错误，在它调用时，使用 toThrow
        
- 测试异步

    - 回调
        > 使用单个参数
        
        ```js
        test('the data is peanut butter', done => {
          function callback(data) {
            expect(data).toBe('peanut butter');
            done();
          }
        
          fetchData(callback); // 异步
        });
        ```
    - Promise
    
    ```js
    // fetchData 返回Promise
    test('the data is peanut butter', () => {
      expect.assertions(1); // 需要catch时使用，验证断言的数量
      return fetchData().then(data => { // 必须return
        expect(data).toBe('peanut butter');
      });
    });
    ```
    
    - .resolves / .rejects #
    
    ```js
    test('the data is peanut butter', () => {
      expect.assertions(1);
      return expect(fetchData()).resolves.toBe('peanut butter'); // 必须return 
    });
    ```
    
    - Async/Await
    
    ```js
    test('the data is peanut butter', async () => {
      expect.assertions(1);
      const data = await fetchData();
      expect(data).toBe('peanut butter');
    });
    
    test('the fetch fails with an error', async () => {
      expect.assertions(1);
      try {
        await fetchData();
      } catch (e) {
        expect(e).toMatch('error');
      }
    });
    ```
    
- 安装和移出

    - 为多次测试重复设置: beforeEach 和 afterEach
    
    ```js
    beforeEach(() => {
      initializeCityDatabase();
    });
    
    afterEach(() => {
      clearCityDatabase();
    });
    
    test('city database has Vienna', () => {
      expect(isCity('Vienna')).toBeTruthy();
    });
    
    test('city database has San Juan', () => {
      expect(isCity('San Juan')).toBeTruthy();
    });
    ```
    > beforeEach 和 afterEach 异步的话，可以使用done，或return Promise的形式
    
    ```js
    beforeEach(() => {
      return initializeCityDatabase();
    });
    ```
    
    - 一次性设置: beforeAll 和 afterAll 
    > 与多次的使用相同
    
    - 范围
    > 默认情况下，before 和 after 的块应用到文件中的每个测试。 此外可以组合在一起使用一个 describe 块的测试。 当他们在 describe 块内部，before 和 after 的块只适用于该 describe 块内测试
    
    - 测试失败
    
    > 如果测试失败，第一件事要检查测试失败时它是否是唯一运行的测试
    
    > 在 Jest 中很容易地运行只有一个测试
    
    ```js
    test.only('this will be the only test that runs', () => {
      expect(true).toBe(false);
    });
    
    test('this test will not run', () => {
      expect('A').toBe('A');
    });
    ```
    
- 模拟函数
    
    - 使用模拟函数
    - .mock属性
    - Mock的返回值
    - 模拟实现
    - 自定义匹配器

#### 快照测试: 测试UI

#### 测试React

- 快照

> 一个例子：

```js
// Link.react.js
import React from 'react';
const STATUS = {
  HOVERED: 'hovered',
  NORMAL: 'normal',
};
export default class Link extends React.Component {
  constructor(props) {
    super(props);
    this._onMouseEnter = this._onMouseEnter.bind(this);
    this._onMouseLeave = this._onMouseLeave.bind(this);
    this.state = {
      class: STATUS.NORMAL,
    };
  }
  _onMouseEnter() {
    this.setState({class: STATUS.HOVERED});
  }
  _onMouseLeave() {
    this.setState({class: STATUS.NORMAL});
  }
  render() {
    return (
      <a
        className={this.state.class}
        href={this.props.page || '#'}
        onMouseEnter={this._onMouseEnter}
        onMouseLeave={this._onMouseLeave}>
        {this.props.children}
      </a>
    );
  }

}
```

```js
// test Link.react.js
import React from 'react';
import Link from '../Link.react';
import renderer from 'react-test-renderer';

test('Link changes the class when hovered', () => {
  const component = renderer.create(
    <Link page="http://www.facebook.com">Facebook</Link>
  );
  let tree = component.toJSON();
  expect(tree).toMatchSnapshot();

  // manually trigger the callback
  tree.props.onMouseEnter();
  // re-rendering
  tree = component.toJSON();
  expect(tree).toMatchSnapshot();

  // manually trigger the callback
  tree.props.onMouseLeave();
  // re-rendering
  tree = component.toJSON();
  expect(tree).toMatchSnapshot();
});
```
> 下次你运行测试时，渲染的结果将会和之前创建的快照进行比较。 代码变动时快照必须按顺序提交。 当快照测试失败，你需要去检查是否是你想要或不想要的变动。 如果变动符合预期，你可以通过jest -u调用Jest从而重写存在的快照

- DOM测试: 使用Enzyme
> 要使用 enzyme 你需要运行 npm install --save-dev enzyme。如果你使用 React 15.5.0 以下版本，你还需要安装 react-addons-test-utils


---
### 使用

- 生成测试报告

1. 运行时使用

```
# 在create-react-app中使用
npm test -- --coverage
# 等效于
jest --coverage
```

2. 在配置文件中

> 添加 --coverage

> 可在package.json的scripts中添加

```js
// 在create-react-app中的使用
"test-p": "node scripts/test.js --env=jsdom --coverage"
```

> 运行后，会生成coverage文件夹，可在浏览器中查看测试的情况

