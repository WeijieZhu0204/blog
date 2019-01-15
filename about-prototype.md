# 关于prototype和\_\_proto\_\_的区别

对于很多人都迷惑，JavaScript对象里面的prototype和\_\_proto\_\_有什么区别和联系？针对这个问题，我们由浅入深的探讨一下。



## 构造器（构造函数）

在 JavaScript 中，构造器其实就是一个普通的函数。当我们使用`typeof`获取构造器类型时，返回的类型是`function`而不是`object`。

```javascript
> typeof String
'function'
> typeof Object
'function'
> typeof Boolean
'function'
...
```

直接执行函数可以获取实例对象：`Object()`，也可以通过new操作符实例化对象：`new Object()`，我们把这些函数称为构造函数 ，原型链上的`constructor`属性都分别指向这些构造函数。

```javascript
> Object
ƒ Object() { [native code] } //echo Object construct function

> Object.prototype.constructor === Object
true
> Array.prototype.constructor === Array
true

> var obj = new Object() // or var obj = Object()
```





## 原型链

JavaScript实际上每个对象都有一个prototype的属性指向原型对象，原型对象也有自己的prototype指向下一个原型对象，就像一个原型链表一样，直到最后一个原型对象的prototype指向null。

当试图访问一个对象的属性时，不仅仅在该对象属性中查找，还会沿着原型链层层往下查找，直到原型链末为止。



### 任何对象的原型链都指向Object.prototype

ECMAScript 标准定义了 7 种数据类型，以下的原型链指向Object.prototype：

- Number
- String
- Boolean
- Object
- Symbol

> 注意：
>
> Null、Undefined是原始数据类型，但不是对象。
>
> Array、Date只是JavaScript的内置对象，并不是原始数据类型。



下面举一个String类型的例子：

```javascript
> 'hello'.__proto__
String {"", length: 0, constructor: ƒ, anchor: ƒ, big: ƒ, blink: ƒ, …}  // echo String.prototype

// 字符串实例的原型链与String对象的原型链相同
> 'hello'.__proto__ === String.prototype
true 

// String对象实际上是一个构造器(constructor)，String实例原型链上的构造函数相同
> 'hello'.__proto__.constructor === String
true
> 'hello'.__proto__.constructor === String.prototype.constructor
true
    

// String类继承于Object类，因此可以看到String对象的原型链指向Object的原型链
> String.prototype.__proto__ === Object.prototype
true

```



### \_\_proto\_\_ 与 prototype的区别和联系

下面从三点说明两者的关系：

1. 只有构造函数才有`prototype`属性。

```javascript
> Object.prototype
{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …} // Object原型对象

> Function.prototype
ƒ () { [native code] } // Function原型对象

> function Test(){}
> Test.prototype
{constructor: ƒ} // Test原型对象

> var obj = {}
> obj.prototype
undefined // obj对象实例没有prototype属性

> var test = new Test()
> test.prototype
undefined // test对象实例没有prototype属性
```



2. 所有对象都有 \_\_proto\_\_ 属性，包括上面提到的构造函数和原型对象：如上面的 `Test` 、`Test.prototype`。

   ```javascript
   > Object.__proto__
   ƒ () { [native code] }
   
   > Test.__proto__
   ƒ () { [native code] }
   
   > Test.prototype.__proto__
   {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
   ```



3. 那为什么存在两个proto呢？prototype 指向的是当前对象的原型对象，_\_proto\_\_ 指向的是实例化当前对象的构造函数的原型对象，描述的很精炼却很抽象，还是看图吧···

   > 特殊情况：Object.create(null)创建对象没有\_\_proto\_\_。

   ![prototype](https://github.com/WeijieZhu0204/Blog/blob/master/assets/images/prototype.png)

    `function Hello(){}、Object、Function`本质上都是一个对象，是Function构造器的一个实例，因此它的`__proto__`指向了Function构造函数的原型对象。



Done. 欢迎指正









