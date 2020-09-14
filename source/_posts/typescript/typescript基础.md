---
title: typescript基础
category: typescript
tag: 编程语言
---

##### typescript 基本介绍?

1. 概念

typescript 是一种由微软开发的自由和开源的编程语言。它是 javascript 的一个超集，而且本质上向这个语言添加了可选的静态类型和基于类的面向对象编程。typescipt 是强类型语言，支持类型检测。

2. 安装(npm 方式)

```shell
npm i -g typescript
```

3. 创建 index.ts 文件

```typescript
function add(x: number, y: number) {
    return x + y
}
```

4. 编译文件

```shell
tsc index.ts
```

5. 编译后的文件

```javascript
function add(x, y) {
    return x + y
}
```

##### 基础语法

1. 类型注解

ts 的类型注解是一种轻量级约束，用于限定变量的类型或者函数返回值的类型。

```typescript
let name: string
let age: number = 24
function test(): Boolean {
    return false
}
```

2. typescript 基本类型

-   String 类型

```typescript
let str: string = 'acb'
```

-   Number 类型

```typescript
let n: number = 2
```

-   Boolean 类型

```typescript
let b: boolean = true
```

-   Array 类型

```typescript
let list: number[] = [1, 2, 3]
let arr: string[] = ['a', 'b', 'c']
//泛型写法
let list2: Array<number> = [1, 2, 3]
//多种类型，可以包含数字和字符元素的数据如下
let list3: (number | string)[] = [1, 'abc']
//任意类型
let list4: any[] = [1, 'a', true]
```

-   Any 类型

any 表示任意类型，可以直接看成是 js 中声明的类型了

```typescript
let a: any
//可以赋值成string
a = 'abc'
//可以赋值成number
a = 34
//可以赋值成数组
a = []
```

-   Enum 类型

使用枚举我们可以定义一些带名字的常量。 使用枚举可以清晰地表达意图或创建一组有区别的用例。

```typescript
//声明
enum Direction {
    UP,
    DOWN,
    LEFT,
    RIGHT
}
//访问
Direction.UP
```

默认情况下，UP 值为 0，其他成员会依次自增 1

不带初始化器的枚举或者被放在第一的位置，或者被放在使用了数字常量或其它常量初始化了的枚举后面。如下是不被允许的。

```typescript
function add(): number {
    return 0
}
enum Test {
    A = add(),
    B //error! 'A' is not constant-initialized, so 'B' needs an initializer
}
//需要手动指定值
enum Test {
    A = add(),
    B = 24
}
```

含字符串成员的枚举中不允许使用计算值，如下代码是不允许的。

```typescript
enum Test {
    A = add(),
    B = '23' //error 含字符串值成员的枚举中不允许使用计算值。
}
```

**字符串枚举**
在一个字符串枚举里，每个成员都必须用字符串字面量，或另外一个字符串枚举成员进行初始化。

```typescript
enum Direction {
    Up = 'UP',
    Down = 'DOWN',
    Left = 'LEFT',
    Right = 'RIGHT'
}
```

**异构枚举**
异构枚举是数值与字符串混合，一般来说不建议使用。

```typescript
enum Test {
    A,
    C,
    B = 'abc',
    D = 23,
    E
}
```

异构枚举中每一个自增成员必须在一个数值成员后面，如下代码是不行的。

```typescript
enum Test {
    A,
    C,
    B = 'abc',
    D //error :枚举成员必须有初始化表达式
}
```

**const 枚举**
大多数情况下，枚举是十分有效的方案。 然而在某些情况下需求很严格。 为了避免在额外生成的代码上的开销和额外的非直接的对枚举成员的访问，我们可以使用 const 枚举。 常量枚举通过在枚举上使用 const 修饰符来定义。

```typescript
const enum Enum {
    A = 1,
    B = A * 2
}
const enum Directions {
    Up,
    Down,
    Left,
    Right
}
```

-   Tuple 类型

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string 和 number 类型的元组。

```typescript
//声明一个元组
let tup: [number, string]
//赋值
tup = [23, 'abc']
//报错
tup = ['abc', 23]
```

-   Null 和 Undefined 类型

```typescript
let n: null = null
let u: undefined = undefined
```

_默认情况下 null 和 undefined 是所有类型的子类型。 就是说你可以把 null 和 undefined 赋值给 number 类型的变量。_

-   Unknown 类型

unknown 可以赋值任何类型

```typescript
let n: unknown
n = 'abc'
n = 23
n = {}
```

any 和 unknown 的最大区别是, unknown 是 top type (任何类型都是它的 subtype) , 而 any 即是 top type, 又是 bottom type (它是任何类型的 subtype ) ,这导致 any 基本上就是放弃了任何类型检查。

```typescript
let a: any = 'aaa'
a.test()
```

上面的代码编译时并不会检查，所以可以顺利通过编译，这样会产生一个运行时错误。

```typescript
let a: unknown = 'aaa'
a.test() //error 'a'上不存在属性test
```

-   Object 类型

object 类型表示非原始类型。

```typescript
let o: object
o = []
o = {}
o = new Date()
```

-   Never 类型

表示永不存在的类型，never 类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是 never 的子类型或可以赋值给 never 类型（除了 never 本身之外）。 即使 any 也不可以赋值给 never。

```typescript
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message)
}

// 推断的返回值类型为never
function fail() {
    return error('Something failed')
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {}
}
```

引用 vue 作者尤雨溪的一个[例子](https://www.zhihu.com/question/354601204/answer/888551021)

假设我们有一个联合类型

```typescript
interface Foo {
    type: 'foo'
}

interface Bar {
    type: 'bar'
}
type All = Foo | Bar
```

在 switch 当中判断 type，TS 是可以收窄类型的 (discriminated union)

```typescript
function handleValue(val: All) {
    switch (val.type) {
        case 'foo':
            // 这里 val 被收窄为 Foo
            break
        case 'bar':
            // val 在这里是 Bar
            break
        default:
            // val 在这里是 never
            const exhaustiveCheck: never = val
            break
    }
}
```

在 default 里面我们把被收窄为 never 的 val 赋值给一个显式声明为 never 的变量。如果一切逻辑正确，那么这里应该能够编译通过。但是假如后来有一天你的同事改了 All 的类型：

```typescript
type All = Foo | Bar | Baz
```

然而他忘记了在 handleValue 里面加上针对 Baz 的处理逻辑，这个时候在 default branch 里面 val 会被收窄为 Baz，导致无法赋值给 never，产生一个编译错误。所以通过这个办法，你可以确保 handleValue 总是穷尽 (exhaust) 了所有 All 的可能类型。

-   Void 类型

void 类型像是与 any 类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void

```typescript
function test(): void {
    console.log('hello world')
}
```
