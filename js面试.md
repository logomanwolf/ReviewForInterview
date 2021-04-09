## curry(柯里化)

为什么要用柯里化？

> 柯里化是一种将使用多个参数的一个函数转换成一系列使用一个参数的函数的技术
>
> curry 的这种用途可以理解为：参数复用。本质上是降低通用性，提高适用性。

柯里化的实现

```javascript
//要点：
//1.判断原先函数的参数个数和当前函数的参数个数是否相等，如果相等则进行，否则继续
let curry= fn => (judge= (...args) =>  fn.length > args.length ? (...args2)=>judge (...args,..args2):fn(...args))
```

## 垃圾回收机制

V8的内存结构

- new_space: 大多数的对象开始都被分配在这里

  ![Scavenge](D:\前端面试准备\Scavenge.jpg)![Scavenge2](D:\前端面试准备\Scavenge2.jpg)

  

- old_space: new_space中的对象存活一段时间就会到这里（经历过Scavenge算法或者To区域内存比超过25%）

  Mark-Sweep(标记清除) 和 Mark-Compact(标记整理) 算法

  https://juejin.cn/post/6844904016325902344

- large_object_space: 存放体积较大的对象，垃圾回收不会移动这个区域

### 弱引用

使用 `WeakMap` 和 `WeakSet` 可以减少内存泄露

键名所引用的对象是弱引用，只要所引用的对象没有其他的引用了，垃圾回收机制就会释放该对象所占的内存。

### 私有变量和私有方法的实现

##### 私有变量

```javascript
//1. 闭包法,在外面定义_private，是伪成员变量
const Example = (function(){
    //我个人觉得这里用let更好，并且测试过没有问题
    var _private= ''
    class Example{
        constructor(){
            _private="private";
        }
        getName(){
            return _private;
        }
    }
    return Example;
})()
//2. 利用Symbol实现
const Example=(funciton(){
    const _private =  Symbol('private')
    class Example{
        constructor(){
          this[_private] = ''    
        }
        getName(){
            return this[_private]
        }
    }
    return Example
})()
//3. 利用WeakMap实现
const _private = new weakMap();
class Example {
   constructor(){
       _private.set(this,'private')
   }
   getName(){
       return _private.get(this)
   }
}
//最新提案
class Point {
  #x;
  #y;

  constructor(x, y) {
    this.#x = x;
    this.#y = y;
  }

  equals(point) {
    return this.#x === point.#x && this.#y === point.#y;
  }
}
```

## new的实现

```javascript
//明确new的作用
//1.根据构造函数生成一个实例
//2.根据构造函数的结果返回不同的值
function objFactory (){
    let obj = object.create(null);
    let constructor = [].shift.apply(arguments);
    obj.__proto__=constructor.prototype;
    let result = constructor.apply(obj,arguments);
    return typeof result === 'object' ? result : obj;
}
```



## js事件机制

1. js是一门单线程语言，同步任务放到主执行栈，异步任务都会放到一个指定的队列（Event Queue）里面。

2. 当主执行栈的任务执行完时，会从Event Queue里读取对应的任务。

3. 读取顺序如下：Event Loop中是否有微任务（micro），如果有，先执行所有的微任务；如果没有，执行最先进入Event Loop的一个宏任务（macro）。

4. 当这个宏任务完成时，继续步骤3。

   

![img](D:\前端面试准备\js事件循环机制.jpg)

> 例1：

```javascript
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});

console.log('script end');
//输出顺序是：script start, script end, promise1, promise2, setTimeout
```

> 例2：

```javascript
console.log('script start');

setTimeout(function() {
  console.log('timeout1');
}, 10);

new Promise(resolve => {
    console.log('promise1');
    resolve();
    setTimeout(() => console.log('timeout2'), 10);
}).then(function() {
    console.log('then1')
})

console.log('script end');
//输出顺序时：script start, promise1, script end, then1, timeout1, timeout2
```

