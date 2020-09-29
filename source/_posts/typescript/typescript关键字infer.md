---
title: typescript关键字infer
category: typescript
tag: 编程语言
---

##### 介绍

`infer`表示在`extends`条件语句中等待推断的类型

例如：获取函数参数类型

```typescript
type ParamType<T extends () => any> = T extends (param: infer P) => any
    ? P
    : any
function test(name: string): any {}

type Result = ParamType<test> // string
```

解释：

> 在这里，第一个`extends`表示类型约束，表示传入的 T 必须是函数类型，第二个`extends`用于条件判断，
> `T extends (param: infer P) => any`表示如果`T`能够赋值给`(param: infer P) => any`类型，则结果是传的函数参数类型`P`，则返回其参数类型，否则为`any`类型

##### 几个使用 infer 的内置类型

1. 获取函数返回值类型

```typescript
type ReturnType<T extends (...args: any[]) => any> = T extends (
    ...args: any[]
) => infer P
    ? P
    : never
```

2. 获取实例类型

```typescript
type InstanceType<T extends new (...args: any[]) => any> = T extends new (
    ...args: any[]
) => infer U
    ? U
    : never
```

3. 获取构造函数参数类型

```typescript
type ConstructorType<T extends new (...args: any[]) => any> = T extends new (
    ...args: infer P
) => any
    ? P
    : never
```

##### 练习

1. 类型过滤

```typescript
//挑选如下结构的函数类型

interface Module {
    count: number
    name: string
    say(word: string): any
}

type FilterFunc<T> = Pick<
    T,
    { [P in keyof T]: T[P] extends Function ? P : never }[keyof T]
>

type Module2 = FilterFunc<Module> // {say(word: string): any}
```

2. `tuple`转`union`类型

```typescript
type Tuples = [string, number]
type ElementOf<T> = T extends Array<infer E> ? E : never
type ToUnion = ElementOf<Tuples>
```
