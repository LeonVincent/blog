### async怎么用
async函数返回一个Promise对象，可以使用then方法添加回调函数。当函数执行的时候，一旦遇到await就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

举一个栗子：
```
let timeout = ms => {
    console.log('a')
    return new Promise(resolve => {
        console.log('b')
        setTimeout(resolve, ms)
    })
}

let asyncPrint = async (value, ms) => {
    await timeout(ms)
    console.log(value)
    return `${value} world`
}
asyncPrint('hello', 500).then(_ => {
    console.log(_)
})//先依次输出 a b 然后500毫秒后输出hello 和 hello world
```

async 函数有多种形式

```
// 函数声明
async function foo() {}

// 函数表达式
const foo = async function () {};

// 对象的方法
let obj = { async foo() {} };
obj.foo().then(...)

// Class 的方法
class Storage {
  constructor() {
    this.cachePromise = caches.open('avatars');
  }

  async getAvatar(name) {
    const cache = await this.cachePromise;
    return cache.match(`/avatars/${name}.jpg`);
  }
}

const storage = new Storage();
storage.getAvatar('jake').then(…);

// 箭头函数
const foo = async () => {};
```

注意⚠️：
- async函数返回的是promise对象，所以可以用then接受函数内部return的数据
- await命令后面一般跟着Promise对象，会等着返回Promise的结果，然后执行后面的。如果不是Promise对象，比如是123,这时等于 return 123。
- await命令目前只允许存在与async函数里面

好了，如果你只是想了解async函数和await怎么用的，到这里就可以了。

=======================================
我是分割线

那么
### async到底是什么

一句话，它就是Generator函数的语法糖。

而Generator的实现不得不依赖于Iterator接口。

而Iterator的实现思想又来源于单向链表

所以我们先来说说单向链表

### 单向链表

> wiki：链表（Linked list）是一种常见的基础数据结构，是一种线性表，但是并不会按线性的顺序储存数据，而是在每一个节点里存到下一个节点的指针（Pointer）。由于不必须按顺序储存，链表在插入的时候可以达到 o(1)的复杂度，比另一种线性表顺序表快得多，但是查找一个节点或者访问特定编号的节点则需要 o(n)的时间，而顺序表响应的时间复杂度分别是 o(logn)和 o(1)。

所以单向链表的优点就是：
- 无需预先分配内存
- 插入/删除节点不影响其他节点，效率高（典型的栗子：dom操作）

简单来说，单向链表是链表中最简单的一种，它包含两个域，一个信息域，一个指针域。这个链接指向列表中的下一个节点，而最后一个节点则指向null

![image](https://user-images.githubusercontent.com/22312092/61137396-00932a00-a4f8-11e9-8ba9-7f89104d9959.png)

单向链表的实现可以看这篇文章：
[es6实现单向链表](https://www.cnblogs.com/xiaosongJiang/p/10878718.html)

### Iterator(遍历器)

Iterator是一种接口，为各种不同的数据结构（原生支持Array Object Map Set）提供统一的访问机制。任何部署了Iterator接口的数据结构都可以完成遍历操作。

Iterator的作用：
- 为各种数据结构提供一个统一的、简便的访问接口
- 使数据结构的成员能够按某种次序排列
