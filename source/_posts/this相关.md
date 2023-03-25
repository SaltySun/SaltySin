---
title: this相关
tags: js
date: 2022-08-15 00:00:00
category: 前端
---

## 什么是this？
在JavaScript中，全局里的this都指向最顶层（浏览器中即window）。
## 关于绑定
### 默认绑定
```vue
function f00() {
  console.log(this.a);
}

var a = 2;

foo(); // 2
```
在全局作用域下用 var 关键字声明的变量和在全局声明的 函数 会被挂载到全局对象(window)上。调用 foo() 时,我们都知道,全局声明的函数的作用域是顶层的 globalObject 在浏览器中也就是 window。
在代码中,foo() 是直接使用不带任何修饰的函数引用进行调用的,因此只能使用默认绑定,所以函数中的 this 为 window,也就是 window.a,所以自然而然的就输出 2 了。
```vue
function f00() {
  "use strict";

  console.log(this.a);
}

var a = 2;

f00(); // Cannot read properties of undefined (reading 'a')

// 因为严格默认情况下,默认绑定,this会被绑定为 undefined ,所以this.a也就等于undivided.a
// 因为 undefined 下没有 a 的属性,所以会报类型错误
```
```vue
function f00() {
  console.log(this.a);
}

var a = 2;

(function () {
  "use strict";
  f00(); // 2
})();
  //如果 foo()运行在非 strict mode 下时,默认绑定才能绑定到全局对象,在严格模式 foo() 则不影响默认绑定。
```
### 隐式绑定
隐式绑定的规则是调用位置是否有上下文对象,或者说是否被某个对象拥有或者包含。
```vue
function foo() {
  console.log(this.a);
}

var obj = {
  a: 111,
  foo,
};

obj.foo(); // 111
```
需要注意的是 foo() 的声明方式,以其之后是如何被当做引用属性添加到 obj 对象中的。但是无论是直接在 obj 中定义还是先定义再添加为引用属性,这个函数严格来说都不属于 obj 对象。
然而调用位置会使用 obj 上下文来引用函数,因此你可以说函数被调用时 obj 对象 "拥有" 或者 "包含" 函数引用。
当函数引用有上下文对象时,隐式绑定规则会把函数调用中的this绑定到这个上下文对象。因此 this.a 和 obj.a 是一样的。
```vue
function foo() {
  console.log(this.a);
}

var obj2 = {
  a: 111,
  foo,
};

var obj1 = {
  a: 777,
  obj2,
};

obj1.obj2.foo(); // 111

// 对象 obj2 为最后一层
// obj1.obj2 仅为属性查找,并还没有开始调用
```
#### 函数脱离原上下文
一个最常见 this 绑定问题就是被隐式绑定的函数会丢失绑定对象,也就是说他会应用默认绑定默认。
```vue
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo,
};

var bar = obj.foo; // 函数别名

var a = "我是全局a";

bar(); // 我是全局a
```
虽然 bar 是 obj.foo 的一个引用,但是实际上,它引用的是 foo 函数的本身,因此此时的 bar() 其实是一个普通的函数调用 因此应用了默认绑定。
这实际上是重新定义了一个 bar 函数,和对象的结构一样,都是重新赋值
```vue
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo,
};

var { foo } = obj; // 这里相当于重新定义了一个函数或者说这是一个函数别名

var a = "我是window下的a";

foo(); // 我是window下的a

var object = {
  moment: 777,
  age: 18,
};

console.log(object); // {moment: 777, age: 18}
var { moment } = object;

moment = "牛逼";

console.log(moment); // 牛逼

console.log(object); // {moment: 777, age: 18}
```
实际上在全局作用域中创建了一个变量 moment 并赋值为 777,后面的直接修改变量不修改对象 object 中的属性 moment。
#### 函数作为参数
```vue
function foo() {
  console.log(this.a);
}

function bar(fn) {
  // fn 其实是引用 foo

  fn();
}

var obj = {
  a: 777,
  foo,
};

var a = "牛逼啊,这也行";

bar(obj.foo); // 牛逼啊,这也行
```
参数传递其实就是一种隐式赋值,因此我们传入函数时也会被隐式赋值,上面这段代码实际上就是以下代码的变体
```vue
function foo() {
  console.log(this.a);
}

function bar() {
  const fn = obj.foo;

  fn();
}

var obj = {
  a: 777,
  foo,
};

var a = "牛逼啊,这也行";

bar(); // 牛逼啊,这也行
```
### 显示绑定
一般情况下，宿主环境提供的一些函数还是你自己创建的函数,你都可以使用 call(...) 和 apply(...) 方法。
他们的第一个参数是一个对象,是给this准备的,接着在调用函数时将其绑定到 this。因为你可以直接指定 this 的绑定对象,因此我们称之为 **显示绑定**
```vue
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
};

var bar = function () {
  foo.call(obj);
};

bar(); // 2
setTimeout(bar, 1000); // 2

// 硬绑定的 bar 不可能再修改他的 this

bar.call(window); // 2
```
许多内置函数,都提供了一个可选的参数,通常成为 上下文,其作用和 bind(...) 一样,确保你的回调函数使用指定的 this。
```vue

function callback(element) {
  console.log(element, this.id);
}

var obj = {
  id: "真不错",
};

// 调用 foo(...) 时把 this 绑定到 obj 上
[1, 2, 3].forEach(callback, obj);
// 1 '真不错'  2 '真不错'  3 '真不错'

// 俺 map 也一样

[1, 2, 3].map(callback, obj);
// 1 '真不错'  2 '真不错'  3 '真不错'

```
### new绑定
使用 new 来调用构造函数会执行：

