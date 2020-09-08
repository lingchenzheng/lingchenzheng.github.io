---
title: $attrs与$listeners
category: vue
tag: 框架
date: 2019/6/3
---

1. \$attrs

官网说明：

> 包含了父作用域中不作为 prop 被识别 (且获取) 的 attribute 绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="\$attrs" 传入内部组件——在创建高级别的组件时非常有用。

意思是，我们在父组件给子组件上绑定的属性(class 和 style 除外)，如果在子组件内部没有声明 props，则在子组件可以通过\$attrs 访问这些属性，以键值对存在。

```html
<!-- 父组件 -->
<template>
    <div>
        <Son :age="age"></Son>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                age: 23
            }
        }
    }
</script>

<!-- 子组件 -->
<template>
    <div>{{$attrs.age}}</div>
</template>
```

age 并未在 props 中声明，但我们可以通过\$attrs 访问 age 属性。

创建高级别组件

```html
<!-- 父组件 -->
<template>
    <div>
        <Son :age="age"></Son>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                age: 23
            }
        }
    }
</script>

<!-- Son组件 -->
<template>
    <div>
        <Child v-bind="$attrs" />
    </div>
</template>

<!-- Child组件 -->
<template>
    <div>{{$attrs.age}}</div>
</template>
```

我们可以通过 v-bind 来进行深层级组件绑定。

2. \$listeners

与$attrs相似，$listeners 包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="\$listeners" 传入内部组件——在创建更高层次的组件时非常有用。

```html
<!-- 父组件 -->
<template>
    <div>
        <Son @add="add"></Son>
    </div>
</template>
<script>
    export default {
        methdos: {
            add() {}
        }
    }
</script>

<!-- Son组件 -->
<template>
    <div>
        <Child v-on="$listeners" />
    </div>
</template>

<!-- Child组件 -->
<template>
    <div>
        <button @click="add">click</button>
    </div>
</template>
<script>
    export default {
        methods: {
            add() {
                this.$emit('add')
            }
        }
    }
</script>
```

$attrs与$listeners 在创建高级组件的时候很有用。
