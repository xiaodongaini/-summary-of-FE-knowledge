# -summary-of-FE-knowledge
主要总结前端遇到的琐碎知识点和一些难以理解的知识

## call() 和 apply() 的区别和用法 ##

### 它们各自的定义： ###

apply：应用某一对象的一个方法，用另一个对象替换当前对象。
call：调用一个对象的一个方法，以另一个对象替换当前对象。

### 它们的共同之处： ###

都“可以用来代替另一个对象调用一个方法，将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。”——摘自JScript5.5 .chm
它们的不同之处：

**apply**：最多只能有两个参数——新this对象和一个数组 argArray。如果给该方法传递多个参数，则把参数都写进这个数组里面，当然，即使只有一个参数，也要写进数组里面。如果 argArray 不是一个有效的数组或者不是 arguments 对象，那么将导致一个 TypeError。如果没有提供 argArray 和 thisObj 任何一个参数，那么 Global 对象将被用作 thisObj， 并且无法被传递任何参数。

**call**：则是直接的参数列表，主要用在js对象各方法互相调用的时候，使当前this实例指针保持一致,或在特殊情况下需要改变this指针。如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。 
更简单地说，apply和call功能一样，只是传入的参数列表形式不同：如 func.call(func1,var1,var2,var3)对应的apply写法为：func.apply(func1,[var1,var2,var3])

### 用法 ###

