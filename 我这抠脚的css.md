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

```html
选择器	描述
selector[attr]	选择selector所匹配的元素，且该元素拥有attr属性。可以省略selector，表示匹配所有拥有attr属性的元素
selector[attr=val]	选择selector所匹配的元素，且该元素拥有值为val的attr属性。可以省略selector，表示匹配所有拥有值为val的attr属性的元素
selector[attr~=val]	选择selector所匹配的元素，且该元素attr属性值具有多个空格分隔的值，其中一个值等于val。可以省略selector
selector[attr*=val]	选择selector所匹配的元素，且该元素attr属性值的任意位置包含val。可以省略selector
selector[attr^=val]	选择selector所匹配的元素，且该元素attr属性值以val开头。可以省略selector
selector[attr$=val]	选择selector所匹配的元素，且该元素attr属性值以val结尾。可以省略selector
```