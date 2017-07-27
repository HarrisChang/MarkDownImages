### JS秘密花园: 闭包与作用域链
提及闭包，多数开发者肯定会有所困扰或者说曾被它困扰过。笔者初学时就头疼过，感觉像雾里看花一样，怎么也不能将其看个透彻。既然有了切肤之痛，就想将其彻底搞清楚，完完全全地掌握。
先抛出闭包的定义：**当函数可以记住并访问所在的作用域(全局作用域除外)时，就产生了闭包，即使函数是在当前作用域之外执行。**
> 简单来说：假设函数A在函数B的内部定义了，并且当函数A在执行时，访问了B里面的变量，那么B就是一个闭包。

闭包有两个重要特性：1. 闭包与作用域链息息相关。2. 闭包是在函数执行过程中被确认。接下来，我们先了解一下作用域链。
#### 一、作用域与作用域链
**作用域**

- 在JS中，我们可以将作用域定义为一套规则，这套规则用来管理引擎如何在当前作用域以及嵌套的子作用域中根据标识符名称(这里的标识符，指的是变量名或函数名)进行变量查找。
- JS中只有全局作用域和函数作用域(eval在平时开发中几乎用不到)。

> JS代码的整个执行阶段，分为两个阶段，代码编译阶段与代码执行阶段。编译阶段由编译器完成，将代码翻译成可执行代码，这个阶段作用域规则会确定。执行阶段由引擎完成，主要任务是执行可执行代码，执行上下文在该阶段创建。

