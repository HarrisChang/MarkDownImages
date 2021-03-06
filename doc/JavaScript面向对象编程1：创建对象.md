### JavaScript面向对象编程(一)：创建对象



#### 一、创建对象
##### 1.1 我们可以通过new的方式创建一个对象
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
以上两种是最简单的创建方式，但是也有缺点：1.如果多生成几个实例，写起来就很麻烦；2.实例与原型之间，没有任何办法可以看出有什么联系。
##### 1.2 工厂模式应运而生
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
##### 1.3 构造函数模式
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
##### 1.4 Prototype模式
JS规定，每一个构造函数都有一个prototype属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。这个对象，就是我们所说的原型。
当我们在创建对象时，可以根据自己的需求，选择性地将一些属性和方法通过prototype属性，挂在到原型对象上。而每一个new出来的实例，都有一个__proto__属性，该属性指向构造函数的原型对象，通过这个属性，让实例对象也能够访问原型对象上的属性和方法。因此，当所有的实例都能够通过__proto__访问原型对象时，原型对象的属性和方法就成了共有方法和属性。
> 当我们访问实例中的属性或方法时，会优先访问实例对象自身的属性和方法。

#### 二、Prototype模式的验证方法
##### 2.1 isPrototypeOf()
这个方法用来判断，某个prototype对象和某个实例之间的关系。
```
CreatePerson.prototype.isPrototypeOf(person1);  //true
```
##### 2.2 hasOwnProperty()
每个实例对象都有一个hasOwnProperty()方法，用来判断某一个属性到底是本地属性，还是继承自prototype对象的属性。
```
person1.hasOwnProperty("name");  //true
```

##### 2.3 in运算符
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
