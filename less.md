# less语法
- 使用koala自动编译less文件，以后维护也维护less。
- /\* 我是被编译的注释,保留 \*/ <br> // 我是不被编译的注释。 不保留

### 变量

- 定义与使用变量  
- 变量存在作用域。和js大体一致 
- @变量名: 值
```
    // 编译前
    @bianliang: 300px;
    .box {
        width: @bianliang;
        height: @bianliang;
    }

    // 编译后
    .box {
      width: 300px;
      height: 300px;
    }

```

### 混合(Mixin)
- 可以混合类选择器和id选择器
```
    .border_radius(@radius:5px) {     // @radius:5px  该变量的默认值是5px
        -webkit-border-radius: @radius;
        -moz-border-radius: @radius;
        border-radius: @radius;
    }

    .radius_test {
        width: 100px;
        height: 100px;
        background: yellow;

        // 不传入值就是默认值
        .border_radius(20px);  
    }


    .border_radius {     
        border-radius: 5px;
    }

    .radius_test {
        width: 100px;
        height: 100px;
        background: yellow;
        
        // 没有变量就可以不加括号，也可以加,建议加，以便区分
        .border_radius;  
    }
```

### 匹配模式
- 匹配不到就默认为空，不报错
- 匹配模式相当于把可复用的代码抽象出来

```
    .triangle(top,@w:5px,@c:#ccc) {
        border-width: @w;
        border-color: transparent transparent @c transparent;
        border-style: solid;
    }
    .triangle(bottom,@w:5px,@c:#ccc) {
        border-width: @w;
        border-color: @c transparent transparent transparent;
        border-style: solid;
    }
    .triangle(@_,@w:5px,@c:#ccc) {  //不管匹配的是哪个，都会加上它
        width: 0;
        height: 0;
        margin-top: 20px;
    }
    .sanjiao {
        .triangle(bottom)
    }


```

### 运算
- 运算的2者，其中一个带有单位就行
- 颜色的运算不常用。大概是转换成GRB数值后运算的

```
    @yunsuan: 300px;
    .box {
        width: @yunsuan + 200;
    }
```

### 嵌套规则
- 和对象的访问层级类似
- &:hover 类似这种方式控制伪类
- & 代表他的上一层选择器

```
    list {}
    list a{}
    list span{}
    list a span{}

    list {
        a {
            span {

            }
            &:hover {

            }
        }
        span {

        }
    }
```

### @arguments变量
- @arguments包含了所有传递进来的参数。

### 避免编译
- 有时候我们需要输出一些不正确的css语法或者使用一些LESS不认识的专有语法。<br>要输出这样的值我们可以在字符串前加上一个 ~ <br>例如: width: ~'calc(300ox - 30px)'; 会原封不动的输出，不会编译。
