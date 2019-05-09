---
title: 原型和原型链
date: 2019-05-08 07:46:37
tags:
  - js
conver: /static/prototype/rain.jpg
---

![JavaScript Object Layout](/static/prototype/prototype.jpg)

以前对原型以及原型链一直理解不够，只是知道通过特定的模式实现继承，最近整理并记录一下对这方面的一些新的认识。

## 概念

首先必须说一下JavaScript中的继承：

> 继承是OO语言中的一个最为人津津乐道的概念.许多OO语言都支持两种继承方式:接口继承和实现继承。接口继承只继承方法签名,而实现继承则继承实际的方法。由于js中函数没有签名,在ECMAScript中无法实现接口继承。ECMAScript只支持 **实现继承** ,而且其实现继承 主要是依靠 **原型链** 来实现的

由此可见，在js中原型链是实现继承的主要方法。

然后我们就要说到prototype（原型）：我们创建的每个函数都有一个prototype属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。换句话说就是可以让所有对象实例共享它所包含的属性和方法。

```JavaScript
function fn () {}
fn.prototype.a = 'aaaa'
var o1 = new fn()
var o2 = new fn()
console.log(o1.a)
// 'aaaa'
console.log(o2.a)
// 'aaaa'
```

原型和实例的关系：

> 每个构造函数都有一个原型对象(prototype),原型对象都包含一个指向构造函数的指针(constructor),而实例(instance)都包含一个指向原型对象的内部指针(\_\_proto\_\_)。

下图以Objec构造函数为例：

![Object function](/static/prototype/instance-fun-proto.jpg)

如果我们让原型对象等于另一个类型的实例，即另一个构造函数创建的对象实例，此时原型对象将包含一个指向另一个原型的指针，实例将在 `instance.__proto__.__proto__` 中找到这另一个原型的属性和方法(实例的 \_\_proto\_\_ 属性等于其构造函数的 prototype 属性)。如此层层递进，就构成了实例和原型的链条，即原型链。

当然实例可以直接获取原型链中的属性和方法，不需要通过\_\_proto\_\_ 属性去访问。因为如果要试图引用对象的某个属性，会首先在对象内部属性中查找一遍，如果找不到，就会接着在\_\_proto\_\_ 对象中，也就是构造函数的原型对象(constructor.prototype)中去查找，直到Object.prototype。

## 确定原型和实例的关系

使用原型链后，我们可以使用`instanceof`操作符来判断原型和实例的关系，如果某个构造函数出现在实例的原型链中，结果会返回为`true`，以上面的例子说明：

```js
console.log(o1 instanceof fn)   // true
console.log(o1 instanceof Function) // false
console.log(o1 instanceof Object)   // true
```

## 几个常见方法创建对象的原型链

- 使用对象字面量创建的对象，其 `__proto__` 指向 `Object.prototype`。
- 使用数组字面量创建的对象，其 `__proto__` 指向 `Array.prototype`。
- 使用`function`关键字创建的对象，其 `__proto__` 指向 `Function.prototype`。
- 使用js构造函数创建的对象，其 `__proto__` 指向该构造函数的 `prototype` 属性。

```js
let o = {a: 1};
// 原型链: o ---> Object.prototype ---> null
let a = ["a", "b", "c"];
// 原型链: a ---> Array.prototype ---> Object.prototype ---> null
function f(){}
// 原型链: f ---> Function.prototype ---> Object.prototype ---> null
let fun = new Function();
// 原型链: fun ---> Function.prototype ---> Object.prototype ---> null
function Foo() {}
let foo = new Foo();
// 原型链: foo ---> Foo.prototype ---> Object.prototype ---> null
```

由此可见，`Object.prototype`就是原型链的顶层，所有对象都继承了它的属性和方法，而它的 `__proto__` 是 null。

## 一些值得一提的地方

- 大多数对象的 `__proto__`  是指向的是构造自己的函数的 `prototype`  对象，然而只有Function的 `__proto__` 指向了 `Function.prototype` ，即指向了自己的 `prototype`对象
- 只有函数才有 `prototype` 属性，但并不是所有函数都有这个属性，`Function.prototype` 就是这样的函数：

```js
Function.prototype
// ƒ () { [native code] }
Function.prototype.prototype
// undefined
let fun = Function.prototype.bind()
// ƒ () { [native code] }
fun.prototype
// undefined
```

如果所有的函数都有 `prototype` 属性，那 `Function.prototype.prototype...` 将永无止尽。

- 箭头函数没有 `prototype` 属性


