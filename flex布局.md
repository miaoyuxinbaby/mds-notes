# flex语法笔记

- 设置为Flex布局后，子元素的float、clear和vertical-align属性将失效。
```
     .box {
        display: flex;
        display: inline-flex; // 行内元素
    }
```
- 容器的所有子元素自动成为容器成员，称为Flex项目(flex item)
- 主轴 main  交叉轴 cross
- 项目默认沿主轴排列，单个项目占据的主轴空间叫做main size,占据的交叉轴空间叫做cross size

### 容器的属性
+ flex-direction
+ flex-wrap
+ flex-flow
+ justify-content
+ align-items
+ align-content

###### flex-direction
- 决定主轴的方向，默认水平，起点在左
```
    .box {
        flex-direction: row; // 主轴为水平方向，起点在左端
        flex-direction: row-reverse;  // 起点左端
        flex-direction: column;     // 主轴为垂直，起点在上沿
        flex-direction: column-reverse; // 下沿
    }
```

###### flex-wrap
- 是否允许换行,默认不换行
```
    .box {
        flex-wrap: nowrap; // 不换行
        flex-wrap: wrap;   // 换行，第一行在上方
        flex-wrap: wrap-reverse; // 换行，第一行在下方
    }
```

###### flex-flow
- flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
```
    .box {
        flex-flow: <flex-direction> || <flex-wrap>
    }
```

###### justify-content
- 定义了项目在主轴上的对齐方式
- 它可能取5个值，具体对齐方式与轴的方向有关。下面假设主轴为从左到右。
```
    .box {
        justify-content: flex-start; // 左
        justify-content: flex-end;  // 右
        justify-content: center; // 中
        justify-content: space-between;  // 两端对齐，项目之间的间隔都相等。
        justify-content: space-around; // 每个项目两侧的间隔相等
    }
```

###### align-items
- 定义了项目在交叉轴上如何对齐
- 下面假设交叉轴从上到下
```
    .box {
        align-items: flex-start; //上
        align-items: flex-end; // 下
        align-items: center; // 中
        align-items: baseline; // 项目的第一行文字的基线对齐
        align-items: stretch; // 如果项目未设置高度或设为auto,将占满整个容器的高度。
    }
```

###### align-content
- 定义多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。
```
.box {
  align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

### 项目的属性
- 以下六个属性设置在项目上
+ order
+ flex-grow
+ flex-shrink
+ flex-basis
+ flex
+ align-self

###### order
- 定义项目的排列顺序。数值越小，排列越靠前，默认为0
```
    .item {
        order: <integer>
    }
```

###### flex-grow 
- 定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
```
    .item {
        flex-grow: <number>;
    }
```
- 如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

###### flex-shrink
- 定义项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
```
    .item {
        flex-shrink: <number>; // default 1
    }
```
- 如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。
负值对该属性无效。

###### flex-basis属性
- 定义了在分配多余空间之前，项目占据的主轴空间
- 浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
```
.item {
  flex-basis: <length> | auto; /* default auto */
}
```
- 它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。

###### flex
- flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。
```
.item {
  flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
}
```
- 该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值

###### align-self属性
- align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。
```
.item {
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```
- 属性可能取6个值，除了auto，其他都与align-items属性完全一致。
