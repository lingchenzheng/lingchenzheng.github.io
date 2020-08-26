---
title: IndexedDB基础知识
category: web
date: 2020/8/25
---

#### 什么是 IndexedDB?

IndexedDB 是一种浏览器存储技术，是浏览器提供的一种大数量存储方案。相当于一个本地数据库，可以被网络脚本操作，同时可以提供索引和搜索。

---

#### 特点

-   异步，操作时不会阻塞浏览器主线程，与 localStorage 相反。
-   支持事务，事务具有原子性，也就是说一个操作，要么成功，要么失败，不会只进行一半。
-   同源策略，网页只能访问自身域名下的数据库，不能跨域访问。
-   存储空间大，一般不会小于 250MB。
-   支持二进制存储，可以存储 ArrayBuffer 和 Blob 对象。

---

#### IndexedDB 术语

1. Database(数据库)
   数据库是 IndexedDB 里最高级别对象，它包含了所有的对象仓库，同时包含了所有持久化数据。你可以通过不同的名字创建多个数据库，但是通常来说，每个应用只会创建一个数据库。

2. Object store(对象仓库)
   对象仓库就像一个存储数据的桶，你可以把它当成关系型数据库里的表。通常，会为每种类型的数据创建一个对象仓库，比如(用户仓库，文章仓库) 。

3. Index(索引)
   索引是用来在数据库检索数据的，我们可以为不同的属性建立索引，从而加快检索。比如有存储文章的仓库，里面的属性有标题，创建日期，文章内容，作者。我们可以通过其中一个属性来检索整条记录。

4. Transaction(事务)
   事务包裹着一个或者一组操作，IndexedDB 所有的操作必须在事务中进行，这样可以保证数据库的完整性，操作要么成功，要么失败，失败了就回滚。

5. Cursor(指针)
   用于遍历或迭代数据库中的多条记录。

---

#### 检查是浏览器否支持

```javascript
let isSupported = 'indexedDB' in window
```

---

#### 基本操作

##### 打开(创建)数据库(database)

浏览器器提供了全局对象 indexedDB，它是 IDBFactory 接口的一个实例化对象，由浏览器自己提供，全局就一个，我们不能通过`new IDCFactory()`自己来实例化，因此可以看出这是一个单例模式。

IDBFactory 提供了一些方法

-   open 打开(或创建)数据库
    open 接受两个参数，第一是数据库名，是一个字符串，第二个参数是数据库版本号，是一个数字类型，默认为 1，可以不传。

    ```javascript
    let idr = window.indexedDB.open('test', 1)
    ```

-   deleteDatabase 删除数据库
    接受一个参数，要删除的数据库名。

    ```javascript
    window.indexedDB.deleteDatabase('test')
    ```

-   databases 获取当前域下所有数据库集合，返回值是一个 promise 对象

    ```javascript
    window.indexedDB.databases().then(res => {
        console.log(res)
    })
    ```

-   cmp 比较两个值

    ```javascript
    let result = indexedDB.cmp(a, b)
    ```

    | 返回值 | 描述      |
    | ------ | --------- |
    | -1     | a 比 b 小 |
    | 0      | a 等于 b  |
    | 1      | a 大于 b  |

调用 open 方法后返回一个 IDBRequest 对象，IDBRequest 接口提供了根据绑定事件处理函数访问结果集的方法。其中结果集来自对数据库和数据库对象发起的异步查询。
_注意：不仅仅是打开数据库会返回 IDBRequest 对象，创建仓库，索引，查询结果也会返回 IDBRequest 对象，它是一个结果集_

打开数据库 IDBRequest 对象有三个事件

-   success 事件
    数据库打开或创建成功时触发
-   error 事件
    数据库打开或创建失败是触发
-   upgradeneeded 事件
    数据库版本升级时触发

```javascript
let idr = indexedDB.open('test')

idr.onsuccess = function (e) {}

idr.onerror = function (err) {}

idr.onupgradeneeded = function (e) {}

//idr.addEventListener(eventname,handler)
//也可以使用addEventListener添加事件监听器
```

我们可以通过`idr`对象的 result 属性拿到结果集，或者通过回调函数的事件对象`e.target.result`

```javascript
let idr = indexedDB.open('test')
idr.onsuccess = function (e) {
    //idr.result
    //e.target.result
}
```

open 方法返回的结果集中，我们可以拿到一个 IDBDatabase 对象，这个对象提供一个数据库连接，通过连接可以操作数据库。

IDBDatabase 属性和方法：