## js原型链和继承

![image-20210113134136054](D:\前端面试准备\原型和原型链.png)

![img](D:\前端面试准备\原型继承)

> 可以将共有的属性放到Person的原型对象中。当访问实例的属性时，先访问对象的属性，再访问原型对象的属性。

#### 两种比较好的继承方式

##### 组合继承

```javascript
function Parent(name){
    this.name=name;
} 
Parent.prototype.getName(){
    return this.name;
}
funciton Child(name,age){
    Parent.call(this,name);
    this.age=age;
}
Child.prototype=new Parent();
Child.prototype.consructor=Parent;
```

##### 优点：

可以实现对于父函数的继承，将共有的函数放到prototype上，使得不需要每创造一个子函数都要重新定义一个方法，同样子属性有了更多的独立性，改编子属性的属性变量不会影响父属性

##### 缺点：

两次调用Parent函数。Child.prototype上会被绑定多余的属性和方法。

##### 组合寄生方式：

```javascript
function Parent(name){
    this.name=name;
}
function Children(name,value){
    Parent.call(this,name);
    this.value = value;
}{
// 这里的Object.create做相当于
// function f(){}; f.prototype=o; return new F; 

Children.prototype = Object.create(Parent.prototype);
Children.protptype.constructor = Parent;
```

##### 优点：

组合寄生的方式避免了Parent构造函数的二次调用，而且Children.prototype上没有了多余的属性和方法



## js数据类型

###### 基本数据类型

`number`, ` boolean`, `symbol`, `undefined`, `null`, `string`, `bigint`

###### 引用数据类型

`Object`（包含`Function`, `Array`,  `Date`）

其中，基本数据类型存储的地点在栈（stack），引用数据类型存储的地点在栈（存储指向堆地址的指针）和堆（实际内容）

###### 判断js数据类型

https://juejin.cn/post/6844903613584654344

1. typeof

2. instanceof

   1. instanceof 是用来判断 A 是否为 B 的实例，表达式为：A instanceof B，如果 A 是 B 的实例，则返回 true,否则返回 false。 在这里需要特别注意的是：**instanceof 检测的是原型**。

```javascript
//instanceof的实现
function instanceof(leftV,rightV){
    let leftValue = leftV.__proto__;
    let rightValue = rightV.prototype;
    while(true){
        if(leftValue===null)
            return false;
        else if(leftValue===rightValue)
            return true;
        else
            leftValue=leftValue.__proto__;
    }
}
```



1. constructor

2. toString

   1. toString() 是 Object 的原型方法，调用该方法，默认返回当前对象的 [[Class]] 。这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。

      对于 Object 对象，直接调用 toString() 就能返回 [object Object] 。而对于其他对象，则需要通过 call / apply 来调用才能返回正确的类型信息。

### 模块化规范

###### 背景：

javaScript的函数式编程有关，没有标准化模块化系统、标准库少

#### CommonJS

CommonJS作用于服务器(node.js)

###### 特点：

1. 一个文件就是一个模块
2. 通过require引入一个模块，export导出模块属性方法

#### AMD

AMD作用于浏览器

###### 特点：

1. 所有的模块都进行异步加载，模块加载不影响后面语句进行（解决了浏览器失去响应的问题）
2. 所有依赖某些模块的语句都放在回调函数中（解决了依赖问题）
3. 提供全局define 函数（方法）来定义模块，require引入模块，exports导出模块

###### 解决的问题：

  1、多个js模块相互引用问题，被依赖模块需早与依赖模块加载。

  2、js加载的会阻塞浏览器页面渲染，加载文件越多，页面失去响应时间越长 

两者都只能在运行时确定模块的依赖关系 

#### CMD

**AMD**推崇依赖前置，在定义模块的时候就要声名其依赖的模块

**CMD**推崇就近依赖，只有在用到某个模块的时候才去require

不同点

#### ES6

