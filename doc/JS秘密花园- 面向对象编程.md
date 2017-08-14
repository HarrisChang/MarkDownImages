### JS秘密花园: 面向对象编程
参考文档：
[简书](http://www.jianshu.com/p/15ac7393bc1f)
[阮一峰](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html)
[oakland的GitHub](https://github.com/oakland/Native-JS-Practice/blob/master/11-learnOOPfromCircles.js/3-%E5%BD%BB%E5%BA%95%E7%90%86%E8%A7%A3JS%E4%B8%AD%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%EF%BC%88%E4%B8%89%E3%80%81JS%E4%B8%AD%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0OOP%EF%BC%89.md)
[Reid的博客](http://www.cnblogs.com/zhangjianhu/p/4548693.html)

#### 一、编程思想的理解
面向过程可以说是从细节处思考问题，面向对象是一种以事物为中心的编程思想。
> **面向过程(Process Oriented Programming)**是一种以过程为中心的编程思想，就是分析出解决问题所需要的步骤，然后用函数把这些步骤一步步实现，使用的时候一个一个依次调用即可。它是最为实际的一种思考方式，就算是面向对象的方法也是含有面向过程思想的，可以说面向过程是一种基础的方法，它考虑的是实际的实现，一般的面向过程是从上往下步步求精，所以面向过程最重要的是模块化的思想方法。
> **面向对象(Object Oriented Programming)**主要是把事物给对象化，对象包括属性和行为，当程序规模不是很大时，面向过程的方法还会提现出一种优势，因为程序的流程很清楚，按着模块与函数的方法可以很好的组织。面向对象也可以说是从宏观方面思考问题。`特点：抽象，封装，继承`

#### 二、对象的定义
如果要总结一下，学习JS过程中最难的是什么，我会毫不犹豫地选择面向对象。之所以难，是因为JS的Object模型很独特，不容易理解。
在C++里，对象就是类或结构体的实例，对象是由其模板实例化得到的。但JS里没有类，它是怎么定义对象的呢？
ECMAScript-262中，对对象的定义是`无序属性的集合，其属性可以包含基本值，对象或者函数`。JS里的对象是用花括号括起来的一堆键值对，键称为对象的属性名，理论是String类型的，但实际上加不加引号都可以。值就是属性名的属性值，既可以是五大基本数据类型，也可以是另外的对象，这样对象里又可以有对象。
#### 创建对象
##### 2.1 我们可以通过new的方式创建一个对象
```
var obj = new Object();
```
##### 也可以通过字面量的形式创建对象
```
var obj = {};
```
当我们要给创建的对象添加方法时，可以这样表示
```
var person = {};
person.name = "Harris";
person.age = 18;
person.getName = function(){
    return this.name;
}
//也可以这样
var person = {
    name: "Harris",
    age: 18,
    getName: function {
        return this.name;
    }
}
```
##### 2.11 访问对象的属性和方法
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

以上两种是最简单的创建方式，但是也有缺点：1.如果多生成几个实例，写起来就很麻烦；2.实例与原型之间，没有任何办法可以看出有什么联系。

##### 2.2 工厂模式应运而生
顾名思义，工厂模式就是提供一个模子，然后通过这个模子复制出我们需要的对象，需要多少就可以复制多少，一劳永逸。
```
var createPerson = function(name, age){
    //声明一个中间对象，该对象就是工厂模式的模子
    var temObj = new Object();
    //依次添加我们需要的属性和方法
    temObj.name = name;
    temObj.age = age;
    temObj.getName = function(){
        return this.name;
    }
    return temObj;
};
//创建两个实例
var person1 = createPerson("Harris",18);
var person2 = createPerson("Tom",20);

person1.getName == person2.getName  //false
```
这样做的问题依然是：person1和person2之间没有内在的联系，不能反映出它们是同一个原型对象的实例。它只是简化了对象创建的方法，并没有实现继承。
##### 2.3 构造函数模式
为了解决从原型对象生成实例的问题，JS提供了一个构造函数(Constructor)模式。
所谓构造函数，其实就是一个普通函数，但是内部使用了this变量，对构造函数使用new运算符，就能生成实例，并且this变量会绑定在实例对象上。
先来一个例子来感受一个new的神奇之处：
```
function demo(){
    console.log(this);
};
demo(); //window
new demo(); //demo
``` 
如此，我们就可以将上面的工厂模式改为
```
function CreatePerson(name,age){
    this.name = name;
    this.age = age;
    this.getName = function(){
        return this.name;
    }
};
var person1 = new CreatePerson("Harris",18);
var person2 = new CreatePerson("Tom",20);

person1 instanceof CreatePerson;  //true
person2 instanceof CreatePerson;  //true
```
此时，person1和person2会自动含有一个constructor属性，指向他们的构造函数。
```
person1.constructor == CreatePerson;  //true
person2.constructor == CreatePerson;  //true
```
JS还提供了一个instanceof运算符，验证原型对象与实例对象之间的关系。
```
p1 instanceof CreatePerson == true;  //true
p2 instanceof CreatePerson == true;  //true
```
> new关键字让构造函数具有了与普通函数不同的许多特点，而new的过程中，执行了如下过程：
> - 1.声明一个中间对象；
> - 2.将该中间对象的原型指向构造函数的原型；
> - 3.将构造函数的this指向该中间对象；
> - 4.返回该中间对象，即返回实例对象。

构造函数虽然好用，但说到底只是一个对象的复制过程，跟工厂模式有相同之处。例如，如果我们声明了100个对象，那么就有100个getName方法生成。每一个getName方法实现的功能都是一模一样的，但由于分属于不同实例，就不得不一直不停为getName分配空间。`存在浪费内存的问题`，我们就不得不求助于原型。
```
person1.getName == person2.getName;  //false
```
##### 2.4 Prototype模式
JS规定，每一个构造函数都有一个prototype属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。这个对象，就是我们所说的原型。
当我们在创建对象时，可以根据自己的需求，选择性地将一些属性和方法通过prototype属性，挂在到原型对象上。而每一个new出来的实例，都有一个__proto__属性，该属性指向构造函数的原型对象，通过这个属性，让实例对象也能够访问原型对象上的属性和方法。因此，当所有的实例都能够通过__proto__访问原型对象时，原型对象的属性和方法就成了共有方法和属性。
> 当我们访问实例中的属性或方法时，会优先访问实例对象自身的属性和方法。

##### 2.5 Prototype模式的验证方法
##### 2.51 isPrototypeOf()
这个方法用来判断，某个prototype对象和某个实例之间的关系。
```
CreatePerson.prototype.isPrototypeOf(person1);  //true
```
##### 2.52 hasOwnProperty()
每个实例对象都有一个hasOwnProperty()方法，用来判断某一个属性到底是本地属性，还是继承自prototype对象的属性。
```
person1.hasOwnProperty("name");  //true
```

##### 2.53 in运算符
in运算符可以用来判断，某个实例是否含有某个属性，不管是不是本地属性。
```
"name" in person1;  //true
```
in运算符还可以用来遍历某个对象的所有属性。
```
for(var val in person1){
    console.log(person1[val]);
}
```
#### 三、继承
前面了解到了如何从原型生成实例，现在介绍一下对象之间“继承”的几种方法。
比如，现在有一个动物对象的构造函数和一个猫对象的构造函数，怎样才能使猫继承动物呢？
```
function Animal(){
    this.species = "动物";
}；
function Cat(name,age){
    this.name = name;
    this.age = age;
};
```
##### 3.1 构造函数绑定
最简单的就是使用call或apply方法，将父对象的构造函数绑定在子对象上，即在子对象上加一行：
```
function Cat(name, age){
    Animal.apply(this,arguments);
    this.name = name;
    this.age = age;
};
var cat1 = new Cat("喵"，18)；
cat1.species;  //动物
```
##### 3.2 prototype模式
使用prototype属性的方法也很常见：
如果猫的prototype对象，指向一个Animal的实例，那么所有猫的实例，就能继承Animal了。
```
Cat.prototype = new Animal();
Cat.prototype.constructor = Cat;
var cat1 = new Cat("大黄"，16);
cat1.species;  //动物
```
第一步：将Cat的prototype对象指向Animal实例，它相当于完全删除了prototype对象原先的值，然后赋予一个新值。
第二步：任何一个prototype对象都有一个constructor属性，指向他的构造函数。如果没有第一步，Cat.prototype.constructor是指向Cat的，加了第一步，则指向了Animal。
```
Cat.prototype.constructor == Animal;  //true
```
更重要的是，每个实例也有constructor属性，默认调用prototype对象的constructor属性。
```
cat1.constructor == Cat.prototype.constructor;  //true
```
因此，在运行第一步之后，cat1的constructor也指向了Animal。
这显然会导致集成链的絮乱(cat1明明是Cat生成的)，所以需要手动矫正，将Cat.prototype的constructor属性值改为Cat，这就是第二步的意义。
> 这是很重要的一点：编程时务必遵循，如替换了prototype对象。
> ```
> o.prototype = {}''
> ```
> 那么，下一步必然是为新的prototype对象加上constructor属性，并将这个属性指回原来的构造函数。
> ```
> o.prototype.constructor = 0;
> ```