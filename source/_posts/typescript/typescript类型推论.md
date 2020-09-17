---
title: typescript类型推论
category: typescript
tag: 编程语言
---

**介绍**

TypeScript 里的类型推论。即，类型是在哪里如何被推断的。
也就是你可以不给变量指定类型，typescript 同第一次给变量赋值来推导这个变量是什么类型。

```typescript
let x = 3
//x被推论成number类型
```

**最佳通用类型**

```typescript
let zoo = [new Rhino(), new Elephant(), new Snake()]
```

类型推断的结果为联合数组类型，(Rhino | Elephant | Snake)[]，并不会推论成 Animal 类型

**上下文类型**

TypeScript 类型推论也可能按照相反的方向进行。 这被叫做“按上下文归类”。按上下文归类会发生在表达式的类型与所处的位置相关时。

```typescript
window.onmousedown = function (mouseEvent) {
    console.log(mouseEvent.button) //<- Error
}
```

这个例子会得到一个类型错误，TypeScript 类型检查器使用 Window.onmousedown 函数的类型来推断右边函数表达式的类型。 因此，就能推断出 mouseEvent 参数的类型了。 如果函数表达式不是在上下文类型的位置， mouseEvent 参数的类型需要指定为 any，这样也不会报错了。

```typescript
window.onmousedown = function (mouseEvent: any) {
    console.log(mouseEvent.button) //<- Now, no error is given
}
```
