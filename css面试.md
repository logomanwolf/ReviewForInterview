## 各种图形的绘制

> tips：如何remember
>
> 当```width ```和`height`为0时，将正常的边框绘制出来是这样的
>
> ![image-20210222181722692](D:\前端面试准备\画图形.png)
>
> border四个方向的值代表了各个方向基准线到中心的距离。
>
> `border-top-width`为0
>
> ![image-20210222182032941](D:\前端面试准备\画图2.png)
>
> `border-bottom-width`为0
>
> ![image-20210222182117437](D:\前端面试准备\border-bottom.png)
>
> `border-left-width`为0
>
> ![image-20210222182154400](D:\前端面试准备\border-left.png)
>
> `border-right-width`为0
>
> ![image-20210222182223935](D:\前端面试准备\border-right.png)
>
> 这样一来，然后将对应的颜色设成parent就能得到相应的三角形了
>
> `border-right-width`为 *0* ,`border-top-color`和`border-bottom-color`为 *transparent* 时
>
> ![image-20210222182912792](D:\前端面试准备\border-tra.png)
>
> 同样的也可以修改 `width` 来获得不同形状的三角形,例如将`border-top-width`改成原来的一半
>
> ![image-20210222184149079](D:\前端面试准备\变形.png)

其他的形状都可以看成三角形和矩形的组合。

例如：

梯形

![image-20210222184605170](D:\前端面试准备\梯形.png)

```css
div.triangular {
    height: 0px;
    width: 0px;
    border-left: 200px solid yellow;
    border-right: 200px solid red;
    border-bottom: 200px solid red;
}
```

更多其他的

https://www.webhek.com/post/40-css-shapes.html.

## **box-sizing属性**

告诉浏览器如何计算一个元素是总宽度和总高度

###### content-box

width = content width;
height = content height

###### border-box

width = border + padding + content width
heigth = border + padding + content heigth

##### 让div填满剩下的空间

方法一：top bottom定位法

关键代码：

```css
div.parent{
    position:relative;
}
div.children2{
    position:absolute;
    top:100px;
    bottom:0px;
}

```

方法二：用grid布局

关键代码：

```css
div.parent{
	display:grid;
    grid-template-rows:100px auto;
}
```

## position的几种取值

###### 默认：static,没有定位，元素出现在正常流当中，忽略top,bottom,left,right,z-index等属性.

###### relative: 相对定位，相对正常值进行定位.(用top,bottom,left,right进行定位)

> 注意，这里的top,left,bottom,right和下面的***定位方式***不同，这里可以把它想象成一个从某个方向施加的力，比如top:30px, 指从top方向施加的力，使得元素向下移动了30px;
>
> 另外，设置为相对定位的元素框会偏移某个距离。元素仍然保持其未定位前的形状，它原本所占的空间仍保留。注意，在使用相对定位时，无论是否进行移动，元素仍然占据原来的空间。因此，移动元素会导致它覆盖其它框。

###### absolute: 根据上一个非默认定位的父标签作为定位原点.(用top,bottom,left,right进行定位)，不存在于正常的文档流中

另外 absolute 相对于 static 定位以外的第一个父元素进行定位。	

[`top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/top)，[`bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/bottom)，[`left`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/left)和[`right`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/right)以不同的方式在绝对定位。 它们指定元素应距离每个包含元素的边的距离，而不是指定元素应该移入的方向。 

> **注意：**如果需要，可以使用[`top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/top)，[`bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/bottom)，[`left`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/left)和[`right`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/right) 调整元素大小。 尝试设置 `top: 0; bottom: 0; left: 0; right: 0;` 和 `margin: 0;` 对你定位的元素，元素变成铺满包含元素的效果

###### fixed: 生成固定定位的元素，相对于浏览器窗口进行定位.(用top,bottom,left,right进行定位),当滚动的时候是不会随着滚动条滚动的。

###### sticky: 相对位置和固定位置的混合体

###### 直到它滚动到某个阈值点（例如，从视口顶部起10像素）为止，此后它就变得固定了。

https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Positioning

## css3 width

css3 width 中添加了 fit-content 和 max-content

`fit-content` div的宽度根据内容收缩,配合`margin`:`auto`可以使元素居中

`max-content`当文字超过最大限制宽度时不会换行

### 属性选择器

###### 存在和值（presence and value）属性选择器

- [attr] : 选择包含该属性的所有元素

- [attr=val] : 该选择器选择属性值为val的所有元素

- [attr~=val] ：选择属性值（以空格间隔出多个值）包含val的所有元素，比如class中以空格间隔的一个类

###### 子串值（Substring value）属性选择器

这种情况的属性选择器也被称为“伪正则选择器”，因为它们提供类似 regular expression 的灵活匹配方式（但请注意，这些选择器并不是真正的正则表达式）：

