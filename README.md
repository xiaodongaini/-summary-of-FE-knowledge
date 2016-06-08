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



