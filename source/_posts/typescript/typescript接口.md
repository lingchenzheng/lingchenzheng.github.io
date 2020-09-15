---
title: typescript接口
category: typescript
tag: 编程语言
---

##### 介绍

在面向对象语言中，接口是一个很重要的概念(java,dart...)，它表示对行为的抽象和约束，比如我们定义了一个接口，这个接口规定有一个方法`eat`，接口只提供方法名，不提供方法实现，具体实现由实现接口的对象来决定。

> TypeScript 的核心原则之一是对值所具有的结构进行类型检查。 它有时被称做“鸭式辨型法”或“结构性子类型化”。 在 TypeScript 里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。

##### 使用接口

```typescript
interface LabelledValue {
    label: string
}

function printLabel(labelledObj: LabelledValue) {
    console.log(labelledObj.label)
}

let myObj = { size: 10, label: 'Size 10 Object' }
printLabel(myObj)
```

**可选属性**

可以通过`?`指定可选属性，表示这个属性可有可无。

```typescript
interface Person {
    name: string
    age?: number
}
//name是必须的，age是可选的
function foo(p: Person) {
    console.log(p.name)
}

foo({ name: 'Tom' })
foo({ name: 'Tom', age: 25 })
```

**额外属性检擦**

我们使用可选属性后，这个属性可填可不填，这样编译器是不会报错的，但是，当我们使用了额外属性时，会得到一个错误。

一个例子

```typescript
interface Person {
    name: ?string
    age?: number
}

function test(person: Person) {
    //TODO
}

test({ name: 'Tom', height: 300 }) //会得到如下错误
//类型“{ name: string; height: number; }”的参数不能赋给类型“Person”的参数。
// 对象文字可以只指定已知属性，并且“height”不在类型“Person”中。
```

可以通过如下方法绕过额外属性检查

-   类型断言

```typescript
interface Person {
    name?: string
    age?: number
}
function test(person: Person) {}
test({ name: '李四', height: 300 } as Person)
```

-   添加一个字符串索引签名

```typescript
interface Person {
    name?: string
    age?: number
    [prop: string]: any
}
function test(person: Person) {}
//下面都是正确的
test({ name: '李四', height: 300 })
test({ name: '李四', height: 300, country: 'China' })
```

-   将对象赋值给另一个变量，因为这个变量不会经过额外属性检查，所以编译器不会报错。

```typescript
interface Person {
    name?: string
    age?: number
}
function test(person: Person) {}
let p = { name: '李四', height: 300 }
test(p)
```

_虽然可以绕过额外属性检查，但是这些额外属性我们是不可以使用的，即在函数内部无法访问_

**只读属性**

某些对象只能在创建时赋值，可以通过`readonly`来指定只读属性。

```typescript
interface Point {
    readonly x: number
    readonly y: number
}

let p1: Point = { x: 10, y: 20 }
p1.x = 5 // error!
```

ReadonlyArray\<T\>类型

```typescript
let a: number[] = [1, 2, 3, 4]
let ro: ReadonlyArray<number> = a
ro[0] = 12 // error!
ro.push(5) // error!
ro.length = 100 // error!
a = ro // error!
```

**函数类型**

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。

```typescript
interface PersonFn {
    (name: string, age: number): boolean
}

let pf = function (name: string, age: number): boolean {
    return age > 0
}
```

**可索引类型**

与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型，比如 `a[10]`或 `ageMap["daniel"]`。 可索引类型具有一个 索引签名，它描述了对象索引的类型，还有相应的索引返回值类型。

```typescript
interface StringArray {
    [index: number]: string
}

let myArray: StringArray
myArray = ['Bob', 'Fred']

let myStr: string = myArray[0]
```

typescript 支持两种数据签名：字符串和数字。

索引不能重复，重复是指类型，不是指前面的标识符

```typescript
interface Test {
    [x: number]: string
    [y: number]: string //error，已经有一个number类型的索引了
}
```

可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。因为使用 number 类型的索引时会将它转成字符串再去索引。

```typescript
interface Cat {
    name: string
}
interface Dog {
    age: number
}
type Animal = Cat | Dog
interface Pets {
    [x: number]: Dog //error Dog不是Cat的子类型
    [x: string]: Cat
}
```

下面这样是正确的

```typescript
interface Cat {
    name: string
}
interface Dog {
    age: number
}
type Animal = Cat | Dog
interface Pets {
    [x: number]: Dog //Dog是Animal的子类型
    [x: string]: Animal
}
```

可以将索引签名设置为只读，这样就防止了给索引赋值：

```typescript
interface ReadonlyStringArray {
    readonly [index: number]: string
}
let myArray: ReadonlyStringArray = ['Alice', 'Bob']
myArray[2] = 'Mallory' // error!
```

**接口实现**

与 Java 里接口一样，一个类也可以通过实现接口去强制约束这个类符合某种契约。

```typescript
interface Person {
    name: string
    age: number
}
//强制Man实现name和age属性
class Man implements Person {
    name: string
    age: number
}
```

_接口只是描述了类的公共部分属性，不能检查私有成员_

**接口继承**

一个接口可继承成里一个接口

```typescript
interface Shape {
    color: string
}
interface Square extends Shape {
    sideLength: number
}
let square = <Square>{}
square.color = 'blue'
square.sideLength = 10
```

多重继承，一个接口可以继承多个接口

```typescript
interface Shape {
    color: string
}

interface PenStroke {
    penWidth: number
}

interface Square extends Shape, PenStroke {
    sideLength: number
}
let square = <Square>{}
square.color = 'blue'
square.sideLength = 10
square.penWidth = 5.0
```

**混合类型**

一个对象可以同时做为函数和对象使用，并带有额外的属性。

```typescript
interface Counter {
    (start: number): string
    interval: number
    reset(): void
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) {}
    counter.interval = 123
    counter.reset = function () {}
    return counter
}

let c = getCounter()
c(10)
c.reset()
c.interval = 5.0
```

**接口继承类**

当接口继承了一个类类型时，它会继承类的成员但不包括其实现。如果被接口继承的类包含私有属性，即 private 或者 protected 修饰的成员时，这个接口只能被它继承类的子类实现。

```typescript
class Biology {
    private name: string
}
interface Person extends Biology {
    age: number
}
class Man extends Biology implements Person {
    age: number
}
//下面是错误的
class Man implements Person {
    age: number
}
```
