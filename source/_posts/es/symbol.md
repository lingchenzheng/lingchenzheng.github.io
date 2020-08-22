---
title: Symbol对象
category: es
tag: js基础
date: 2019/6/26
---

**简介**

> Symbol 对象是 ES6 新增的一种基本类型数据，属于原始类型数据，表示独一无二的值。

**创建 Symbol 对象**

```javascript
//通过Symbol函数来创建一个symbol对象
let s = Symbol()
//可以传入一个字符串参数来描述当前symbol对象
let s2 = Symbol('hello')
typeof s // symbol
```

**symbol 不能与任何类型进行运算，否则报错，symbol 可以转换成字符串，或者 boolean 类型**

```javascript
let s = Symbol('hello')

String(s) // 'Symbol("hello")'
s.toString() // 'Symbol("hello")'
Boolean(s) // true
if (s) {
} // true
```

**使用 symbol 作为对象属性的 key，ES5 中，对象属性的都是字符串，这就导致，有时候我们需要给某个对象拓展属性，可能会造成命名冲突，覆盖原来的属性值，使用 symbol 可以解决这个问题，因为 symbol 是独一无二的。**

```javascript
let s = Symbol('foo')
const obj = {
    s: 'tom'
}
obj[s] = '张三'
obj.s //tom
obj[s] //张三
```

_注意：symbol 作为对象属性不能通过属性访问器来访问和设置，只能通过[s]来访问。而且 symbol 作为对象属性键，不能没枚举，也就是不能被 for in 和 for of 命中。也不能被 Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()返回。_

**消除魔术字符，魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。我们可以将这个字符串声明为一个变量，变量值为一个 symbol 对象**

```javascript
//比如表示某个状态
const status = Symbol('uploading')
```

**单例模式**

```javascript
const FOO_KEY = Symbol.for('foo')

function A() {
    this.foo = 'hello'
}
if (!global[FOO_KEY]) {
    global[FOO_KEY] = new A()
}
module.exports = global[FOO_KEY]
```

**Symbol.for()与 Symbol.keyFor()**

有时，我们希望重新使用同一个 Symbol 值，Symbol.for 方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建并返回一个以该字符串为名称的 Symbol 值。

```javascript
let s1 = Symbol.for('foo')
let s2 = Symbol.for('foo')

s1 === s2 // true
```

Symbol.keyFor 方法返回一个已登记的 Symbol 类型值的 key。

```javascript
let s1 = Symbol.for('foo')
Symbol.keyFor(s1) // "foo"
let s2 = Symbol('foo')
Symbol.keyFor(s2) // undefined
```
