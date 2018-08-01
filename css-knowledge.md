- position: fixed， 当元素祖先的transform属性非none时，容器由视口改为改祖先

```html
<div class="parent">
  <div class="fix"></div>
</div>
```
```
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