-   属性
    -   name
        返回数据库的名字
    -   version
        数据库版本
    -   objectStoreNames
        当前连接数据库中所有仓库名字的列表
-   方法
    -   close
        关闭数据库连接并立即返回
    -   createObjectStore
        创建并返回一个新的 object store 或 index
        _注意：此方法只能在 versionchange 中使用，数据库结构发生变化，比如 upgradeneeded 事件。所以创建仓库和索引通常在 upgradeneeded 实现_
    -   deleteObjectStore
        根据传入的名字删除仓库或索引
    -   transaction
        返回一个事务对象，这个对象用来操作仓库，实现增删改查。

##### 创建仓库(object store)

IDBDatabase.createObjectStore 方法接受两个参数，第一个为仓库名(可以看成是关系型数据库中的表明)，第二个是一个可选参数对象。

**语法**
`IDBDatabase.createObjectStore(name)`
`IDBDatabase.createObjectStore(name, options)`

**参数说明**

1.  name 仓库名
2.  options
    -   keyPath 可以通过 keyPath 指定一个主键，如果为空或未指定，object store 创建时将没有 key path，而是使用 out-of-line keys 。你也能传一个数组作为 keyPath 。
    -   autoIncrement 如果为 true, object store 有一个 key generator. 默认为 false。

_说明：keyPath 为指定主键，如果指定了，则主键为内建，也就是说主键是属性中的一员，这个时候如果 autoIncrement 是 false，则添加数据时必须传入主键。如果没有指定 keyPath，则默认使用外键，autoIncrement 为 true 的时候，外键自增长。_

```javascript
//在upgradeneeded事件里创建一个名为user的仓库，主键为id
let request = indexedDB.open('test', 1)
request.onupgradeneeded = function (e) {
    let db = this.result
    let store = db.createObjectStore('user', {
        keyPath: 'id'
    })
}
```

##### 创建索引(index)

索引是用来对数据库记录进行检索的，说白了就是查询的时候将是索引的键的值考虑进去。主键默认会被查询。

IDBObjectStore 实例表示一个仓库对象，`IDBDatabase.createObjectStore`和`IDBTransaction.objectStore`方法会返回一个其实例对象。

IDBObjectStore 方法：

-   方法

    1.  add(value,key)
        add 方法接受两个参数，返回一个 IDBRequest 对象
        add 只能插入数据，如果键是指定的记录的一个属性，当这条记录存在时，插入时会报错。
        value：插入的值
        key：键，如果不指定，则为 null

    2.  clear
        创建并立即返回一个 IDBRequest 对象，clear 用来删除仓库里所有的记录。

    3.  count(key)
        创建并立即返回一个 IDBRequest 对象，查找给定条件记录的条数。

    4.  createIndex(name,keyPath,options)
        该方法用来创建索引，只能在 versionchange 事件回调中使用。
        name：索引名称
        keyPath：索引所在的属性
        options：配置对象

        > unique：布尔值，如果为 true，则表示这条记录是唯一的，这个属性在记录中不能有重复的(举个例子，假如仓库中有个 name 值为张三的记录，如果 name 设置为索引，并且 unique 为 true，则不能插入 name 为张三的新记录，即使其他属性值不一样。)
        > multiEntry：如果为 true，则当 keypath 解析为数组时，索引将为每个数组元素在索引中添加一个条目。如果为 false，它将添加一个包含数组的条目。

    5.  delete(key)
        返回一个 IDBRequest 对象，通过指定的 key 删除一条数据。

    6.  deleteIndex(indexName)
        根据传入的索引名删除索引。

    7.  get(key)
        获取数据，参数为主键的值。

    8.  index(name)
        打开索引，参数为索引名，返回一个 IDBIdex 对象
        索引的意义在于可以检索任意字段，具体稍后(IDBIndex 对象部分)再介绍。

    9.  openCursor(range,direction)
        打开一个指针，返回一个 IDBCursor 对象。
        指定是用来遍历仓库记录的，具体稍后(IDBCursor 对象部分)再介绍。
        range 与 direction 参数是可选的，range 表示遍历的区间，direction 表示遍历的方向。

    10. put(value,key)
        添加数据，与 add 的区别是，如果添加的记录 key 已在仓库中存在，则会覆盖，不存在则新建一条记录。

这里主要讲索引(index)的创建，下面是一个创建索引的 demo

