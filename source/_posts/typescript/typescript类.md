---
title: typescript类
category: typescript
tag: 编程语言
---

##### 介绍

在面向对象编程语言里都有类的概念，比如 Java，C#等等。类就是对具有相同特征事物的描述，个人觉得它就像是一个模板，根据这个模板制造具有相同特征的不同对象。

typescript 中类的简单使用

```typescript
class Person {
    constructor(name: string) {
        this.name = name
    }
    name: string
    eat() {}
}
```

##### 类的继承

基于类的程序设计中一种最基本的模式是允许使用继承来扩展现有的类，可以重用代码。

```typescript
class Person {
    name: string
    constructor(name: string) {
        this.name = name
    }
}

class Man extends Person {
    age: number
}
let man = new Man('Tom')

man.age = 23
```

派生子类可以对基类的方法重写，同时也可以通过`super`调用基类的方法。

```typescript
class Person {
    name: string
    constructor(name: string) {
        this.name = name
    }
    eat() {
        console.log('super')
    }
}

class Man extends Person {
    age: number
    eat() {
        console.log('this')
    }
    baseEat() {
        //调用父类的eat方法
        super.eat()
    }
}
```

##### 属性修饰符`private,protected,public`

**public(默认修饰符)**

属性不指定修饰符时默认采用 public 修饰符，表示公共的，公共属性可以在类的外面访问。

```typescript
class Person {
    public name: string
    age: number
}
let person: Person = new Person()
//在类的外面访问name与age属性
p.name
p.age
```

**private**

表示私有属性，只能在类的内部访问。

```typescript
class Person {
    private name: string
    constructor(name: string) {
        this.name = name
    }
}
let person: Person = new Person('李四')
person.name //属性“name”为私有属性，只能在类“Person”中访问。
```

结构类型系统

typescript 采用的是结构类型系统，在比较两种不同的类型时，并不在乎它们从何处而来。

```typescript
interface Foo {
    name: string
    age: number
}
interface Bar {
    name: string
    age: number
}
let foo: Foo = {
    name: 'Tom',
    age: 24
}
//因为结构相同，所以ts认为它们是同一个类型
let bar: Bar = foo
```

_注意：当我们比较带有 private 或 protected 成员的类型的时候，情况就不同了。 如果其中一个类型里包含一个 private 成员，那么只有当另外一个类型中也存在这样一个 private 成员， 并且它们都是来自同一处声明时，我们才认为这两个类型是兼容的。 对于 protected 成员也使用这个规则。_

```typescript
class Animal {
    private name: string
    constructor(theName: string) {
        this.name = theName
    }
}
class Rhino extends Animal {
    constructor() {
        super('Rhino')
    }
}
class Employee {
    private name: string
    constructor(theName: string) {
        this.name = theName
    }
}
let animal = new Animal('Goat')
let rhino = new Rhino()
let employee = new Employee('Bob')

animal = rhino
animal = employee // 错误: Animal 与 Employee 不兼容.
```

因为 Animal 和 Rhino 共享了来自 Animal 里的私有成员定义 private name: string，因此它们是兼容的。 然而 Employee 却不是这样。当把 Employee 赋值给 Animal 的时候，得到一个错误，说它们的类型不兼容。

**protected**

表示受保护的属性，与 private 不同的是，protected 修饰的属性在派生子类中可以访问

```typescript
class Person {
    protected name: string
}
class Man extends Person {
    setName(name: string) {
        this.name = name
    }
}
let man: Man = new Man()
man.setName('李四')
```

**readonly**
readonly 修饰的属性表示只读，只读属性必须在声明时或构造函数里被初始化。

```typescript
class Person {
    readonly name: string
}
let person: Person = new Person()
person.name = 'Tom' //error:无法分配到 "name" ，因为它是只读属性。
```

##### 参数属性

一般来说，我们需要定义类的成员，并且在构造函数给属性赋值。通过参数属性，我们可以在一个地方定义并初始化一个成员。

```typescript
class Person {
    constructor(public name: string, readonly age: number) {}
}

let p: Person = new Person('Tom', 24)
```

##### 存储器

typescript 支持 setter\/getter 方式来访问成员

```typescript
let passcode = 'secret passcode'
class Employee {
    private _fullName: string

    get fullName(): string {
        return this._fullName
    }

    set fullName(newName: string) {
        if (passcode && passcode == 'secret passcode') {
            this._fullName = newName
        } else {
            console.log('Error: Unauthorized update of employee!')
        }
    }
}

let employee = new Employee()
employee.fullName = 'Bob Smith'
if (employee.fullName) {
    alert(employee.fullName)
}
```

##### 静态属性

静态属性不属于实例成员，属于类本身的属性，使用`static`关键字修饰

```typescript
class Grid {
    static origin = { x: 0, y: 0 }
    calculateDistanceFromOrigin(point: { x: number; y: number }) {
        let xDist = point.x - Grid.origin.x
        let yDist = point.y - Grid.origin.y
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale
    }
    constructor(public scale: number) {}
}

let grid1 = new Grid(1.0) // 1x scale
let grid2 = new Grid(5.0) // 5x scale

console.log(grid1.calculateDistanceFromOrigin({ x: 10, y: 10 }))
console.log(grid2.calculateDistanceFromOrigin({ x: 10, y: 10 }))
```

##### 抽象类

抽象类一般作为派生类的基类，与接口一样不能被实例化。与接口区别是，它可以定义成员的实现细节。

使用关键字`abstract`定义抽象类，抽象类里可以定义抽象方法，抽象方法通用使用`abstract`关键字修饰，抽象方法的具体实现由派生类决定。

_注意：抽象方法的`public,private,protected`修饰符不能省略，派生类必须实现抽象方法_

```typescript
abstract class Person {
    //抽象方法
    abstract eat(): void
    //具体方法
    move() {}
}

class Man extends Person {
    //重写抽象方法
    eat() {}
}
```

-   类定义会创建两个东西：类的实例类型和一个构造函数。

构造函数

```typescript
class Greeter {
    static standardGreeting = 'Hello, there'
    greeting: string
    greet() {
        if (this.greeting) {
            return 'Hello, ' + this.greeting
        } else {
            return Greeter.standardGreeting
        }
    }
}

let greeter1: Greeter
greeter1 = new Greeter()
console.log(greeter1.greet())
//去Greeter构造函数类的类型，而不是实例类型，然后将自身赋值给自身构造函数的类型
let greeterMaker: typeof Greeter = Greeter
greeterMaker.standardGreeting = 'Hey there!'

let greeter2: Greeter = new greeterMaker()
console.log(greeter2.greet())
```

因为类可以创建出类型，所以你能够在允许使用接口的地方使用类。

```typescript
class Point {
    x: number
    y: number
}
interface Point3d extends Point {
    z: number
}
let point3d: Point3d = { x: 1, y: 2, z: 3 }
```
