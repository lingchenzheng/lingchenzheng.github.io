---
title: typescript高级类型
category: typescript
tag: 编程语言
---

##### 交叉类型(Intersection Types)

交叉类型是将多个类型合并为一个类型，使用`&`操作符。

例如：

```typescript
interface A {
    a: number
}
interface B {
    b: number
}
//C类型既有a属性也有b属性
type C = A & B
```

大多是在混入（mixins）或其它不适合典型面向对象模型的地方看到交叉类型的使用。

```typescript
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{}
    for (let id in first) {
        ;(<any>result)[id] = (<any>first)[id]
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            ;(<any>result)[id] = (<any>second)[id]
        }
    }
    return result
}
class Person {
    constructor(public name: string) {}
}
interface Loggable {
    log(): void
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person('Jim'), new ConsoleLogger())
var n = jim.name
jim.log()
```

##### 联合类型(Union Types)

联合类型表示几种类型之一，使用`|`操作符。

```typescript
//如下Customer既可以是字符类型，也可以是数值类型
type Customer = string | number
let c: Customer
c = 123
c = 'abc'
```

**访问联合类型的属性**

如果联合类型有属性，我们只能访问所有类型中的共同属性

```typescript
interface A {
    name: string
    say(): void
}
interface B {
    age: number
    say(): void
}
type C = A | B
let o: C
o.say()
o.name //error:类型C上不存在name属性
o.age //error:类型C上不存在age属性
```

如果需要访问联合类型上不是共同的属性，需要通过类型断言来访问。

```typescript
interface A {
    name: string
    say(): void
}
interface B {
    age: number
    say(): void
}
type C = A | B
let o: C

let n = (o as A).name
let m = (<A>o).name
```

**自定义类型保护**

```typescript
interface A {
    name: string
    say(): void
}
interface B {
    age: number
    say(): void
}
type C = A | B
function test(o: C) {
    if ((o as A).name) {
        console.log((o as A).name)
    } else {
        console.log((o as B).age)
    }
}
```

在上面的例子中，我们多次使用了类型断言，比较麻烦。如果我们希望一旦检查过类型，后面的分支就可以清除地知道数据的类型？

```typescript
interface A {
    name: string
    say(): void
}
interface B {
    age: number
    say(): void
}
type C = A | B

function isA(obj: A | B): obj is A {
    return (obj as A).name !== undefined
}
function test(o: C) {
    if (isA(o)) {
        console.log(o.name)
    } else {
        console.log(o.age)
    }
}
```

在这个例子里，`obj is A`就是类型谓词。 谓词为`parameterName is Type` 这种形式，`parameterName` 必须是来自于当前函数签名里的一个参数名。
_typescript 不仅知道 if 里是 A 类型，它还知道 else 里面一定不是 A 类型_

**`typeof`类型保护**

```typescript
function isNumber(o: any): o is number {
    return typeof o === 'number'
}
function isFunction(o: any): o is Function {
    return typeof o === 'function'
}
```

**instanceof 类型保护**

```typescript
function isDate(o: any): o is Date {
    return o instanceof Date
}
```

**类型别名**

我们可以使用`type`关键字给类型起一个新名字，类型别名有时和接口很像，但是可以作用于原始值，联合类型，元组以及其它任何你需要手写的类型。

```typescript
type Name = string
type Test = number | string
type Check = () => string
```

类型别名使用泛型

```typescript
type Tree<T> = {
    value: T
    left: Tree<T>
    right: Tree<T>
}
```

`interface`(接口)与`type`(类型别名)的区别

1. 接口会创建一个新的类型，类型别名不会创建新的类型
2. 类型别名不能被 extends 和 implements(自己也不能 extends 和 implements 其它类型)

##### 字符串字面量类型

```typescript
type Names = 'Tom' | 'Jack' | 'Jhon'
function checkName(name: Names) {
    if (name === 'Tom') {
    } else if (name === 'Jack') {
    } else if (name === 'Jhon') {
    }
}
```

##### 数字字面量类型

```typescript
type Num = 1 | 2 | 6
```

##### 可辨识联合类型

个人觉得可辨识联合类型就是通过某一个可辨识的特征，来确定具体是哪一种类型。

打个比方，A，B，C 三个人，A 叫张三，B 叫李四，C 也叫李四，那我们我们怎么来区分它们，名字肯定不行，首先必须找个他们共同拥有的属性，而且这个属性值不同。由此我们想到使用省份证号可以。

typescript 基于 javascript 模式，如果满足以下三个要素，则会自动辨别类型(收窄类型)。

-   具有普通的单例类型属性— 可辨识的特征
-   一个类型别名包含了那些类型的联合— 联合
-   此属性上的类型保护

```typescript
interface A {
    id: '1'
    name: string
}
interface B {
    id: '2'
    age: number
}
interface C {
    id: '3'
    height: number
}
type DU = A | B | C
function test(person: DU) {
    switch (person.id) {
        case '1':
            //类型辨识为A
            person.name
            break
        case '2':
            //类型辨识为B
            person.age
            break
        case '3':
            //类型辨识为C
            person.height
            break
        default:
            break
    }
}
```

**完整性检查**

上面的例子中我们可以看到，如果某天你的同事在有加了一个类型 D，代码编程输入下。

```typescript
interface A {
    id: '1'
    name: string
}
interface B {
    id: '2'
    age: number
}
interface C {
    id: '3'
    height: number
}
interface D {
    id: '4'
}
type DU = A | B | C | D
function test(person: DU) {
    switch (person.id) {
        case '1':
            //类型辨识为A
            person.name
            break
        case '2':
            //类型辨识为B
            person.age
            break
        case '3':
            //类型辨识为C
            person.height
            break
        default:
            break
    }
}
```