```javascript
let request = indexedDB.open('test', 1)
request.onupgradeneeded = function (e) {
    let db = this.result
    let store = db.createObjectStore('user', {
        autoIncrement: true
    })
    //创建name索引
    store.createIndex('name', 'name', {
        unique: true
    })
    //创建age索引
    store.createIndex('age', 'age', {
        unique: false
    })
}
```

##### 创建事务对象(transaction)

IDBTransaction 接口定义了异步事务使用数据库中的事件对象属性。IndexedDB 的所有读写操作都应该在一个事务中完成。

IDBTransaction 实例由 IDBDatabase 的 transaction 方法创建。

IDBTransaction 属性和方法：

-   属性

    -   db
        当前事务所连的数据库
    -   error
        事务发生错误后产生的错误类型，如果事务未完成，则 error 的值为 null。
    -   mode
        事务的模式，比如读写权限，默认是 readonly
    -   objectStoreNames
        返回当前事务连接的仓库列表名

-   方法

    -   abort
        放弃本次连接的 transaction 的所有修改，如果当前的 transaction 处于回滚完毕或完成状态，则会抛出一个错误事件。
    -   objectStore(storeName)
        返回当前事务作用域的仓库对象。

```javascript
let request = indexedDB.open('test', 1)
request.onsuccess = function (e) {
    db = request.result
    //获取事务对象
    let transaction = db.transaction('user', 'readwrite')
    //获取指定作用域仓库对象
    let store = transaction.objectStore('user')
    //插入数据
    store.put({ name: '张三', age: 26 })
}
```

##### 索引对象(IDBIndex)

IDBIndex 接口提供了异步获取数据库中一个 index 的功能。index 是一种用于在另一个 object store 中查找记录的 object store，其被称为被引用的 object store。你可以通过使用该接口来取回数据。

**获取索引**

```javascript
let request = indexedDB.open('test', 1)
request.onsuccess = function (e) {
    db = request.result
    //获取事务对象
    let transaction = db.transaction('user', 'readwrite')
    //获取指定作用域仓库对象
    let store = transaction.objectStore('user')
    //获取索引名为name的索引
    let nameIndex = store.index('name')
    //通过nameIndex索引检索记录，返回IDBRequest对象
    let req = nameIndex.get('李四')
    //获取查询结果
    req.onsuccess = function (e) {
        let { result } = e.target
    }
}
```

IDBIndex 的常用方法和属性

-   属性

    -   name
        索引名。
    -   objectStore
        索引关联的仓库。
    -   keyPath
        索引关联的属性
    -   unique
        索引关联的属性是否唯一。
    -   multiEntry
        影响索引的键路径的求值结果生成数组时索引的行为方式。如果为 true，则索引中对于键数组中的每个项都有一条记录。如果为 false，则数组中的每个键都有一条记录。

-   方法

    -   get
        根据传入的值来检索结果，如果有多条的话，只返回第一条。
    -   count
        根据传入的值来检索结果，返回结果的条数。
    -   getKey
        根据传入的值来检索结果，返回第一条结果的键值。
    -   openCursor
        创建索引的指针，用来遍历指定范围的结果集。
    -   getAll(非标准方法)
        根据传入的值来检索结果，返回所有结果。
    -   getAllkeys(非标准方法)  
        根据传入的值来检索结果，返回所有键的值。
    -   openKeyCursor(非标准方法)
        创建索引键的指针，用来遍历指定范围的结果集，用来遍历键的。

下面以简单例子来说明

有如下数据库，`name`与`age`字段都建立了索引

![data](/images/web/IndexedDB存储/1.png)

```javascript
let request = indexedDB.open('test', 1)
request.onsuccess = function (e) {
    db = request.result
    //获取事务对象
    let transaction = db.transaction('user', 'readwrite')
    //获取指定作用域仓库对象
    let store = transaction.objectStore('user')

    //获取索引
    let nameIndex = store.index('name')
    //get
    nameIndex.get('张三').onsuccess = function (e) {
        console.log(e.target.result)
        //{name: "张三", age: 25}
    }
    //count
    nameIndex.count('张三').onsuccess = function (e) {
        console.log(e.target.result)
        //返回2，名字为张三的数据有两条
    }
    //getKey
    nameIndex.getKey('张三').onsuccess = function (e) {
        console.log(e.target.result)
        //返回1，第一个为张三的记录键为1
    }
    //openCursor
    nameIndex.openCursor('张三').onsuccess = function (e) {
        let cursor = e.target.result
        //返回一个IDBCursor对象，范围为name值为张三的记录，具体遍历稍后介绍
    }
    //getAll
    nameIndex.getAll('张三').onsuccess = function (e) {
        console.log(e.target.result)
        //[{name: "张三", age: 25},{name: "张三", age: 26}]
    }
    //getAllkeys
    nameIndex.getAllKeys('张三').onsuccess = function (e) {
        console.log(e.target.result)
        //返回[1,2]，第name为张三的记录的键
    }
}
```