1. 实现静态加载，也就是说在编译时就加载了，因此可以进行编译的静态分析
2. 每一个文件就是一个模块，通过import引入文件和export输出内部变量，用export default来输出变量，可以自定义引入的变量名

#### 加载模块的方法

###### 浏览器端

<script src="./a.js" type="application/javascript"></script>
如果脚本体积太大，下载的时间就会变长，浏览器会有“卡死”的不好体验，因此允许异步加载：

<script src="./a.js" type="application/javascript" defer></script>
<script src="./a.js" type="application/javascript" async></script>
**defer**要等到页面正常渲染结束后才会加载

**async**下载完成后就会停止渲染，执行完这个脚本后再开始下载外部脚本

当a.js是ES6模块时，可以直接用下面的语句，默认和defer的功能相同

<script type="module" src="./a.js"></script>
**CommonJS** 和 **ES6**的区别

1. 一个静态一个动态
2. common输出的是一个值的复制，ES6输出的是一个值的 引用

##### 具体解释

###### commonJs

```javascript
//lib.js
var counter=0;
function incCounter(){
    count++;
}
export.module={
    counter,
    incCounter
}
// main.js
const module=require("lib.js")
console.log(module.counter)
module.incCounter();
console.log(module.counter)
// 输出为0,0

//-------------------------分割线---------------------------
//lib.js
var count=0;
funciton incCounter(){
    count++;
}
exprort.module={
    incCounter,
    get counter(){
        return counter
    }
}

//main.js同上
//输出结果为：0，1
/*上面的代码说明，lib.js模块加载以后，它的内部变化就影响不到输出的mod.counter。这是因为mod.counter是一个原始类型的值，会被缓存。除非写成一个函数，否则得不到内部变动后的值。*/

```

###### es6

```javascript
// lib . js
export let counter = 3 ;
export function incCounter() {
	counter++;
}
// main. js
import { counter, incCounter } from ’./1ib ’;
console.log(counter); // 3
incCounter() ;
console.log (counter); // 4
```

上面的代码说明， ES6 模块输入的变量counter是活的，完全反应其所在模块lib . js内部的变化。

#### es6 中 get和set的用法

1. 用来设置私有属性

   ```javascript
   let age=6
   class person{
   	name=0;
   	constructor(name){
   		this.name=name;
   	}
   	get age(){
   		return age;
   	}
   }
   //
   let person=new Person('a');
   console.log(person.name);
   person.name=2;
   console.log(person.name)
   //0 0
   //想要改变，需要增加一个set方法
   //---------修改后--------
   let age=6
   class person{
   	name=0;
   	constructor(name){
   		this.name=name;
   	}
   	get age(){
   		return age;
   	}
       set age(value){
           age=value;
       }
   }
   ```

### event.target和currentTarget

```javascript
//currentTarget的值为body(注册监听事件处理程序的元素)  target的值为button(事件的元素)
document.body.onclick = function(event) {
	console.log(event.currentTarget === document.body); // true
	console.log(this === document.body); // true
	console.log(event.target === document.getElementById("myBtn")); // true
};
```

### js基本类型

**基本数据类型**

- Number(ES5): typeof()返回"number"。用于任何类型的数字：整数或者浮点数。
- String(ES5): typeof()返回"string"。用于字符串。一个字符串可以包含一个或多个字符，所以没有单独的单字符类型。
- Boolean(ES5): typeof()返回"boolean"。用于 true 和 false。
- Null(ES5): typeof()返回"object"。用于未知的值 —— 只有一个 null 值的独立类型。
- Undefined(ES5): typeof()返回"undefined"。用于未定义的值 —— 只有一个 undefined 值的独立类型。
- **Symbol**(ES6): typeof()返回"symbol"。用于唯一的标识符。

**引用数据类型**

- Object(ES5): typeof()返回"object"。用于更复杂的数据结构。
- *Function(ES5): typeof()返回"function"，属于object。

