---
title: typescript泛型
category: typescript
tag: 编程语言
---

**介绍**

关于对 typescript 泛型的介绍，官网文档如下：

> 软件工程中，我们不仅要创建一致的定义良好的 API，同时也要考虑可重用性。 组件不仅能够支持当前的数据类型，同时也能支持未来的数据类型，这在创建大型系统时为你提供了十分灵活的功能。

文档中描述的比较简单，我们对泛型的概念还是很迷惑，下面我将从自己的理解来具体解释以下泛型。

首先我们知道 typescript 是基于类型系统的，基于类型系统好处，我们可以写出更高质量的代码，可以更好的设计程序，代码的可读性更强，大型项目中多人合作更方便等等。类型检查带来了好处，但是它也带了一定副作用。下面以一个例子说明：

```typescript
class Dog {}
class Person {
    constructor(pets: Dog) {
        this.pets = pets
    }
    pets: Dog
    name: string
}

let person: Person = new Person(new Dog())
```

上面的代码，声明了一个`Person`类，类里面有属性`pets`为`Dog`类型，如果我们需要拓展一个`Cat`类型怎办？重新声明一个类？如下：

```typescript
class Dog {}
class Cat {}
class Person {
    constructor(pets: Dog) {
        this.pets = pets
    }
    pets: Dog
    name: string
}

class Person2 {
    constructor(pets: Cat) {
        this.pets = pets
    }
    pets: Cat
    name: string
}

let person: Person = new Person(new Dog())
let person2: Person2 = new Person2(new Cat())
```

很明显`Person`和`Person2`只有`pets`属性的类型不同，其他都一样，如果重复声明两个类这样代码的可重用性太低了。

对于上面的问题，在`javascript`这种弱类型语言来说就不存在

```javascript
class Dog {}
class Cat {}
class Person {
    constructor(pets) {
        this.pets = pets
    }
}
let person = new Person(new Dog())
let person2 = new Person2(new Cat())
```

当然 typescript 也是兼容上面的写法，但是这样就跟写 js 没什么区别，就无法利用 typescript 的类型系统来对代码进行检测了。

所以这时候泛型就上场了。

```typescript
class Dog {}
class Cat {}
class Person<T> {
    constructor(pets: T) {
        this.pets = pets
    }
    pets: T
    name: string
}
let person: Person<Dog> = new Person(new Dog())
let person2: Person<Cat> = new Person(new Cat())
```

所以我觉得泛型的作用就是让我们既能类型系统带来的好处，又能像弱类型语言那样使 API 更加灵活，代码的可重用性更好。

**使用泛型变量**

```typescript
function identity<T>(arg: T): T {
    return arg
}
```

**泛型函数的类型**

```typescript
//变量fn是一个泛型函数
let fn: <T>(x: T) => T
//所以它的值只能使泛型函数
fn = function <T>(x: T): T {
    return x
}
fn = function () {} //error:不能将类型“<T>() => void”分配给类型“<T>(x: T) => T”

//使用其他变量也行，T与U相当于一个占位符
fn = function <U>(x: U): U {
    return x
}
```

使用对象签名字面量定义泛型函数

```typescript
let fn: { <T>(x: T): T }

fn = function <T>(x: T): T {
    return x
}
```

泛型接口

```typescript
interface FN {
    <T>(x: T): T
}

let fn: FN = function <T>(x: T): T {
    return
}
```

**泛型约束**

当我们使用泛型，编译器并不能证明每种类型都有某个属性。

```typescript
function test<T>(x: T): void {
    x.name //error:类型“T”上不存在属性“name”
}
```

这个时候我们可以定义一个接口来描述约束条件，并且通过`extends`关键字来实现约束。

```typescript
interface A {
    name: string
}
function test<T extends A>(x: T): void {
    x.name
}
```

**在泛型约束中使用类型参数**

如果我们要某个对象的属性值，并且我们想要确保这个属性存在于对象 obj 上，因此我们需要在这两个类型之间使用约束。

```typescript
function getProperty(obj: T, key: K) {
    return obj[key] //error
}
```

我们应该要约束`key`是`obj`的键中的一种

```typescript
function getProperty<T, K>(obj: T, key: K) {
    return obj[key] //error:类型“K”无法用于索引类型“T”
}

let x = { a: 1, b: 2, c: 3, d: 4 }

getProperty(x, 'a') // okay
getProperty(x, 'm')
```

**在泛型里使用类类型**

在 TypeScript 使用泛型创建工厂函数时，需要引用构造函数的类类型。比如：

```typescript
function create<T>(c: { new (): T }): T {
    return new c()
}
```

一个高级例子

```typescript
class BeeKeeper {
    hasMask: boolean
}

class ZooKeeper {
    nametag: string
}

class Animal {
    numLegs: number
}

class Bee extends Animal {
    keeper: BeeKeeper
}

class Lion extends Animal {
    keeper: ZooKeeper
}

function createInstance<A extends Animal>(c: new () => A): A {
    return new c()
}

createInstance(Lion).keeper.nametag // typechecks!
createInstance(Bee).keeper.hasMask // typechecks!
```