##### 指针(IDBCursor)

IDBCursor 像一个指针，指向仓库中某条记录的位置，用来对指定范围的记录进行迭代遍历。

从上面的介绍，我们可以知道，有两种方式获取 IDBCursor 对象。

-   IDBObjectStore 接口的 openCursor 方法
-   IDBIndex 接口的 openCursor 和 openKeyCursor 方法

_说明：IDBObjectStore 创建的是 Key 迭代的指针，IDBIndex 创建的是索引(某个属性)迭代的指针_

IDBCursor 常用属性和方法：

-   属性
    -   key
        返回记录中指针中有效的主键。
    -   source
        返回指针所关联的对象，IDBObjectStore 或 IDBIndex 对象。
    -   direction
        返回指针迭代的方向。
    -   primaryKey
        返回指针当前有效主键。
    -   value
        返回指针所指向的记录数据。

_注意：key 与 primaryKey，key 表示当前迭代中的键，比如 name 属性创建的索引进行遍历，key 为 name 的值，而 primaryKey 是这条记录的 Key 的值，就是创建仓库时所指定的 keyPath 键的值。_

-   方法
    -   advance
        设置指针向下移动位置的次数。
    -   continue
        设置指针移动到下一个位置。
    -   delete
        返回一个 IDBRequest 对象，并且在一个单独的线程中，删除游标位置记录，而不改变游标的位置。这个可以用作删除一些特定的记录。
    -   update
        返回一个 IDBRequest 对象，并且在一个单独的线程中，更新对象存储中当前游标位置的值。这个可以用来更新特定的记录。

```javascript
let request = indexedDB.open('test', 1)
request.onsuccess = function (e) {
    db = request.result
    //获取事务对象
    let transaction = db.transaction('user', 'readwrite')
    //获取指定作用域仓库对象
    let store = transaction.objectStore('user')
    //获取索引对象
    let nameIndex = store.index('name')
    //获取索引关联的指针
    nameIndex.openCursor().onsuccess = function (e) {
        let cursor = e.target.result
        if (cursor) {
            console.log(cursor.value)
            //指针向下移
            cursor.continue()
        } else {
            console.log('没有更多数据')
        }
    }
}
```

结果如下：

![指针](/images/web/IndexedDB存储/2.png)

_注意：结果排序，是按照索引关联的属性值的第一个字符的 Unicode 编码从小到大的，比如上面的 name 索引，则以姓名的第一个字排序，‘张’的 Unicode 编码为 24352，‘李’为 26446，所以所有张三排在李四前面。_

**将`age=28`的记录删除**

```javascript
let request = indexedDB.open('test', 1)
request.onsuccess = function (e) {
    db = request.result
    //获取事务对象
    let transaction = db.transaction('user', 'readwrite')
    //获取指定作用域仓库对象
    let store = transaction.objectStore('user')
    //获取索引对象
    let nameIndex = store.index('name')
    //获取索引关联的指针
    nameIndex.openCursor().onsuccess = function (e) {
        let cursor = e.target.result
        if (cursor) {
            console.log(cursor.value)
            //指针向下移
            if (cursor.value.age === 28) {
                cursor.delete()
            }
            cursor.continue()
        } else {
            console.log('没有更多数据')
        }
    }
}
```

**将`age=28`的记录名字改成“王五”**

```javascript
let request = indexedDB.open('test', 1)
request.onsuccess = function (e) {
    db = request.result
    //获取事务对象
    let transaction = db.transaction('user', 'readwrite')
    //获取指定作用域仓库对象
    let store = transaction.objectStore('user')
    //获取索引对象
    let nameIndex = store.index('name')
    //获取索引关联的指针
    nameIndex.openCursor().onsuccess = function (e) {
        let cursor = e.target.result
        if (cursor) {
            console.log(cursor.value)
            let user = cursor.value
            if (user.age === 28) {
                user.name = '王五'
                cursor.update(user)
            }
            //指针向下移
            cursor.continue()
        } else {
            console.log('没有更多数据')
        }
    }
}
```

advance 方法不多做说明，与 continue 方法类似，只是它可以设置移动的次数。

**openCursor 方法的参数**