**基本数据类型和引用数据类型访问**

- 基本类型值：存在栈中，按值访问，操作的是他们实际保存的值；
- 引用类型值：栈中保存地址，堆中保存值，按引用访问，当查询时，我们需要先从栈中读取内存地址，然后再顺藤摸瓜地找到保存在堆内存中的值；



## ==，===，is()的区别

这么理解： 当进行双等号比较时候： 先检查两个操作数数据类型，如果相同， 则进行===比较， 如果不同， 则愿意为你进行一次类型转换， 转换成相同类型后再进行比较， 而===比较时， 如果类型不同，直接就是false.

1. 先说`===`，这个比较简单，只需要利用下面的规则来判断两个值是否恒等就行了：

   1. 如果类型不同，就*不相等*

   2. 如果两个都是数值，并且是同一个值，那么相等； 

      1. 值得注意的是，如果两个值中至少一个是**NaN**，那么*不相等*（判断一个值是否是*NaN*，可以用`isNaN()`或`Object.is()`来判断）。

   3. 如果两个都是**字符串**，每个位置的字符都一样，那么*相等*；否则*不相等*。

   4. 如果两个值都是同样的**Boolean**值，那么*相等*。

   5. 如果两个值都引用同一个**对象或函数**，那么*相等*，即两个对象的**物理地址**也必须保持一致；否则*不相等*。

   6. 如果两个值都是**null**，或者都是**undefined**，那么*相等*。

2. 再说`Object.is()`，其行为与`===`基本一致，不过有两处不同：

   1. **+0**不等于**-0**。
   2. **NaN**等于自身。

## 关于Symbol

```js
var a1= Symbol('foo');
var a2=Symbol('foo');
a1===a2 //false
```

## call, apply, bind函数的区别

###### 相同点

**call()、apply()、bind() 都是用来重定义 this 这个对象的！**

###### 不同点

![call&apply&bind区别](D:\前端面试准备\call&apply&bind区别.png)

call 、bind 、 apply 这三个函数的第一个参数都是 this 的指向对象，第二个参数差别就来了：

call 的参数是直接放进去的，第二第三第 n 个参数全都用逗号分隔，直接放到后面 **obj.myFun.call(db,'成都', ... ,'string' )**。  

apply 的所有参数都必须放在一个数组里面传进去 **obj.myFun.apply(db,['成都', ..., 'string' ])**。

bind 除了返回是函数以外，它的参数和 call 一样。

```js
obj.myFun.call(db,'成都','上海')；　　　　 // 德玛 年龄 99  来自 成都去往上海
obj.myFun.apply(db,['成都','上海']);      // 德玛 年龄 99  来自 成都去往上海  
obj.myFun.bind(db,'成都','上海')();       // 德玛 年龄 99  来自 成都去往上海 
obj.myFun.bind(db,['成都','上海'])();　　 // 德玛 年龄 99  来自 成都, 上海去往 undefined
```

#### call, apply, bind的实现

###### bind函数的实现主要注意以下几点：

1. bind函数返回的是一个函数，且该函数的参数也可以作为原函数的参数，换而言之就是要进行参数合并。
2. bind函数需要判断是当将原函数的this对象绑定到参数对象中还是返回的函数（当有new关键字在时）。
3. 开始的时候一定要判断是否调用的是函数，反例如下：

```javascript
Funciton.prototype.bind2.call({a:f},...)
//这个时候调用bind2的函数就不是一个对象
```

#### bind的实现

```javascript
Function.prototype.bind2 = function (context) {
    if (typeof this !== "function") {
      throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
    }
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var fNOP = function () {};
    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(this instanceof fNOP ? this : context, args.concat(bindArgs));
    }
    fNOP.prototype = self.prototype;
    fBound.prototype = new fNOP();
    return fBound;
}
```

#### call的实现