![过程](https://github.com/HarrisChang/MarkDownImages/raw/master/4-1.png)
<center>过程</center>

**作用域链**

![执行上下文生命周期](https://github.com/HarrisChang/MarkDownImages/raw/master/4-2.png)
<center>执行上下文生命周期</center>

作用域是一套规则，作用域链是该规则的具体实现。作用域链是由当前环境与上层环境的一系列变量对象组成，它保证了当前执行环境对符合访问权限的变量和函数的有序访问。
函数在调用激活时，会开始创建对应的执行上下文，在执行上下文生成的过程中，变量对象、作用域链以及this指向分别会被确定。
结合以下事例来理解作用域链。
```
var a = 20;
function test(){
    var b = a + 20;
    function innerTest(){
        var c = 10;
        return b + c;
    }
    return innerTest();
}
test();
```
在上例中，全局，函数test，函数innerTest的执行上下文先后创建，我们设定他们的变量对象分别为VO(global)，VO(test)，VO(innerTest)。而innerTest的作用域链，则同时包含了这三个变量对象，所以innerTest的执行上下文可如下表示。
```
innerTestEC = {
    VO: {...}, //变量对象
    scopeChain: [VO(innerTest), VO(test), VO(global)],
    this: {}
}
```
是的，可以直接用一个数组来表示作用域链，数组第一项为作用域链的最前端，最后一项为作用域链的最末端，所有的最末端都是全局变量对象。
很多人以为当前作用域与上层作用域为包含关系，其实不是。以最前端为起点，最末端为终点的单向通道来形容更为贴切。

![作用域链图示](https://github.com/HarrisChang/MarkDownImages/raw/master/4-3.png)
<center>作用域链图示</center>

作用域链是由一系列变量对象组成，我们可以在这个单向通道中，查询变量对象中的标识符，这样就可以访问到上一层作用域中的变量了。

#### 二、闭包
> 闭包意味着当前作用域总是能够访问到外部作用域中的变量，因为函数是JS中唯一拥有自身作用域的结构，因此闭包的创建依赖于函数。

我们知道，函数的执行上下文，在执行完毕之后，生命周期结束，那么该函数的执行上下文就会失去引用。其占用的内存空间很快就会被垃圾回收器释放，`可是闭包的存在，会阻止这一过程。`
```
var fn = null;
function foo(){
    var a = 2;
    function innerFoo(){
        console.log(a);
    }
    fn = innerFoo();  //将innerFoo的引用，赋值给全局变量中的fn
}
function bar(){
    fn();  //此处的保留的innerFoo的引用
}
foo();
bar();
```
在上例中，foo()执行完毕之后，依常理其执行环境生命周期结束，所占内存被垃圾回收器释放。但通过fn = innerFoo()，函数innerFoo的引用被留了下来，复制给了全局变量fn。该行为导致foo的变量对象也被留了下来。于是，函数fn在bar内部执行时，依然可以访问这个被保留下来的变量对象，所以此刻仍能访问到a的值。这样，我们就可以称foo为闭包。

![闭包fn的作用域链](https://github.com/HarrisChang/MarkDownImages/raw/master/4-4.png)
<center>闭包fn的作用域链</center>

> 所以，通过闭包，我们可以在其他的执行上下文中，访问到函数的内部变量。从应用层面，这是闭包最重要的特性。

需注意的是，虽然例子中的闭包被保存在了全局变量中，但是闭包作用域链并不会发生任何改变。在闭包中，能够访问到的变量，仍是作用域链上能够查询到的变量。

#### 三、闭包应用场景
##### 3.1经典for循环应用
```
//demo01.js
for(var i = 0; i < 5; i++){
    console.log(i);
}
```
不用怀疑，输出结果是：0，1，2，3，4
```
//demo02.js
for(var i = 0; i < 5; i++){
    setTimeout(function(){
        console.log(i);
    },i*1000)
}
```
输出结果是：开始输出一个5，然后每隔一秒输出一个5，共输出五个。
```
//demo03.js
for(var i = 0; i < 5; i++){
    (function(e){
        setTimeout(function(){
            console.log(e);
        },e*1000)
    })(i);
}
```
输出结果是：开始输出一个0，然后每隔一秒一次输出1，2，3，4。
```
//demo03.js
for(var i = 0; i < 5; i++){
    (function(e){
        setTimeout(function(){
            console.log(e);
        },e*1000)
    })(i);
}
//如果删掉i会怎样？
```
输出结果是：直接输出5个undefined。
```
//demo04.js
for(var i = 0; i < 5; i++){
    setTimeout(function(i){
        console.log(i);
    }(i),i*1000);
}
```
输出结果是：立即输出0，1，2，3，4

##### 3.2for循环应用拓展
```
//demo01.js
for (var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(new Date, i);
    }, 1000);
}
console.log(new Date, i);
```
循环执行过程中，几乎同时设置了5个定时器，一般情况下，这5个定时器都会在1秒后触发，而循环后的输出是立刻执行的，因此，输出结果是：
<pre>
Wed Jul 26 2017 10:42:<font color="#fe6f69">50</font> GMT+0800 (中国标准时间) 5
Wed Jul 26 2017 10:42:51 GMT+0800 (中国标准时间) 5
Wed Jul 26 2017 10:42:51 GMT+0800 (中国标准时间) 5
Wed Jul 26 2017 10:42:51 GMT+0800 (中国标准时间) 5
Wed Jul 26 2017 10:42:51 GMT+0800 (中国标准时间) 5
Wed Jul 26 2017 10:42:51 GMT+0800 (中国标准时间) 5
</pre>
**追问1：**如果希望输出5 0 1 2 3 4该怎么做？
```
//方法1：
for (var i = 0; i < 5; i++) {
    (function(i){
		setTimeout(function() {
            console.log(new Date, i);
        }, 1000)
	})(i);
}
console.log(new Date, i);
```
```
//方法2：
for (var i = 0; i < 5; i++) {
    (function(i){
		setTimeout(function(j) {
            console.log(new Date, j);
        }, 1000, i)
	})(i);
}
console.log(new Date, i);
```
**追问2：**如果希望输出0 1 2 3 4 5该怎么做？

```
//方法1：
for (var i = 0; i < 5; i++) {
    setTimeout((function() {
        console.log(new Date, i);
    })(), 1000);
}
console.log(new Date, i);
```
#### 3.3闭包面试题
```
function fun(n,o) {
  console.log(o)
  return {
    fun:function(m){
      return fun(m,n);
    }
  };
}
var a = fun(0);  a.fun(1);  a.fun(2);  a.fun(3);
var b = fun(0).fun(1).fun(2).fun(3);
var c = fun(0).fun(1);  c.fun(2);  c.fun(3);
```
输出结果为：
<pre>
undefined，0，0，0
undefined，0，1，2
undefined，0，1，1
</pre>

