---
title: flex 笔记
date: 2017-10-23 13:23:58
tags:
- 前端
- css
- flex
categories:
- 笔记
- 前端
- css
---

#### [http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)

#### [http://www.ruanyifeng.com/blog/2015/07/flex-examples.html](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

---

#### 容器

<!--more-->

```
display: flex;
display: -webkit-flex;

// 行内元素

display: inline-flex;
display: -webkit-inline-flex;
```
> 设为flex后，子元素的float、clear、vertical-align将失效

![image](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png)

- 属性
    - flex-direction
    决定主轴的方向
    ```
    flex-direction: row | row-reverse | column | column-reverse;
    ```
    - flex-wrap
    是否换行
    ```
    flex-wrap: nowrap | wrap | wrap-reverse; // 不换行  换行，第一行在上方  换行，第一行在下方
    ```
    - flex-flow
    flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap
    ```
    flex-flow: <flex-direction> || <flex-wrap>;
    ```
    - justify-content
    在主轴上的对齐方式
    ```
    justify-content: flex-start | flex-end | center | space-between | space-around;
    ```
    > space-between：两端对齐，项目之间的间隔都相等。
    
    > space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。
    
    - align-items
    在交叉轴上如何对齐
    ```
    align-items: flex-start | flex-end | center | baseline | stretch;
    ```
    > baseline: 项目的第一行文字的基线对齐。
    
    > stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
    
    - align-content
    多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用
    ```
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
    ```

#### 项目
- 属性
    - order
    项目的排列顺序。数值越小，排列越靠前，默认为0
    ```
    order: <integer>;
    ```
    - flex-grow
    项目的放大比例，默认为0，即如果存在剩余空间，也不放大
    ```
    flex-grow: <number>; /* default 0 */
    ```
    > 如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍
    
    - flex-shrink
    定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小
    ```
    flex-shrink: <number>; /* default 1 */
    ```
    > 取值为0时，空间不足也不会缩小
    
    > 负值对该属性无效。
    
    - flex-basis
    定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
    ```
    flex-basis: <length> | auto; /* default auto */
    ```
    > 可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。
    
    - flex
    flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选
    ```
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
    ```
    > 有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)
    
    > 建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值
    
    - align-self
    属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch
    ```
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
    ```
    > 该属性可能取6个值，除了auto，其他都与align-items属性完全一致
    
    ---
    
#### 使用笔记:

- z-index

display为flex的元素，即使position为static也能设置z-index