参考：
[http://my.oschina.net/warmcafe/blog/74973](http://my.oschina.net/warmcafe/blog/74973)

[http://www.cnblogs.com/huyong/p/4139875.html](http://www.cnblogs.com/huyong/p/4139875.html)

## 几道关于克隆的经典的题目 ##

### 题目：实现一个函数clone，可以对JavaScript中的5种主要的数据类型（包括Number、String、Object、Array、Boolean）进行值复制。 ###

答案：

```
/**
 * 对象克隆
 * 支持基本数据类型及对象
 * 递归方法
 */
function clone(obj) {
    var o;
    switch (typeof obj) {
        case "undefined":
            break;
        case "string":
            o = obj + "";
            break;
        case "number":
            o = obj - 0;
            break;
        case "boolean":
            o = obj;
            break;
        case "object": // object 分为两种情况 对象（Object）或数组（Array）
            if (obj === null) {
                o = null;
            } else {
                if (Object.prototype.toString.call(obj).slice(8, -1) === "Array") {
                    o = [];
                    for (var i = 0; i < obj.length; i++) {
                        o.push(clone(obj[i]));
                    }
                } else {
                    o = {};
                    for (var k in obj) {
                        o[k] = clone(obj[k]);
                    }
                }
            }
            break;
        default:
            o = obj;
            break;
    }
    return o;
}
```

### 如何消除一个数组里面重复的元素？ ###

```
// 方法一：
var arr1 =[1,2,2,2,3,3,3,4,5,6],
    arr2 = [];
for(var i = 0,len = arr1.length; i< len; i++){
    if(arr2.indexOf(arr1[i]) < 0){
        arr2.push(arr1[i]);
    }
}
document.write(arr2); // 1,2,3,4,5,6
```

### z-index 属性 ###

z-index : auto | number
z-index 属性设置元素的堆叠顺序，如果为正数，则离用户更近，为负数则表示离用户更远；
拥有更高堆叠顺序的元素总是会处于堆叠顺序较低的元素的前面；
z-index 仅能在定位元素上奏效（position 属性值为 relative 或 absolute 或 fixed的对象）。

参考资料：
[聂卫东-IE下z-index 的各种坑](http://www.cnblogs.com/Darren_code/archive/2012/03/05/z-index.html)

[杜瑶-你需要了解的z-index世界](http://blog.doyoe.com/2014/01/21/css/你需要了解的z-index世界/)

### 解决 ie6、ie7下float为right换行的情况 ###

遇到一个问题，float:right换行bug，情况是并列的几个块级元素如div和span，一些设置了左浮动一些设置右浮动，一行的宽度足够放下所有的块级元素，但此时ie6则显示右浮动的其中一块换行了

```
<div>
	<span class="left">左边</span>
	<span class="center">中间</span>
	<span style="float: right" class="right">右边</span>
</div>
```

.left和.center都没有 float 属性，然后在FireFox，google chrome及IE8等浏览器下都正常显示，可在Internet Explorer 6 下有bug：本来排一行的左边+中间+右边，可右边淘气的跳到了下一行。

#### 解决方法： ####

不用修改css什么的，只要把float的模块放到非float的前面即可。

上面的html结构重排下序：

```
<div>
	<span style="float: right" class="right">右边</span> 
	<span class="left">左边</span> 
	<span class="center">中间</span> 
</div>
````

### 低版本 IE 使用insertBefore() 方法时需要第二个参数不为空 ###

```
var parent=document.getElementById(parentID);
parent.insertBefore(child,(parent.hasChildNodes())? parent.childNodes[0]: null);
```

### JavaScript中对象类型检测 ###

JS中有两种数据类型的值：基本数据类型和引用数据类型

1. 基本数据类型：简单的数据段 存放在栈中 按值访问：**Undefined Null Boolean Number String**
2. 引用数据类型：由多个值构成的对象 存放在堆中 按地址访问 **Object Array Function 等**

#### 检测对象属于那种数据类型 ####

#### typeof ####

只能检测基本数据类型，不能检测引用数据类型

#### instanceof ####

1. 概述：instanceof 运算符可以用来判断某个构造函数的prototype属性是否存在另外一个要检测对象的原型链上
2. 语法：object instanceof constructor
3. 因为所有的实例都继承自Object, 所以object instanceof Object 返回皆为true
注意：
（1）如果表达式 obj instanceof Foo 返回true，则并不意味着该表达式会永远返回ture，因为Foo.prototype属性的值有可能会改变，改变之后的值很有可能不存在于obj的原型链上，这时原表达式的值就会成为false。

#### constructor ####

通过 obj.constructor 得到某个实例到对应的构造函数
在修改了构造函数（A）原型链且没有显式的修改A.prototype.constructor的情况下，结果将不是我们想要的

```
function A(){};
var a = new A();
console.log(a.constructor);

function B(){};

A.prototype = new B();
var a1 = new A();
console.log(a1.constructor);

A.prototype.constructor=A;

console.log(a1.constructor);

```

#### {}.toString || Object.prototype.toString 返回一个代表该对象的字符串 ####

```
function A(){};
var a = new A();
var b = new Array();
var c = 'abc';
var d = 123;
var e = new Date();

console.log({}.toString.call(a));
console.log({}.toString.call(b));
console.log({}.toString.call(c));
console.log({}.toString.call(d));
console.log({}.toString.call(e));
```
输出：
```
[object Object]
[object Array]
[object String]
[object Number]
[object Date]
```

### 使用rem设计移动端页面 ###

#### 首先设置meta标签 ####

```
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no" />
```

#### 以7.5为基准，设置 html 的 fontSize  ####

```
<script>
	document.documentElement.style.fontSize = document.documentElement.clientWidth / 7.5 + 'px';
</script>
```

在 width=device-width;initial-scale=1 设置为 1 的情况下，iphone6 的宽度为750px, html 的 fontSize = 750/7.5=100;
所以，div 的宽度为 2rem，为200px;

在iphone5 中，fontSize = 640/7.5=85.33333333333333;所以 div 的宽度为2rem=170.666666;

但是div所占的比例还是一样的，为2/7.5;

```
<html>
<head>
	<title></title>
	<meta charset="utf-8" />
	<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no" />
	<style>
	body{
		margin: 0;
		padding: 0;
	}
	.box{
		width: 2rem;
		height: 2rem;
		background: red;
	}
	</style>
</head>
<body>

	<div class="box"></div>

	<script>
	document.documentElement.style.fontSize = document.documentElement.clientWidth / 7.5 + 'px';
	</script>
</body>
</html>
```

### 理解BFC(Block Formatting Context（块级格式化上下文）) ###

Block Formatting Context（块级格式化上下文）是W3C CSS 2.1 规范中的一个概念，它决定了元素如何对其内容进行定位，以及与其他元素的关系和相互作用。

简单来讲，我们可以把它理解为，*我们在进行盒模型布局的时候，如果一个元素符合了成为BFC的条件，该元素成为一个隔离了的独立容器，元素内部元素会垂直的沿着其父元素的边框排列，和外部元素互不影响 。比如浮动元素会触发BFC，浮动元素内部的子元素主要受到该浮动元素的影响，而两个浮动元素之间是互不影响的。*

#### BFC规范中的定义 ####

w3c规范对BFC的解释：
　　浮动元素和绝对定位元素，不是块级盒子的块容器 （如 inline-blocks, table-cells, 和 table-captions），以及设置了overflow属性（除了visible）的块级盒子 ，都会为他们的内容创建新的BFC（块级格式上下文）。
　　在BFC中，盒子从顶端开始垂直地 一个接一个地排列，两个盒子之间的垂直的间隙是由他们的margin 值所决定的。在一个BFC中，两个相邻的块级盒子的垂直外边距会产生折叠。
　　在BFC中，每一个盒子的左外边缘（margin-left）会触碰到容器的左边缘(border-left)（对于从右到左的格式来说，则触碰到右边缘）。

#### BFC 布局规则 ####

- 内部的Box会在垂直方向，一个接一个地放置。
- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
- 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
- BFC的区域不会与float box重叠。
- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
- 计算BFC的高度时，浮动元素也参与计算

#### 如何触发 BFC ####

float 元素
position（absolute，fixed）
display (table-cell，table-caption，inline-block)
overflow 除了visible 以外的值（hidden，auto，scroll ）
fieldset元素
早期IE的hasLayout会触发一个新的block formatting context

#### BFC应用、作用和原理 ####

[【深入BFC】 关于CSS中float布局，清除浮动，和margin合并的原理解析，解开你心中的那些困惑！](http://www.cnblogs.com/v10258/p/3530290.html)

[BFC 神奇背后的原理](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)







