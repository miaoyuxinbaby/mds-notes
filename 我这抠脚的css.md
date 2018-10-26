# 我这抠脚的css

text-align: justify; 实现两端对齐文本效果

当然了， 支持flex就用flex

```html
span
  几个字
```

```css
span {
  /* 控制span的宽度 */
  width: 100px;
  text-align: justify;
}
span::after{
  content: '';
  display: inline-block;
  width: 100%;
}
```

- position: fixed， 当元素祖先的transform属性非none时，容器由视口改为改祖先

```html
<div class="parent">
  <div class="fix"></div>
</div>
```

```css
.parent {
  margin: 50px;
  border: 2px solid red;
  height: 300px;
  width: 300px;
  transform: translate(0);
}
.fix {
  position: fixed;
  height: 100px;
  width: 100px;
  background: green;
  left: 10px;
  top: 10px;
}
```

```txt
选择器 描述
selector[attr] 选择selector所匹配的元素，且该元素拥有attr属性。可以省略selector，表示匹配所有拥有attr属性的元素
selector[attr=val] 选择selector所匹配的元素，且该元素拥有值为val的attr属性。可以省略selector，表示匹配所有拥有值为val的attr属性的元素
selector[attr~=val] 选择selector所匹配的元素，且该元素attr属性值具有多个空格分隔的值，其中一个值等于val。可以省略selector
selector[attr*=val] 选择selector所匹配的元素，且该元素attr属性值的任意位置包含val。可以省略selector
selector[attr^=val] 选择selector所匹配的元素，且该元素attr属性值以val开头。可以省略selector
selector[attr$=val] 选择selector所匹配的元素，且该元素attr属性值以val结尾。可以省略selector
```

## BFC

创建一个独立的渲染区域，并规定了 block-level box 如何布局，且与这个区域外部毫不相关

BFC布局规则如下(注意BFC只影响块儿级盒)：

- 内部Box按垂直方向一个接一个的放置
- Box垂直方向的距离由margin值决定，并且属于同一个BFC的两个相邻的box的margin值会重叠
- 每个元素的 margin-box 的左边与包含块儿 border-box 的左边相接触
- BFC的区域不会与浮动盒子重叠
- BFC就是一个隔绝的容器，容器里面的子元素不影响外面元素的布局，反之亦然
- 计算BFC的高度时，浮动元素也参与计算

以下CSS属性，将会触发BFC：

- 根元素，即 `<html>`
- float 属性值不为 none
- position 属性的值为 absolute 或 fixed
- overflow 属性值不为 visible
- display 属性值为 inline-block, table-cell, table-caption

## 堆叠上下文

```html
根元素 (HTML),
z-index 值不为 "auto"的 绝对/相对定位，
一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex，
opacity 属性值小于 1 的元素（参考 the specification for opacity），
transform 属性值不为 "none"的元素，
mix-blend-mode 属性值不为 "normal"的元素，
filter值不为“none”的元素，
perspective值不为“none”的元素，
isolation 属性被设置为 "isolate"的元素，
position: fixed
在 will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值
-webkit-overflow-scrolling 属性被设置 "touch"的元素
```