- [attr|=val] : 选择attr属性的值以val（包括val）或val-开头的元素（-用来处理语言编码）。
- [attr^=val] : 选择attr属性的值以val开头（包括val）的元素。
- [attr$=val] : 选择attr属性的值以val结尾（包括val）的元素。
- [attr*=val] : 选择attr属性的值中包含字符串val的元素。

###### 伪元素

[伪元素（Pseudo-element）](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements)跟伪类很像，但它们又有不同的地方。它们都是关键字 —— 但这次伪元素前缀是两个冒号 (::) —— 同样是添加到选择器后面达到指定某个元素的某个部分。

::after
::before
::first-letter
::first-line
::selection
::backdrop

## Float布局

https://www.cnblogs.com/wzndkj/p/10362371.html

######  `float` 和 `absolute` 的区别

 `float` 和 `absolute` 的区别是前者只脱离普通文档流，而不脱离文本流，也就是其他元素的文本依然当它的文本的存在，影响到了其之后的文档流。而且会形成BFC，但是`absolute` 脱离了文档流和文本流，其他元素会当它完全不存在。

https://blog.csdn.net/u013594477/article/details/80735530

## 居中的实现

```css
margin: o auto;
```

## css权重表

![css选择器权重表](D:\前端面试准备\css选择器权重表.jpg)

1. 常用选择器权重优先级：***!important > id > class > tag***
2. !important可以提升样式优先级，但不建议使用。如果!important被用于一个简写的样式属性，那么这条简写的样式属性所代表的子属性都会被应用上!important。 例如：*background: blue !important;*
3. 如果两条样式都使用!important，则权重值高的优先级更高
4. 在css样式表中，同一个CSS样式你写了两次，后面的会覆盖前面的
5. 样式指向同一元素，权重规则生效，权重大的被应用
6. 样式指向同一元素，权重规则生效，权重相同时，就近原则生效，后面定义的被应用
7. 样式不指向同一元素时，权重规则失效，就近原则生效，离目标元素近的样式被应用

## margin-left:-100%

当margin为负数时，元素相对原来的位置向左移动，其后面的元素位置也会随之变化。超出视图区域不可见。

那为什么在圣杯布局中margin-left设置为-100%时，后面的left元素直接跑到上层了呢？主要是浮动布局的原因。在浮动布局中，同一级别的浮动元素会像inline元素一样地排开，如果宽度不够就会换行，但是由于margin-left=-100%,left元素则会向左移动父元素的宽度，刚好就会在第一排最左边，重叠在center上面。

## 圣杯布局

https://juejin.cn/post/6844903817104850952

![圣杯布局1](D:\前端面试准备\圣杯布局1.png)

关于这一步转到下一步：#right{margin-right:-150px}

![圣杯布局2](D:\前端面试准备\圣杯布局2.png)

我的理解，该操作相当于将元素向右移动了150px,使之完全超出了父元素的内容区，因此换行对他不起作用了，就会恢复到原来类似于inline的布局了。

## css3特性

css `background` 使用 `background-clip`可以设置 `background` 的范围

### transform-origin

transform-origin设置旋转的原点，默认图形原点，用偏移左上角的数值，方向关键字或百分比来表示

![](D:\前端面试准备\transform1.jpg)

```javascript
transform-origin:50% 0 
```



![](D:\前端面试准备\transform2.jpg)

```js
transform-origin: 100% 50%`` 
```



![](D:\前端面试准备\transform3.jpg)

transform-origin : 0 0

![](D:\前端面试准备\transform4.jpg)

### animation

https://www.w3school.com.cn/css/css3_animations.asp

`@keyframes`用来定义一个关键帧

`animation` 动画名称，一个周期花费时间，运动曲线（默认ease），动画延迟（默认0），播放次数（默认1），是否反向播放动画（默认normal），是否暂停动画（默认running）

```css
@keyframes example {
  0%   {background-color:red; left:0px; top:0px;}
  25%  {background-color:yellow; left:200px; top:0px;}
  50%  {background-color:blue; left:200px; top:200px;}
  75%  {background-color:green; left:0px; top:200px;}
  100% {background-color:red; left:0px; top:0px;}
}
/* 应用动画的元素 */
div {
  width: 100px;
  height: 100px;
  position: relative;
  background-color: red;
  animation-name: example;
  animation-duration: 4s;
}

@keyframes logoText {
    0%{
        transform:translateX(30px);
        opacity: 0;
    }
    100%{
        transform:translateX(0);
        opacity: 1;
    }
}
```

### 换行

`word-break` 

![](D:\前端面试准备\wordBreak.png)

`word-wrap`

![](D:\前端面试准备\wordWrap.png)

### 文字溢出

```css
text-overflow:ellipsis (超出隐藏，用...表示)
```

### 渐变

```css
background-image:linear-gradient(to right, rgba(0,1,2,0.6),rgba(f,f,f,0.4))
```

## Grid布局

用grid布局实现