```javascript
//需要注意的点是：1.context的判断 2.ctx要添加一个属性保存this,并在调用后删除 3.判断有无args
Function.prototype.call = function (context,...args){
    let ctx = context || window;
    ctx.fn = this;
    let result= args?  ctx.fn(...args):ctx.fn()
    delete ctx.fn
    return result
}
```

## 关于this的指向问题

this的指向永远都是在调用的时候才决定的

```javascript
function a(){
    var user = "追梦子";
    console.log(this.user); //undefined
    console.log(this); //Window
}
a();
```

```javascript
var o = {
    user:"追梦子",
    fn:function(){
        console.log(this.user);  //追梦子
    }
}
o.fn();
```

```javascript
var o = {
    user:"追梦子",
    fn:function(){
        console.log(this.user); //追梦子
    }
}
window.o.fn();
```

```javascript
var o = {
    a:10,
    b:{
        // a:12,
        fn:function(){
            console.log(this.a); //undefined
        }
    }
}
o.b.fn();
//尽管对象b中没有属性a，这个this指向的也是对象b，因为this只会指向它的上一级对象，不管这个对象中有没有this
//要的东西。
```

```javascript
var o = {
    a:10,
    b:{
        a:12,
        fn:function(){
            console.log(this.a); //undefined
            console.log(this); //window
        }
    }
}
var j = o.b.fn;
j();
//　this永远指向的是最后调用它的对象，也就是看它执行的时候是谁调用的，虽然函数fn是被对象b所引用，但是
//在将fn赋值给变量j的时候并没有执行所以最终指向的是window
```

```jsx
  var a = {
    b: function() {
      var func = function() {
        console.log(this.c);
      }
      func();
    },
    c: 'hello'
  }
  a.b(); // undefined 这里的this指向的是全局作用域
  console.log(a.c); // hello
//同上
```

**构造函数版this：**

```javascript
function Fn(){
    this.user = "追梦子";
}
var a = new Fn();
console.log(a.user); //追梦子
```

　　这里之所以对象a可以点出函数Fn里面的user是因为new关键字可以改变this的指向，将这个this指向对象a，为什么我说a是对象，因为用了new关键字就是创建一个对象实例，我们这里用变量a创建了一个Fn的实例（相当于复制了一份Fn到对象a里面），此时仅仅只是创建，并没有执行，而调用这个函数Fn的是对象a，那么this指向的自然是对象a，那么为什么对象a中会有user，因为你已经复制了一份Fn函数到对象a中，用了new关键字就等同于复制了一份。

**当this碰到return时**

```javascript
function fn()  
{  
    this.user = '追梦子';  
    return {};  
}
var a = new fn;  
console.log(a.user); //undefined
```

```javascript
function fn()  
{  
    this.user = '追梦子';  
    return function(){};
}
var a = new fn;  
console.log(a.user); //undefined
```

```javascript
function fn()  
{  
    this.user = '追梦子';  
    return 1;
}
var a = new fn;  
console.log(a.user); //追梦子
function fn()  
{  
    this.user = '追梦子';  
    return undefined;
}
var a = new fn;  
console.log(a); //fn {user: "追梦子"}
//如果返回值是一个对象，那么this指向的就是那个返回的对象，如果返回值不是一个对象那么this还是指向函数的实例。
//这里this还是指向那个函数的实例，因为null比较特殊。
function fn()  
{  
    this.user = '追梦子';  
    return null;
}
var a = new fn;  
console.log(a.user); //追梦子
```

> 补充：
>
> 1.在严格版中的默认的this不再是window，而是undefined。
>
> 2.new操作符会改变函数this的指向问题。首先new关键字会创建一个空的对象，然后会自动调用一个函数方法（具体是什么后续可以再查），将this指向这个空对象，这样的话函数内部的this就会被这个空的对象替代。	

## 箭头函数的指向问题

