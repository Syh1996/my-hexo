---
title: let没有变量提升？X
date: 2021-07-14 10:40:17
tags: js
---

### 证明var声明存在变量提升

通常我们是这样子来证明var存在变量提升的。

```js
function fn(){
    console.log(a) // undefined
    var a = 12
}
fn()
复制代码
```

为什么这段代码会输出undefined，而不是报错呢？

原因就是js在创建执行上下文时，会检查代码，找出变量声明和函数声明，并将函数声明完全存储在环境中，而将通过var声明的变量设定为undefined，这就是所谓的变量提升。从字面上理解就是变量和函数声明会被移动到函数或者全局代码的开头位置。

那么当我们将var替换为let时，结果又会如何？

```js
function fn(){
    console.log(a) // Uncaught ReferenceError: a is not defined
    let a = 12
}
fn()
复制代码
```

意料之中，代码报错了。很多人通过这个反例，便认为let没有变量提升，但其实这是错误的。上面举的例子只能证明，var存在变量提升，但是并不能证明let不存在变量提升。

### 证明let声明存在变量提升

我们再举一个例子：

```js
var x = 'parent value';
(function() {
  console.log(x); // parent value
}())
复制代码
```

代码会输出parent value，原因很简单，涉及到了**作用域链**的知识。在匿名函数作用域中没有找到x变量，便会沿着作用域链，找到父级作用域，然后便再父级作用域中找到了x变量，并输出。

接着我们在匿名函数中，加入let进行变量声明，此时结果会是如何呢？

```js
var x = 'parent value';
(function() {
  console.log(x); // Uncaught ReferenceError: x is not defined
  let x = 'child value'
}())
复制代码
```

想不到吧！此时的代码又会报错了，从这里其实可以看出let也是存在**变量提升**的，知识在变量显式赋值之前不能对变量进行读写，否则就会报错，这也就是所谓的let和const的**暂时性死区**。


作者：37.2℃同志链接：https://juejin.cn/post/6983702070293430303来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。