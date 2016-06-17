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







