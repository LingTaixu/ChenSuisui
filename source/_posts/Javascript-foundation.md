---
layout: old
title: JavaScript 基础知识
date: 2018-06-05 15:35:29
tags: 旧
---

## 数组遍历方法

### 1. for循环 
使用临时变量，将长度缓存起来，避免重复获取数组长度，当数组较大时优化效果才会比较明显。

### 2. forEach循环
循环遍历数组中的每一项，没有返回值，对原数组没有影响，不支持IE

### 3. map循环
有返回值，可以return出来，map的回调函数中支持return返回值；return的是啥，相当于把数组中的这一项变为啥（并不影响原来的数组，只是相当于把原数组克隆一份，把克隆的这一份的数组中的对应项改变了）

### 4. for of 
遍历数组的每一项
**和for in区别**
1. for Of 无法遍历对象 
2. 遍历输出结果不同
3. for in 会遍历自定义属性，for of不会


## 原始类型

> JavaScript 中原始类型有六种，原始类型既只保存原始值，是没有函数可以调用的

### **1. 六种原始类型**

- String
- Number
- Boolean
- Null
- Undefined
- symbol

> **注意**
> 为什么说原始类型没有函数可以调用，但`'1'.toString( )`却又可以在浏览器中正确执行？

因为`'1'.toString( )`中的`'1'`在这个时候会被封装成其对应的字符串对象，以上代码相当于*new String('1').toString()*，因为new String('1')创建的是一个对象，而这个对象里是存在toString()方法的。



### **2. null到底是什么类型**
现在很多书籍都把`null`解释成空对象，是一个对象类型。然而在早期`JavaScript`的版本中使用的是32位系统，考虑性能原因，使用低位存储变量的类型信息，`000`开头代表对象，而null就代表全零，所以将他错误的判断成了`Object`，虽然后期内部代码改变，但`null`类型为`Object`的判断却保留下来，至于`null`具体是什么类型，属于仁者见仁智者见智，你说他是对象类型也可以，说他是一个bug也行。

## 对象类型
> 在JavaScript中，除了基础类型，其他都是对象类型，对象类型存储的是地址，而原始类型存储的是值。

```javascript
var a = [];
var b = a;
a.push(1);
console.log(b); // 输出[1]
```

在以上代码中，创建了一个对象类型`a`(数组)，再把`a`的地址赋值给了变量`b`，最后改变`a`的值，打印`b`时，`b`的值也同步发生了改变，因为它们在内存中使用的是同一个地址，改变其中任何一变量的值，都会影响到其他变量。

### **1. 对象当做函数参数**
```javascript
function testPerson(person) {
  person.age = 52;
  person = {
    name: '李四',
    age: 18
  }
  return person;
}
var p1 = {
  name: '张三',
  age: 23
}
var p2 = testPerson(p1);
console.log(p1.age);  // 输出52
console.log(p2.age);  // 输出18
```
**代码分析
1.  `testPerson`函数中，`person`传递的是一个对象`p1`的指针副本
2.  在函数内部，改变`person`的属性，会同步反映到对象`p1`上，`p1`对象中`age`属性发生改变，既值就是52
3.  `testPerson`函数又返回一个新对象，这个对象此时和参数`person`没有任何关系，因为他分配了一个新的内存地址
4.  以上分析可看下图

