text-align: justify; 	实现两端对齐文本效果

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