上面的例子中，我们 openCursor 都没有带参数，表示的所有记录按返回顺序迭代。但是有时候我们并不需要遍历所有记录，遍历顺序也可能改变，而且要过滤重复记录。
openCursro 为我们提供了两个参数，来设置指针的遍历范围和方向。

1. 第一个参数 IDBKeyRange 对象或者指定值，null，如果是 null 表示匹配所有，如果是指定值则表示全等匹配。

比如，我们要遍历名字为“张三”的记录

```javascript
//获取索引关联的指针
nameIndex.openCursor('张三').onsuccess = function (e) {
    let cursor = e.target.result
    if (cursor) {
        console.log(cursor.value)
        //指针向下移
        cursor.continue()
    } else {
        console.log('没有更多数据')
    }
}
```

![范围遍历](/images/web/IndexedDB存储/3.png)

**IDBKeyRange 对象**
IDBKeyRange 接口为我们提供了一系列的静态方法来创建 IDBKeyRange 对象

| 方法                               | 描述                             |
| ---------------------------------- | -------------------------------- |
| IDBKeyRange.upperBound(x)          | key 小于等于 x 的记录            |
| IDBKeyRange.upperBound(x,true)     | key 小于 x 记录                  |
| IDBKeyRange.lowerBound(x)          | key 大于等于 x 记录              |
| IDBKeyRange.lowerBound(x,true)     | key 大于 x 记录                  |
| IDBKeyRange.bound(x, y)            | key 大于等于 x 小于等于 y 的记录 |
| IDBKeyRange.bound(x, y,true,true)  | key 大于 x 小于 y 的记录         |
| IDBKeyRange.bound(x, y,true,false) | key 大于 x 小于等于 y 的记录     |
| IDBKeyRange.bound(x, y,false,true) | key 大于等于 x 小于 y 的记录     |
| IDBKeyRange.only(z)                | key = z 的记录                   |

_如过 key 是非数字，则比较其 unicode 编码_

下面举一个例子，遍历年龄大于等于 25，小于等于 26 的记录

```javascript
//获取索引对象
let ageIndex = store.index('age')
//指定范围
let range = IDBKeyRange.bound(25, 26)
//获取索引关联的指针
ageIndex.openCursor(range).onsuccess = function (e) {
    let cursor = e.target.result
    if (cursor) {
        console.log(cursor.value)
        //指针向下移
        cursor.continue()
    } else {
        console.log('没有更多数据')
    }
}
```

结果如下
![范围遍历](/images/web/IndexedDB存储/4.png)

_x 的值必须小于 y 的值，否则报错，x,y,z 不仅仅是数字，也可以是字符_

2. 第二个参数表示遍历的方向，有四个值

| 值         | 说明                                            |
| ---------- | ----------------------------------------------- |
| next       | 默认值，允许重复，查询结果 IDBKeyRange 递增顺序 |
| nextunique | 允许重复，查询结果 IDBKeyRange 递增顺序         |
| prev       | 允许重复，查询结果 IDBKeyRange 递减顺序         |
| prevunique | 允许重复，查询结果 IDBKeyRange 递减顺序         |

例子：以 name 为索引，获取名字为张三，并且按递减顺序遍历

```javascript
//获取指定作用域仓库对象
let store = transaction.objectStore('user')
//获取索引对象
let nameIndex = store.index('name')
//获取索引关联的指针
let range = IDBKeyRange.only('张三')
nameIndex.openCursor(range, 'prev').onsuccess = function (e) {
    let cursor = e.target.result
    if (cursor) {
        console.log(cursor.value)
        //指针向下移
        cursor.continue()
    } else {
        console.log('没有更多数据')
    }
}
```

#### 总结

-   IndexedDB 相当于浏览器的一个内置数据库，给我们提供了较大的存储空间，相比较与 cookie 与 storage，它的空间大的多，而且存储数据更加多样性，支持数据搜索，而且它是采用异步的，不会阻塞主线程。

-   浏览器给我们提供了一系列操作 IndexedDB 的接口，比如 IDBFactory,IDBDatabase,IDBIndex,IDBObjectStore 等等，我们并不能直接使用这些接口或者说是实例化它，而是通过浏览器提供的对象来调用方法得到相关实例化对象。

-   因为 IndexedDB 采用的是异步设计方式，所以它的大部分操作都是返回一个类似与 Promise 对象的结果集对象，这个对象是 IDBRequest 接口的实例，它有三种不同状态`pending`,`done`,`error`状态。比如，打开数据，插入数据，插叙数据，创建遍历器等，都会返回一个 IDBRequest 对象。
