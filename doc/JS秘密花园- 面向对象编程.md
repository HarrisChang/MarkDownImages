### JS秘密花园: 面向对象编程
#### 一、编程思想的理解
面向过程可以说是从细节处思考问题，面向对象是一种以事物为中心的编程思想。
> **面向过程(Process Oriented Programming)**是一种以过程为中心的编程思想，就是分析出解决问题所需要的步骤，然后用函数把这些步骤一步步实现，使用的时候一个一个依次调用即可。它是最为实际的一种思考方式，就算是面向对象的方法也是含有面向过程思想的，可以说面向过程是一种基础的方法，它考虑的是实际的实现，一般的面向过程是从上往下步步求精，所以面向过程最重要的是模块化的思想方法。
> **面向对象(Object Oriented Programming)**主要是把事物给对象化，对象包括属性和行为，当程序规模不是很大时，面向过程的方法还会提现出一种优势，因为程序的流程很清楚，按着模块与函数的方法可以很好的组织。面向对象也可以说是从宏观方面思考问题。`特点：抽象，封装，继承`

#### 二、对象的定义
ECMAScript-262中，对象被定义为：`无序属性的集合，其属性可以包含基本值、对象或者函数。`简单来说，在JS中对象就是一些无序的key-value对组成，其中value可以是基本值、对象或函数。
```
//myObject就是一个对象
var myObject = {
    name: "Harris",
    age: 18,
    getHobby: function(){}
}
```
##### 2.1创建对象
```
//1.通过new的方式创建对象
var myObject = new Object();

//2.通过字面量的形式创建对象
var myObject = {};
```
当我们需要给创建的对象添加方法时，可以如下操作。
```
//demo01.js
var myObject = {};
myObject.name = "Harris";
myObject.getName = function(){
    return this.name;
}
//demo02.js
var myObject = {
    name: "Harris",
    getName: function(){
        return this.name;
    }
}
```
##### 2.2访问对象的属性和方法
假设有一个对象：
```
var myObject = {
    name: "Harris",
    getName: function(){
        return this.name;
    }
}
```
当访问name属性时，可以
```
myObject.name 或者 myObject['name'];
```
如果我们要访问的属性名是一个变量时，通常会采用第二种方法。可以使用forEach遍历对象的属性。

#### 2.3工厂模式
顾名思义，工厂模式就是提供一个模子，然后通过这个模子复制出我们需要的对象，需要多少就可以复制多少，一劳永逸。
```
function createObj(name, age){
    //声明一个中间对象，该对象就是工厂模式的模子
    var obj = new Object();
    //添加需要的属性或方法
    obj.name = name;
    obj.age = age;
    obj.getName = function(){
        return this.name;
    }
    return obj;
}
//创建实例
var objOne = createObj("Harris",18);
var objTwo = createObj("Rene",20);
```
显然，工厂模式解决了重复代码的问题，但仍有两点不足需要注意：

- 没有办法识别对象实例的类型。(我们需要使用构造函数的方式来解决此问题)

#### 2.4构造函数



















console.log(typeof([1,2,3]));  // object
console.log(typeof(null));  //object
console.log(typeof(undefined)); //undefined
console.log(typeof(Object)); //function
console.log(typeof(Array)); //function
undefined、number、string、boolean属于简单的值类型，不是对象。剩下的几种情况--函数、数组、对象、null、new Number(10)都是对象，他们是引用类型。
判断一个变量是不是对象很简单，值类型的判断用typeof，引用类型的判断用instanceof

var fn = function(){
   alert(3);
}
console.log(fn instanceof Object); // true
console.log(fn instanceof Function); // true
console.log(fn instanceof Object === fn instanceof Function); // true



![执行上下文生命周期](https://github.com/HarrisChang/MarkDownImages/raw/master/3-2.png)<center>执行上下文生命周期</center>
