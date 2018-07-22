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