1. 在内存中创建一个新对象;
2. 这个新对象内部的 [[prototype]] 特性 被赋值为构造函数的 prototype属性 
3. 构造函数中内部的 this 被赋值为这个新对象(即 this 指向新对象);
4. 执行构造函数内部的代码(给新对象添加属性);
5. **_如果构造函数返回非空对象,则返回该对象;否则,返回刚创建的新对象;_**
```vue

function Foo(moment) {
  this.moment = moment;
}

var bar = new Foo(777);

console.log(bar.a); // 777
  // 使用 new 来调用 Foo(...) 时,我们会构造一个新对象并把他绑定到 Foo(...) 调用中的 this 上。
```
如果构造函数返回一个对象,则该对象将作为整个表达式的值返回,而传入的构造函数的 this 将会被抛弃。
```javascript
var mayDay = {
  moment: "moment"
}

function Foo() {
  this.moment = 777;

  return 111; // 这里的返回值变化了
}

var bar = new Foo();

console.log(bar.moment); // 777 输出的是新对象的 moment

```
## 箭头函数
箭头函数表达式的语法比函数表达式更简洁,并且没有自己的 this, arguments,super或new.target。箭头函数表达式更适用于那些本来需要匿名函数的地方,并且它不能用作构造函数。正是因为箭头函数没有 this,自然而然的就不能使用 new 操作符了。
```javascript

var moment = "moment";

var bar = {
  moment: 777,
  general: function () {
    console.log(this.moment);
  },
  arrow: () => {
    console.log(this.moment);
  },
  nest: function () {
    var callback = () => {
      console.log(this.moment);
    };
    callback();
  },
};

bar.general(); // 777
bar.arrow(); // moment
bar.nest(); // 777

```
## apply，bind 以及 call
apply和call的用法大同只不过传入的参数不同。
apply的第二个参数传的是参数类数组，call是多个参数。
```javascript
function.call(thisArg, arg1, arg2, ...)
function.apply(thisArg, [argArray])
```
thisArg：在 fun 函数运行时指定的 this 值。需要注意的是，指定的 this 值并不一定是该函数执行时真正的 this 值，如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动指向全局对象（浏览器中就是window对象），同时值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的自动包装对象。
```javascript
    var a ={
        name : "Cherry",
        fn : function (a,b) {
            console.log( a + b)
        }
    }

    var b = a.fn;
    b.call(a,1,2)       // 3
		b.apply(a,[1,2])       // 3
```
bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。
**引用MDN上的**[**描述**](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind#%E6%8F%8F%E8%BF%B0)**：**
**bind()** 函数会创建一个新的**绑定函数**（**bound function**，BF）。绑定函数是一个 exotic function object（怪异函数对象，ECMAScript 2015 中的术语），它包装了原函数对象。调用**绑定函数**通常会导致执行**包装函数**。 **绑定函数**具有以下内部属性：

- **[[BoundTargetFunction]]** - 包装的函数对象
- **[[BoundThis]]** - 在调用包装函数时始终作为 **this** 值传递的值。
- **[[BoundArguments]]** - 列表，在对包装函数做任何调用都会优先用列表元素填充参数列表。
- **[[Call]]** - 执行与此对象关联的代码。通过函数调用表达式调用。内部方法的参数是一个**this**值和一个包含通过调用表达式传递给函数的参数的列表。

当调用绑定函数时，它调用 **[[BoundTargetFunction]]** 上的内部方法 **[[Call]]**，就像这样 **Call(_boundThis_, _args_)**。其中，**boundThis** 是 **[[BoundThis]]**，**args** 是 **[[BoundArguments]]** 加上通过函数调用传入的参数列表。
绑定函数也可以使用 [new](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new) 运算符构造，它会表现为目标函数已经被构建完毕了似的。提供的 this 值会被忽略，但前置参数仍会提供给模拟函数。
```javascript
    var a ={
        name : "Cherry",
        fn : function (a,b) {
            console.log( a + b)
        }
    }

    var b = a.fn;
    b.bind(a,1,2)()           // 3
```