在`test`函数里并没有 D 类型的处理逻辑，这个时候编译器并不会报错，可以通过编译，因为它可以走默认值。但是，如果我们必须要对每种类型处理，即在可辨识联合的变化时，我们想让编译器可以通知我们。

**使用`never`来检查完整性**

```typescript
function test(person: DU) {
    switch (person.id) {
        case '1':
            //类型辨识为A
            person.name
            break
        case '2':
            //类型辨识为B
            person.age
            break
        case '3':
            //类型辨识为C
            person.height
            break
        default:
            //如果不能到达这个分支，则可以通过编译，如果达到了则报错
            let x: never = person
            break
    }
}
```

##### 态`this`类型

多态的 this 类型表示的是某个包含类或接口的 子类型。 这被称做 F-bounded 多态性。 它能很容易的表现连贯接口间的继承。

```typescript
class BasicCalculator {
    public constructor(protected value: number = 0) {}
    public currentValue(): number {
        return this.value
    }
    public add(operand: number): this {
        this.value += operand
        return this
    }
    public multiply(operand: number): this {
        this.value *= operand
        return this
    }
}

class ScientificCalculator extends BasicCalculator {
    public constructor(value = 0) {
        super(value)
    }
    public sin() {
        this.value = Math.sin(this.value)
        return this
    }
    // ... other operations go here ...
}

let v = new ScientificCalculator(2).multiply(5).sin().add(1).currentValue()
```

如果没有 this 类型， ScientificCalculator 就不能够在继承 BasicCalculator 的同时还保持接口的连贯性。 multiply 将会返回 BasicCalculator，它并没有 sin 方法。 然而，使用 this 类型， multiply 会返回 this，在这里就是 ScientificCalculator。

##### 索引类型

使用索引类型，编译器就能够检查使用了动态属性名的代码。 例如，一个常见的 JavaScript 模式是从对象中选取属性的子集。

```typescript
function getProp<T, K extends keyof T>(o: T, prop: K): T[K] {
    return o[prop]
}
```

**`keyof`索引类型操作符**

语法：`keyof T`

对于任何类型 `T`， `keyof T` 的结果为 `T` 上已知的公共属性名的联合。

例如

```typescript
interface Person {
    name: string
    age: number
}
let personKeys: keyof Person //personKeys为 'name' | 'age'类型
//或者
type personKeys = keyof Person
```

**索引类型和字符串索引签名**

`keyof`和 `T[K]`与字符串索引签名进行交互。 如果你有一个带有字符串索引签名的类型，那么 `keyof T`会是 `string`。 并且 `T[string]`为索引签名的类型：

```typescript
interface Map<T> {
    [key: string]: T
}
let keys: keyof Map<number> // string
let value: Map<number>['foo'] // number
```

##### 映射类型

typescript 提供一种从旧类型创造新类型的方式。

简单例子

```typescript
type Keys = 'option1' | 'option2'
type Flags = { [K in Keys]: boolean }

//Flag -> type Flag {option1:boolean,options2:boolean}
```

它的语法与索引签名的语法类型，内部使用了 for .. in。 具有三个部分：

1. 类型变量 K，它会依次绑定到每个属性。
2. 字符串字面量联合的 Keys，它包含了要迭代的属性名的集合。
3. 属性的结果类型。

使用泛型的通用写法

```typescript
interface Person {
    name: string
    age: number
}

type MapPerson<T> = {
    [P in keyof T]?: T[P]
}

type OptionalPerson = MapPerson<Person>

function test(person: OptionalPerson) {
    console.log(person)
}
//只传入name也是可以的，因为他们都是可选的
test({ name: 'abc' })
```

##### 预定义类型

-   `Exclude<T, U>` -- 从 T 中剔除可以赋值给 U 的类型。
-   `Extract<T, U>` -- 提取 T 中可以赋值给 U 的类型。
-   `NonNullable<T>` -- 从 T 中剔除 null 和 undefined。
-   `ReturnType<T>` -- 获取函数返回值类型。
-   `InstanceType<T>` -- 获取构造函数类型的实例类型。

```typescript
type T00 = Exclude<'a' | 'b' | 'c' | 'd', 'a' | 'c' | 'f'> // "b" | "d"
type T01 = Extract<'a' | 'b' | 'c' | 'd', 'a' | 'c' | 'f'> // "a" | "c"

type T02 = Exclude<string | number | (() => void), Function> // string | number
type T03 = Extract<string | number | (() => void), Function> // () => void

type T04 = NonNullable<string | number | undefined> // string | number
type T05 = NonNullable<(() => string) | string[] | null | undefined> // (() => string) | string[]

function f1(s: string) {
    return { a: 1, b: s }
}

class C {
    x = 0
    y = 0
}

type T10 = ReturnType<() => string> // string
type T11 = ReturnType<(s: string) => void> // void
type T12 = ReturnType<<T>() => T> // {}
type T13 = ReturnType<<T extends U, U extends number[]>() => T> // number[]
type T14 = ReturnType<typeof f1> // { a: number, b: string }
type T15 = ReturnType<any> // any
type T16 = ReturnType<never> // any
type T17 = ReturnType<string> // Error
type T18 = ReturnType<Function> // Error

type T20 = InstanceType<typeof C> // C
type T21 = InstanceType<any> // any
type T22 = InstanceType<never> // any
type T23 = InstanceType<string> // Error
type T24 = InstanceType<Function> // Error
```