```javascript
// 使用箭头函数
var circle = {
	radius: 10,
	outerDiameter() {
var innerDiameter = () => {
	console.log(2 * this.radius);
};
	innerDiameter();
}
};
circle.outerDiameter(); // 打印20
```

内层函数innerDiameter它本身并没有this值，其使用的this来自于作用域链，来自于更高层的作用域。innerDiameterr的外层函数outerDiameter是普通函数，它是有this值的，它的this值就是circle对象。因此，innerDiameter函数中所使用的this来自outerDiameter函数，其值为circle对象。

## 关于Undefined

undefined 作为if(undefined){}的条件时是false；!undefined作为条件时相当于true；

## 关于null

对null执行typeof预算，结果返回字符串'object',也就是说，可以将null认为是一个特殊的对象值，含义是“非对象。”

if(obj) 是用来判断obj是否为null或undefined,因为if(null)和if(undefined)都是进入false的分支

if(typeof obj==='object') 判断是否是object或null，因为都进入true的分支

isNaN(null)  //返回false,因为isNaN()会首先尝试将这个参数转换为数值，然后才会对转换后的结果是否是NaN进行判断。因此，对于能被强制转换为有效的非NaN数值来说（ 值得一提的是，包含**空格的字符串、空字符串、空数组和布尔值**会被强制转换为数值0或1）

## sort函数

`sort(function)`

function函数里面：小于用return -1，等于用return 0，大鱼用return 1.

## 作用域链

最重要的两点：

**js中函数的作用域链在函数定义的时候就决定了**

### 变量提升

执行上下文的代码分成两那个阶段进行：1. 进入执行上下文 2. 代码执行

进入执行上下文时，首先会处理函数声明（**函数提升**），如果变量名称和已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性。

但是注意在执行阶段，赋值操作会改变变量值。

具体参考https://github.com/mqyqingfeng/Blog/issues/5

## JavaScript 变量提升

JavaScript 中，函数及变量的声明都将被提升到函数的最顶部。

JavaScript 中，变量可以在使用后声明，也就是变量可以先使用再声明。

var是全局作用域，有变量提升的作用。

## js中new有无括号的没有区别

```javascript
//只是执行顺序的时候要注意：
funciton A(){
	this.b='b';
}
let a =new A().b  //输出为'b'
let a =new A.b  //报运行错误
```

# 闭包（ES5）

https://segmentfault.com/a/1190000003985390

https://blog.csdn.net/weixin_45846805/article/details/106972978

当内部函数可以访问到外部函数的变量即叫做闭包。

自由变量指的是函数中使用的，但既不是函数参数也不是函数局部变量的变量。（这里的局部变量是相对于当前函数说的）

换句话说自由变量是那些跨作用域上下文的变量

常见的两个例子

```javascript
//例1：
var f = function(){
			var i = 0;
			return function(){
				i++;
				console.log(i)
			}
		}
		var s = f();
		s();//1
		s();//2
		s();//3

//例2：
	window.onload = function(){
			var lists = document.getElementsByTagName('li');
			for(var  i = 0 ; i< lists.length;i++){
				lists[i].onclick = function(){
					console.log(i)//点击时都会打印4
			}
		}
	}
```

## 临时死区

`let`与`const`声明的变量并不会被提升到作用域的顶部，如果在声明之前访问这些变量，即使是相对安全的`typeof`操作符也会触发引用错误。

```javascript
console.log(typeof value);  // 抛出语法错误
let value = "Y";
```

原因是当JavaScript引擎在扫描代码发现变量声明时，会根据不同的关键字采取不同的操作。

- 遇到`var`声明时，会将变量提升至作用域顶部
- 遇到`let`或`const`声明时，将声明放到临时死区中

只有执行过变量声明语句后，变量才会从临时死区中移出，可被正常访问，否则会触发运行时错误。

###  Immediately Invoked Function Expression和块级作用域

https://zhuanlan.zhihu.com/p/80691396

主要是为了解决闭包自由变量问题，有点类似于class的概念。