![img](http://119.23.216.240:2020/image/Object.png)

## typeof 和 instanceof

### **1. typeof**
> typeof会准确判断除`null`以外的所有基础数据类型，对于对象类型，除了`function(函数)`会被判断成`function`，其他一律返回`Object`
```javascript

typeof 1      //number
typeof '1'    //String
typeof true       // boolean
typeof undefined  // undefined
typeof Symbol()   // symbol

typeof []         // object
typeof {}         // object
typeof console.log// function

```
### **2. instanceof**

>instanceof通过原型链可以判断出对象的类型，但并不是百分百准确

```javascript
function Person(name) {
  this.name = name;
}
var p1 = new Person();
console.log(p1 instanceof Person) // true

var str = new String('abc');
console.log(str instanceof String)// true
```

## 类型转换
`javascript`中，类型转换只有三种:
1.  转换成数字
2.  转换成布尔值
3.  转换成字符串

### **1. 经典面试题**
```javascript
console.log( []==![] ) //true
```
**代码分析**
1. 左侧是一个对象(数组)
2. 右侧是一个布尔值，对象`[  ]`转换成布尔值`true`，因为除了`null`所有对象都转换成布尔值，所以`![  ]`结果为`false`
3. 此时相当于`对象==布尔值`，依据类型转换规则，转换成数字类型进行比较
4. 对象(空数组)转换成`0`，布尔值`false`转换成`0`
5. 即`0==0`，返回`true`

类型转换规则，如下图:

![2](http://119.23.216.240:2020/image/2.png)

## == 和 ===
如何你对上面的例子还一知半解，那么我们来详细介绍一下`==`和`===`的规则以及区别。

### **1. ===严格相等**
> `===`叫做严格相等，是指：左右两边不仅值要相等，类型也要相等，例如`'1'===1`的结果是`false`，因为一边是`string`，另一边是`number`。
```javascript
console.log('1'===1); // 输出false
```

### **2. ==不严格相等**
> `==`不像`===`那样严格，对于一般情况，只要值相等，就返回`true`，但`==`还涉及一些类型转换，它的转换规则如下：
> - 两边的类型是否相同，相同的话就比较值的大小，例如`1==2`，返回`false`
> - 类型不相同会进行类型转换
> - 判断的是否是`null`和`undefined`，是的话就返回`true`
> - 判断的类型是否是`String`和`Number`，是的话，把`String`类型转换成`Number`，再进行比较
> - 判断其中一方是否是`Boolean`，是的话就把`Boolean`转换成`Number`，再进行比较
> - 如果其中一方为`Object`，且另一方为`String`、`Number`或者`Symbol`，会将`Object`转换成原始类型后，再进行比较

### **3. 转Boolean** 
除了`undefined`、`null`、`false`、`0`、`-0`、`NaN`和空字符串转换成`false`以外，其他所有值都转换成`true`，包括所有`对象`。

### **4. 对象转原始类型**
> 太陡了 可以选择性观看
对象转原始类型，会调用内置的[ToPrimitive]函数，对于该函数而言，其逻辑如下：
1. 是否已经是原始类型，是则直接返回
2. 调用valueOf()，如果转换为原始类型，则返回
3. 调用toString()，如果转换为原始类型，则返回
4. 也可以重写Symbol.toPrimitive()方法，优先级别最高
5. 如果都没有返回原始类型，会报错
```javascript
var obj = {
  value: 0,
  valueOf() {
    return 1;
  },
  toString() {
    return '2'
  },
  [Symbol.toPrimitive]() {
    return 3
  }
}
console.log(obj + 1); // 输出4
```
### **5. 对象转原始类型应用**
```javascript
// 问：如何使if(a==1&&a==2&&a==3) {console.log('true')};正确打印'true'
var a = {
  value: 0,
  valueOf() {
    this.value++;
    return this.value;
  }
}
if(a==1 && a==2 && a==3) {
  console.log('true'); // 输出true
}
```
**代码分析:**
1. 重写对象`a`的`valueOf()`方法，使`value`属性每次调用时自增
2. 当判断`a==1`时，第一次调用`valueOf()`方法，此时`value`等于`1`，判断`1==1`，继续向下走
3. 判断`a==2`时，第二次调用`valueOf()`方法，此时`value`等于`2`，判断`2==2`，继续向下走
4. 判断`a==3`时，第三次调用`valueOf()`方法，此时`value`等于`3`，判断`3==3`，if判断结束
5. `if`条件判断为`true && true && true`，执行`console.log('true')`，打印`true`


## new构造调用的过程
无论是通过字面量还是通过`new`进行构造函数调用创建出来的`对象`，其实都一样。调用`new`的过程如下：
1.  创建一个新对象
2.  原型绑定
3.  绑定this到这个新对象
4.  返回新对象


## 闭包
> 当一个函数能够记住并访问它所在的词法作用域的时候，就产生了闭包，即使函数式在词法作用域之外执行
> 
> **闭包的缺陷** ：由于闭包打破了函数主用于的束缚，导致里面的数据无法清除销毁，当数据过大的时候会导致`数据溢出`
> 
> 由于在js中，变量到的作用域属于函数作用域，在函数执行后作用域会被清除、内存也会随之被回收，但是由于闭包是建立在一个`函数内部的子函数`，由于其可访问上级作用域的原因，即使上级函数执行完，`作用域也不会随之销毁`，这时的子函数---也就是闭包，便拥有了访问上级作用域中的变量权限，即使上级函数执行完后，作用域内的值也`不会被销毁`。

### **1. 闭包的几种表现形式**
>1. 返回一个函数
>2. 作为函数参数传递
>3. 回调函数
>4. 非典型闭包 IIEF (立即执行函数)

**返回一个函数:** 这种形式的闭包在`JavaScript`的代码编写中，是非常常见的一种方式。
```javascript
var a  = 1;
function foo(){
  var a = 2;
  // 这就是闭包
  return function(){
    console.log(a);
  }
}
var bar = foo();
// 输出2，而不是1
bar();
```
**作为函数参数传递:** 无论通过何种手段将内部函数传递到它所在词法作用域之外，它都会持有对原始作用域的引用，无论在何处执行这个函数，都会产生闭包。
```javascript
var a = 1;
function foo(){
  var a = 2;
  function baz(){
    console.log(a);
  }
  bar(baz);
}
function bar(fn){
  // 这就是闭包
  fn();
}
// 输出2，而不是1
foo();
```
**回调函数:** 在定时器、事件监听、Ajax请求、跨窗口通信、Web Workers或者任何异步中，只要使用了回调函数，实际上就是在使用闭包。
```javascript
// 定时器
setTimeout(function timeHandler(){
  console.log('timer');
}，100)

// 事件监听
$('#container').click(function(){
  console.log('DOM Listener');
})
```
**IIEF** IIFE(立即执行函数表达式)并不是一个典型的闭包，但它确实创建了一个闭包。
```javascript
var a = 2;
(function IIFE(){
  // 输出2
  console.log(a);
})();
```

### **2. 经典循环和闭包面试题**
> 以下代码运行结果是什么，如何改进？
```javascript
for(var i=1;i<=5;i++){
  setTimeout(function timer(){
    console.log(i)
  }, i*1000)
}
```
**代码分析：**
1. `for`循环一共创建了5个定时器，并且定时器是在循环之后执行
2. `for`循环结束后，用`var i` 定义的变量`i`等于6
3. 以此执行5个定时器，都打印变量`i`，所以结果打印五次6

**第一种改进方法：** 利用`IIFE(立即执行函数表达式)`当每次`for`循环时，把此时的`i`变量传递到定时器中
```javascript
for(var i=1;i<=5;i++){
  (function(j){
    setTimeout(function timer(){
      console.log(j)
    }, i*1000)
  })(i)
}
```
**第二种方法：** `setTimeout`函数的第三个参数，可以作为定时器执行时的变量进行使用
```javascript
for(var i=1;i<=5;i++){
  setTimeout(function timer(j){
    console.log(j)
  }, i*1000, i)
}
```

**第三种方法(推荐)：** 在循环中使用`let i`代替`var i`
```javascript
for(let i=1;i<=5;i++){
  setTimeout(function timer(){
    console.log(i)
  }, i*1000)
}
```

### 3. 闭包本质上解决了什么
**闭包解决了什么：在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。**

## 原型、原型链
 **原型:**
> 1. 函数都要`prototype`(显示原型)属性，而`prototype`会自动初始化一个空对象，这个对象就是原型对象
> 2. 原型对象中会有一个`constructor`属性,这个属性将指向了函数本身
> 3. 实例化对象都有一个`_proto_(隐式原型)`属性，`_proto_`属性指向原型对象 
```javascript
function Person() {
}
var p = new Person();
//方法才有prototype,普通对象无prototype
console.log(Person.prototype); // Object{} 
console.log(p.prototype); // undifined

//任何对象都是有构造函数的,Person这种方法的构造函数是Function。
//备注:constructor很容易被改变，一般不用它，此处只是打印下列对象的构造函数是什么。
console.log(p.constructor); //function Person(){}  
console.log(Person.constructor); //function Function(){} 
console.log({}.constructor); // function Object(){}
console.log(Object.constructor); // function Function() {}
console.log([].constructor);  //function Array(){}
```
那什么是构造函数呢？用`function`声明的都是函数，而如果直接调用的话，那么`Person()`就是一个普通函数，只有用函数`new`产生对象时，这个函数才是`new`出来对象的构造函数。

**采用new关键创建对象的过程**
### 1. 声明方法
**当我们声明一个`function`关键字的方法时，会为这个方法添加一个`prototype`属性，指向默认的原型对象，并且此`prototype`的`constructor`属性也指向方法对象。** 此两个属性会在创建对象时被对象的属性引用。
![prototype](http://119.23.216.240:2020/image/3.png)

### 2. 我们如果用Hello创建一个对象h，看这个对象有什么属性。
![prototype](http://119.23.216.240:2020/image/4.png)

备注：`getPrototypeOf`是获取`_proto_`

我们惊喜的发现，`new`出来的对象，它的`constructor`指向了方法对象，它的`_proto_`和`prototype`相等。  即new一个对象，它的`_proto_`属性指向了方法的`prototype`属性，并且`constructor`指向了`prototype`的`constructor`属性。

### 3. new创建一个对象的过程
```javascript
function Hello(name) {
  this.name = name;
}
var h = new Hello("...");
//伪代码:
function newObj(name){
  var obj = {};
  obj.__proto__ = Hello.prototype; 
  obj.constructor = Hello.prototype.constructor;
  var result = Hello.call(obj, name);
  return typeof result==='object' && result!=null ? result : obj;  //当无返回对象或默认时返回obj。
}
var hh = newObj("...");
console.log(hh);
console.log(h);
//虽然hh!=h,但是可以看到这个hh就和h的结构一样了
```
过程：
1. 先创建一个新对象
2. 设置空对象的_proto_指向方法的原型
3. 设置空对象的constructor为构造函数的名称
4. 用新对象做this指向方法调用函数
5. 将初始化完毕的新对象返回

**`具体内容之后更新`**

**原型链：**
> 1. 从实例对象往上找构造这个实例的相关对象，然后这个关联的对象再往上找，找到创造它的上一级的原型对象，以此类推，一直到`object.prototype`原型对象终止,原型链结束.
> 
> 2. 原型链中的原型对象中的内容,是会被不同的实例,所共有的

![prototype](http://119.23.216.240:2020/image/5.png)

第一个 `__proto__`  指向的是 `Hello.prototype`第二个 `__proto__` 指向的是 `Object.prototype`

从上面说明的过程中，我们发现只要是对象就是由构造函数来创建的，并且内部二个属性是从构造函数的`prototype`衍生的一个指向，而构造函数的`prototype`也是一个对象，那么它应该肯定也有一个构造函数，首先它是一个`Object {} `对象，那么它的构造函数肯定是`Object`，所以就会有一个指针`_proto_`指向`Object.prototype`。最后`Object.prototype`因为没有`_proto_`，指向`null`，这样就构成了一个原型链。

**原型链查找分析**
1. 当访问一个对象的成员的时候，会先在自身找有没有，如果找到直接使用。
2. 如果没有找到，则去**原型链指向的对象的构造函数的`prototype`中找**，找到直接使用，没找到就返回`undifined`或报错。

```javascript
function Person(name){
  this.name = name;
}
var p = new Person();
//p ---> Person.prototype --->Object.prototype---->null
```

**原型指针**

我们知道了原型的概念，接下来我们就看下面这张图来具体分析一下原型的指针：**(图片有点问题 推荐使用白色主题查看)**
![call](http://119.23.216.240:2020/image/6.png)

中间最上面蓝色模块标注的构造函数`Foo`, 里面有两个属性`_proto_`和`prototype`, 这两个很容易使人混淆。
1. `prototype`属性，它是**函数所独有**的，它是从**一个函数指向一个对象**。它的含义是**函数的原型对象**，也就是这个函数（其实所有函数都可以作为构造函数）所创建的实例的原型对象; 这个属性是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法（我们把这个对象叫做原型对象）;
2. `__proto__` 是原型链查询中实际用到的，它总是指向 `prototype`，换句话说就是指向**构造函数的原型对象**，它是**对象独有的**。注意，为什么Foo构造函数也有这个属性呢，因为在js的宇宙里万物皆对象，包括函数
根据以上的概括我们能知道Foo构造函数`_proto_`指向的是他的构造函数的原型对象，它的构造函数是`Function`, 也就是说`Foo`的`_proto_`指向`Function.prototype`, 

    我们再看到左边绿色的`a`和`b`函数的`_proto_`指像的是`Foo.prototype`,因为他们是通过 `new Foo`实例化出来的，它们的构造函数就是`Foo()`，即`a._proto_ = Foo.prototype`；

    接着我们来看看最右边紫色的模块`Function.prororype`, 它的`_proto_`指针指向的是`Object.prototype`,`Object._proto_`又为`null`.

    于是我们就可以得出：在原型链中的指向是，`函数` → `构造函数`  →` Function.prototype` → `Object.protype` → `null` ;

3. 　我们看到最中间灰色模块有一个`constructor`属性，这个又是做什么用的呢？

    每个函数都有一个原型对象，该原型对象有一个`constructor`属性，指向创建对象的函数本身。

    此外，我们还可以使用`constructor`属性，所有的实例对象都可以访问`constructor`属性，`constructor`属性是创建实例对象的函数的引用。我们可以使用`constructor`属性验证实例的原型类型（与操作符`instanceof`非常类似）。

    由于`constructor`属性仅仅是原始构造函数的引用，因此我们可以使用该属性创建新的对象，如：

    ![pro](http://119.23.216.240:2020/image/7.png)

    通过第一个对象实例化对象的`constuctor`方法创建第2个实例化对象，说明创建的新对象`fn2` 是`Fn`的实例，由于`fn1`和`fn2`不是同一个对象可以得出它们是两个截然不同的实例；

### 4. 总结
  
1. `__proto__` 是原型链查询中实际用到的，它总是指向 `prototype`；
2. `prototype` 是函数所独有的，在定义构造函数时自动创建，它总是被 `__proto__` 所指。
3. 所有对象都有`__proto__`属性，函数这个特殊对象除了具有`__proto__`属性，还有特有的原型属性`prototype`。
4. `prototype`对象默认有两个属性，`constructor`属性和`__proto__`属性。
5. `prototype`属性可以给函数和对象添加可共享（继承）的方法、属性，而`__proto__`是查找某函数或对象的原型链方式。
6. `constructor`，这个属性包含了一个指针，指回原构造函数。


### 5. 应用场景
1. 实现对象之间数据共享
2. (继承)在ES6之前, 没有class的情况下, 模拟面向对象, 构造函数中放私有属性, 原型上放共有属性

## 深浅拷贝
由于`JavaScript`中对象是引用类型，保存的是地址，深、浅拷贝的区别是，当拷贝结束后，在一定程度上改变原对象中的某一个引用类型属性的值，新拷贝出来的对象依然受影响的话，就是浅拷贝，反之就是深拷贝。

### 1. 浅拷贝的几种实现方法

> 1. 利用`Object.assign()`方法
> 2. 利用`...`扩展运算符

**第一种方法：** `Object.assign()`会拷贝原始对象中的所有属性到一个新对象上，**如果属性为对象**，则拷贝的是对象的地址，改变对象中的属性值，新拷贝出来的对象依然会受影响。

```javascript
var obj = {
  name: '张三',
  age: 23,
  isStudent: false,
  job: {
    name: 'FE',
    money: 12
  }
}
var newObj = Object.assign({}, obj);
obj.job.money = 21;
console.log(newObj.name);     // 输出张三
console.log(newObj.age);      // 输出23
console.log(newObj.job.money);// 输出21，受影响
```

**第二种方法：** `...`扩展运算符是`ES6`新增的内容

```javascript
var obj = {
  name: '张三',
  age: 23,
  isStudent: false
}
var newObj = {...obj};
console.log(newObj.name);     // 输出张三
console.log(newObj.age);      // 输出23
```

### 2. 深拷贝几种实现方式

>1. 配合使用`JSON.parse()`和`JSON.stringify()`两个函数(局限性比较大)
>2. 实现自己的简易深拷贝方法
>3. `lodash`第三方库实现深拷贝

**第一种方法：** 利用`JSON`的序列化和反序列化方法，可以实现简易对象深拷贝，但此种方法有较大的限制：
1. 会忽略属性值为`undefined`的属性
2. 会忽略属性为`Symbol`的属性
3. 不会序列化函数
4. 不能解决循环引用的问题，直接报错
  
```javascript
var obj = {
  name: '张三',
  age: 23,
  address: undefined,
  sayHello: function() {
    console.log('Hello');
  },
  isStudent: false,
  job: {
    name: 'FE',
    money: 12
  }
}

var newObj = JSON.parse(JSON.stringify(obj));
obj.job.money = 21;

console.log(newObj.name);      // 输出张三
console.log(newObj.age);       // 输出23
console.log(newObj.job.money); // 输出12

console.log(newObj.address);   // 报错
console.log(newObj.sayHello());// 报错
```

**第二种：** 实现自己简易的深拷贝函数

```javascript
function deepClone(obj) {
  function isObject(o) {
    return (typeof o === 'object' || typeof o === 'function') && o !== null;
  }
  if(!isObject(obj)) {
    throw new Error('非对象');
  }
  var isArray = Array.isArray(obj);
  var newObj = isArray ? [...obj] : {...obj};
  Reflect.ownKeys(newObj).forEach(key => {
    newObj[key] = isObject(newObj[key]) ? deepClone(newObj[key]) : newObj[key];
  })
  return newObj;
}
var obj = {
  name: 'AAA',
  age: 23,
  job: {
    name: 'FE',
    money: 12000
  }
}
var cloneObj = deepClone(obj);
obj.job.money = 13000;
console.log(obj.job.money);     // 输出13000
console.log(cloneObj.job.money);// 输出12000
```

**第三种方法：** 使用[lodash](https://lodash.com/docs#cloneDeep)第三方函数库实现(需要先引入lodash.js)
```javascript
var obj = {
  name: '张三',
  age: 23,
  isStudent: false,
  job: {
    name: 'FE',
    money: 12
  }
}
var newObj = _.cloneDeep(obj);
obj.job.money = 21;
console.log(newObj.name);     // 输出张三
console.log(newObj.age);      // 输出23
console.log(newObj.job.money);// 输出12，不受影响
```

## 继承

在`JavaScriptES6`之前，实现继承需要依赖原型、原型链和构造函数等等技术手段组合使用，在ES6之后，可以使用`Class`类继承(并没有真正的类，只是一个语法糖，实质依然是函数)
>**继承的几种方式**
> 1. 原型链实现继承
> 2. 借用构造函数实现继承
> 3. 组合继承
> 4. 寄生组合继承
> 5. 类继承

### 1. 原型链实现继承
> 通过重写子类的原型，并将它指向父类的手段实现。这种方式实现的继承，创建出来的实例既是子类的实例，又是父类的实例。它有如下几种缺陷：
> 1. 不能向父类构造函数传参
> 2. 父类上的引用类型属性会被所有实例共享，其中一个实例改变时，会影响其他实例
```javascript 
function Animal() {
  this.colors = ['red','blue'];
}
function Dog(name) {
  this.name = name;
}
Dog.prototype = new Animal();

var dog1 = new Dog('旺财');
var dog2 = new Dog('钢镚');
//其中一个实例改变时，会影响其他实例
dog2.colors.push('yellow');
console.log(dog1.colors); // ["red", "blue", "yellow"]
console.log(dog2.colors); // ["red", "blue", "yellow"]

console.log(dog1 instanceof Dog);   // true
console.log(dog1 instanceof Animal);// true
```
### 2. 借用构造函数实现继承

> 借用构造函数实现继承，通过在子类中使用`call()`方法，实现借用父类构造函数并向父类构造函数传参的目的。但这种方法，无法继承父类原型对象上的属性和方法。

```javascript
function Animal(name) {
  this.name = name;
  this.colors = ['red','blue'];
}
Animal.prototype.eat = function() {
  console.log(this.name + ' is eating!');
}
function Dog(name) {
  Animal.call(this,name);
}

var dog1 = new Dog('旺财');
var dog2 = new Dog('钢镚');
dog2.colors.push('yellow');

console.log(dog1.colors); // ["red", "blue"]
console.log(dog2.colors); // ["red", "blue", "yellow"]

console.log(dog1 instanceof Dog);   // true
console.log(dog2 instanceof Animal);// false

console.log(dog1.eat()); // 报错
```

### 3. 组合继承

> 组合继承是组合了原型链继承和借用构造函数继承这两种方法，它保留了两种继承方式的优点，但它并不是百分百完美的：
> - 父类构造函数被调用多次。

```javascript
function Animal(name) {
  this.name = name;
  this.colors = ['red','blue'];
}
Animal.prototype.eat = function() {
  console.log(this.name + ' is eatting');
}
function Dog(name) {
  Animal.call(this,name);
}
Dog.prototype = new Animal(); // 第一次调用
var dog1 = new Dog('dog1');   // 第二次调用
var dog2 = new Dog('dog2');   // 第三次调用
dog1.colors.push('yellow');
console.log(dog1.name);  // 输出dog1
console.log(dog2.colors);// 输出['red','blue']
console.log(dog2.eat()); // 输出dog2 is eatting
```

### 4. 寄生组合继承

> 寄生组合继承是在组合继承的基础上，采用`Object.create()`方法来改造实现
```javascript
function Animal(name) {
  this.name = name;
  this.colors = ['red','blue'];
}
Animal.prototype.eat = function() {
  console.log(this.name + ' is eatting');
}
function Dog(name) {
  Animal.call(this,name);
}
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
var dog1 = new Dog('dog1');
var dog2 = new Dog('dog2');
dog1.colors.push('yellow');
console.log(dog1.name);  // 输出dog1
console.log(dog2.colors);// 输出['red','blue']
console.log(dog2.eat()); // 输出dog2 is eatting
```

### 5. Class实现继承

>运用ES6 class新特性来实现继承

```javascript
class Animal {
  constructor(name) {
    this.name = name;
    this.colors = ['red','blue'];
  }
  eat() {
    console.log(this.name + ' is eatting');
  }
}
class Dog extends Animal {
  constructor(name) {
    super(name);
  }
}
var dog1 = new Dog('dog1');
var dog2 = new Dog('dog2');
dog1.colors.push('yellow');
console.log(dog1.name);  // 输出dog1
console.log(dog2.colors);// 输出['red','blue']
console.log(dog2.eat()); // 输出dog2 is eatting
```

## JavaScript异步

### 1. 同步和异步的区别
- **同步：** 就是指一个进程在执行某个请求的时候，若该请求需要一段时间才能返回信息，那么这个进程将会一直等待下去，直到返回信息之后才继续执行后面的代码
- **异步：** 是指进程不需要一直等下去，而是继续执行下面的操作，不管其他进程的状态。当有消息返回时系统会通知进程进行处理，这样可以提高执行的效率。

### 2. 并发和并行

并行和并发是两个概念，容易混淆是因为并行和并发在中文意思上相近，其实在英文中，这是完全不相同的东西，并行(parallelism)、并发(concurrency)

> **概念理解**
> 1. 并行(parallelism)：是微观概念，假设CPU有两个核心，则我们就可以同时完成任务A和任务B，同时完成多个任务的情况就可以称之为并行。
> 2. 并发(concurrency)：是宏观概念，现在有任务A和任务B，在一段时间内，通过任务之间的切换完成这两个任务，这种情况称之为并发。

### 3. 回调函数

回调函数广泛存在于我们所编写的`JavaScript`代码中，它表现在事件绑定，Ajax请求或者其他的情况下，一个回调函数可表现成如下形式

```javascript
ajax(url, () => {
  console.log('这里是回调函数');
})
```

**回调地狱：** 回调函数很好的解决了某些异步情况，但过度滥用回调函数会造成回调地狱，即回调函数过长，嵌套过深。过长或者嵌套过深的回调函数，会让回调函数存在强耦合关系，一旦有一个函数有所改动，那么可能会牵一发而动全身。一个回调地狱可能如下所示：
```javascript
ajax(firstUrl, () => {
  console.log('这里是首次回调函数');
  ajax(secondUrl, () => {
    console.log('这里是第二次回调函数');
    ajax(threeUrl, () => {
      console.log('这里是第三次回调函数');
      // todo更多
    })
  })
})
```

### 4. Generator
在ES6之前，一个函数一旦执行将不会被中断，一直到函数执行完毕，在ES6之后，由于`Generator`的存在，函数可以暂停自身，待到合适的机会再次执行。用`Generator`可以解决回调地狱。
```javascript
function *fetch() {
  yield ajax(url, () => {console.log('这里是首次回调函数');});
  yield ajax(url, () => {console.log('这里是第二次回调函数');});
  yield ajax(url, () => {console.log('这里是第三次回调函数');});
}
var it = fetch();
var result1 = it.next();
var result2 = it.next();
var result3 = it.next();
```

### 5. Promise  (大概了解会有详解)

`Promise`翻译过来就是承诺的意思，`Promise`一共有三种状态：`pending(等待中)`、`resolve(完成)`和`reject(拒绝)`，这个承诺意味着在将来一定会有一个表决，并且只能表决一次，表决的状态一定是`resolve(完成)`或者`reject(拒绝)`，一个`Promise`可能会是如下的形式：

```javascript
// 普通的Promise
function foo() {
  return new Promise((resolve,reject) => {
    // 第一次表决有效，其后无论是resolve()还是reject()都无效
    resolve(true); 
    resolve(false);
  })
}

// Promise解决回调地狱
ajax(url).then(res => {
  console.log('这里是首次回调函数');
}).then(res => {
  console.log('这里是第二次回调函数');
}).then(res => {
  console.log('这里是第三次回调函数');
})
```

### 6. Promise.all()
 `Promise.all()`方法是把一个或者几个`Promise`组合在一个数组里，只有当数组中的所有`Promise`全部表决完成，才返回。

```javascript
var p1 = Promise.resolve(1);
var p2 = new Promise((resolve) => {
  setTimeout(() => {
    resolve(2);
  }, 100);
})
var p3 = 3;
Promise.all([p1,p2,p3]).then((res) => {
  console.log(res); // 输出[1,2,3]
})
```

### 7. Promise.race()
`Promise.race()`方法把一个或者几个`Promise`组合在一个数组里，只要数组中有一个表决了，就返回。

```javascript 
var p1 = Promise.resolve(1);
var p2 = new Promise((resolve) => {
  setTimeout(() => {
    resolve(2);
  }, 100);
})
var p3 = 3;
Promise.race([p2,p1,p3]).then((res) => {
  console.log(res); // 输出1
})
```

### 8. async/await
如果一个方法前面加上了`async`，那么这个方法就会返回一个`Promise`，`async`就是将函数用`Promise.resolve()`包裹了下，并且`await`只能配合`async`使用，不能单独出现。一个`async/await`可能会是如下的形式：
```javascript
// 普通的async/await
async function foo() {
  let number = await 3; // await自动用promise.resolve()包装
  console.log(number);
}
foo();

// async/await解决回调地狱
async function fetch() {
  var result1 = await ajax(url1);
  var result2 = await ajax(url2);
  var result3 = await ajax(url3);
}
fetch();
```

### 9. setInterval、setTimeout和requestAnimationFrame

**setTimeout:** `setTimeout`延时执行某一段代码，但`setTimeout`由于`EventLoop`的存在，并不百分百是准时的，一个`setTimeout`可能会表示如下的形式：
```javascript
// 延时1s之后，打印hello,world
setTimeout(() => {
  console.log('hello,world');
}, 1000)
```

**setInterval：** `setInterval`在指定的时间内，重复执行一段代码，与setTimeout类似，它也不是准时的，并且有时候及其不推荐使用`setInterval`定时器，因为它与某些耗时的代码配合使用的话，会存在执行积累的问题，它会等耗时操作结束后，一起一个或者多个执行定时器，存在性能问题。一个`setInterval`可能会表示如下的形式：
```javascript
setInterval(() => {
  console.log('hello,world');
}, 1000)
```

**requestAnimationFrame:** 翻译过来就是请求动画帧，它是html5专门用来设计请求动画的API，它与`setTimeout`相比有如下优势：
1. 根据不同屏幕的刷新频率，自动调整执行回调函数的时机。
2. 当窗口处于未激活状态时，`requestAnimationFrame`或停止执行，而`setTimeout`不会
3. 自带函数节流功能
```javascript
var progress = 0;
var timer = null;
function render() {
  progress += 1;
  if (progress <= 100) {
    console.log(progress);
    timer = window.requestAnimationFrame(render);
  } else {
    cancelAnimationFrame(timer);
  }
}
//第一帧渲染
window.requestAnimationFrame(render);
```





## V8引擎垃圾回收机制

### 1. 原理
> JS 的垃圾回收机制的基本原理是：
- 找出那些不再继续使用的变量，然后释放其占用的内存，垃圾收集器会按照固定的时间间隔周期性地执行这一操作。
- V8 的垃圾回收策略主要基于分代式垃圾回收机制，在 V8 中，将内存分为新生代和老生代，新生代的对象为存活时间较短的对象，老生代的对象为存活事件较长或常驻内存的对象。
  
![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/**cd3b1a0ccf7644168728f92e5cee6e21~tplv**-k3u1fbpfcp-zoom-1.image)

- V8 堆的整体大小等于新生代所用内存空间加上老生代的内存空间，而只能在启动时指定，意味着运行时无法自动扩充，如果超过了极限值，就会引起进程出错。
### 2. **`Scavenge` 算法**
- 在分代的基础上，新生代的对象主要通过 Scavenge 算法进行垃圾回收，在 Scavenge 具体实现中，主要采用了一种复制的方式的方法—— Cheney 算法。
  
- Cheney 算法将堆内存一分为二，一个处于使用状态的空间叫 From 空间，一个处于闲置状态的空间称为 To 空间。分配对象时，先是在 From 空间中进行分配。
  
- 当开始进行垃圾回收时，会检查 From 空间中的存活对象，将其复制到 To 空间中，而非存活对象占用的空间将会被释放。完成复制后，From 空间和 To 空间的角色发生对换。
  
  
![img2](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f01b71ee6ba644e4bb72a0b8215d7dc2~tplv-k3u1fbpfcp-zoom-1.image)

- 当一个对象经过多次复制后依然存活，他将会被认为是生命周期较长的对象，随后会被一定到老生代中，采用新的算法进行管理。
  
- 还有一种情况是，如果复制一个对象到To空间时，To空间占用超过了25%，则这个对象会被直接晋升到老生代空间中
### 3. 标记-清除和标记-整理算法
- 对于老生代中的对象，主要采用标记-清除和标记-整理算法。标记-清除和前文提到的标记一样，于`Scavenge`算法相比，标记清除不会将内存空间划为两半，标记清除在标记阶段会标记活着的对象，而在内存回收阶段，它会清除没有被标记的对象。
  
- 而标记整理是为了解决标记清除后留下的内存碎片问题。
### 4.内存泄漏
>内存泄漏（Memory Leak）是指程序中己动态分配的堆内存由于某种原因程序未释放或无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统崩溃等严重后果。

#### 内存泄漏的常见场景：

- 缓存:存在内存中数据一只没有被清掉
- 作用域未释放（闭包）
- 无效的 DOM 引用
- 没必要的全局变量
- 定时器未清除(React中的合成事件，还有原生事件的绑定区别)
- 事件监听为清空
- 内存泄漏优化