---
title: typescript模块与命名空间
category: typescript
tag: 编程语言
---

##### 介绍

在`typescript1.5`以后，术语名发生了变化，"内部模块"现在称为命名空间，"外部模块"为模块，与 ES6 中的模块概念相同。
也就是说以前`module X{}`语法现在应该改为`namespace X{}`，当然`module X{}`目前也能识别，不建议使用，说不定哪个版本就会移除。

##### 模块

与 ES2015 模块概念相似，我们可以将一个文件看成是模块。模块在自身作用域里执行，这意味着定义在一个模块里的变量，函数，类等等在模块外部是不可见的，除非你明确地使用 export 形式之一导出它们。 相反，如果想使用其它模块导出的变量，函数，类，接口等的时候，你必须要导入它们，可以使用 import 形式之一。

-   **全局模块**

默认情况下，一个 typescript 文件如果不包含顶级`import`或`export`，可以看成是全局模块，在全局模块里声明的变量在全局作用域可以访问。

如在`src/foo.ts`文件里有如下代码

```typescript
const foo: string = 'abc'
```

在`src/bar.ts`里可以访问`foo`变量

```typescript
const bar: string = foo
```

这样容易造成全局环境污染，所以不建议这么做。

-   **文件模块**

任何包含顶级`import`或`export`关键字的 ts 文件，都可以看成是一个文件模块，文件模块会在当前文件中创建一个本地作用域。

将上面的例子改写如下

```typescript
//src/foo.ts
export const foo: number = 'acb'
```

```typescript
//src/bar.ts
const bar: number = foo //error:早不到变量foo
```

这时我们必须要导入才能使用

```typescript
//src/bar.ts
import {} from './foo.ts'
const bar: number = foo
```

-   **模块系统类型**

1. ES 模块语法

> -   使用 export 关键字导出一个变量或类型
>
> ```typescript
> export const someVar = 123
> export type someType = {
>     foo: string
> }
> ```
>
> -   另一种写法
>
> ```typescript
> const foo = 123
> function test() {}
> export { foo, test }
> ```
>
> -   重命名的方式导出
>
> ```typescript
> const foo = 123
> export { foo as bar }
> ```
>
> -   使用`import`导入变量或类型
>
> ```typescript
> import { someVar, someType } from './foo'
> ```
>
> -   通过重命名的方式导入变量或者类型
>
> ```typescript
> import { someVar as aDifferentName } from './foo'
> ```
>
> -   导入所有并赋值在一个对象上
>
> ```typescript
> import * as foo from './foo'
> ```
>
> -   副作用导入
>
> ```typescript
> import 'core-js'
> ```
>
> -   从其他模块导入后整体导出
>
> ```typescript
> export * from './foo.ts'
> ```
>
> -   从其他模块导入部分并导出
>
> ```typescript
> export { foo } from './foo.ts'
> ```
>
> -   通过重命名，部分导出从另一个模块导入的项目
>
> ```typescript
> export { someVar as aDifferentName } from './foo'
> ```
>
> -   默认导出，导入
>
> ```typescript
> //导出变量
> export default someVar = 123
> //导出函数
> export default function someFunction() {}
> //导出类
> export default class someClass {}
> ```
>
> 导入使用 `import x from 'xx'` 语法
>
> ```typescript
> import foo from './foo.ts'
> ```

2. CommonJS 与 AMD 语法

CommonJS 和 AMD 的环境里都有一个 exports 变量，这个变量包含了一个模块的所有导出内容。CommonJS 和 AMD 的 exports 都可以被赋值为一个对象, 这种情况下其作用就类似于 es6 语法里的默认导出，即 export default 语法了。虽然作用相似，但是 export default 语法并不能兼容 CommonJS 和 AMD 的 exports。

使用`export =`和`import = require()`

> -   导出
>
> ```typescript
> class Person {
>     name: string
>     age: number
>     say() {
>         console.log(`I am is ${this.name}`)
>     }
> }
> export = Person
> ```
>
> -   导入
>
> ```typescript
> import Person = require('./foo.ts')
> ```

##### 命名空间

在模块化概念出来之前，我们为了避免全局变量污染，都会采用命名空间的方式来避免。
js 中所谓的命名空间就是将变量挂在一个命名的对象上。
比如 A 和 B 合作开发一个项目，它们都定义了一个`add`方法，因为名字相同，所以有一个会被覆盖，为了解决这个问题。

```javascript
//A的方法
function add(x, y) {
    return x + y
}
//B的方法
function add(x, y) {
    return 2 * x + y
}
```

B 的`add`会覆盖 A 的，为了解决这个问题，它们决定把各自的方法放到一个对象里

```javascript
//A的方法
const UtilA = {
    add(x, y) {
        return x + y
    }
}
//B的方法
const UtilB = {
    add(x, y) {
        return 2 * x + y
    }
}
```

这样它们的方法就不会相互影响`UtilA`与`UtilB`就是命名空间。

typescript 命名空间

```typescript
namespace A {
    export function add() {}
}
namespace B {
    export function add() {}
}
```

转换成 js 后

```javascript
var A
;(function (A) {
    function add() {}
    A.add = add
})(A || (A = {}))
var B
;(function (B) {
    function add() {}
    B.add = add
})(B || (B = {}))
export {}
```

**嵌套命名空间**

```typescript
namespace A {
    export function add() {}
    export namespace B {
        export function add() {}
    }
}
```

**别名**

通过`import q = x.y.z`来给命名空间取别名

```typescript
namespace Shapes {
    export namespace Polygons {
        export class Triangle {}
        export class Square {}
    }
}

import polygons = Shapes.Polygons
let sq = new polygons.Square()
```
