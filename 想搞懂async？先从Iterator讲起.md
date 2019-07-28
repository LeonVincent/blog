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

###### Iterator的作用：
- 为各种数据结构提供一个统一的、简便的访问接口
- 使数据结构的成员能够按某种次序排列
- 依靠Iterator，可以实现for...of循环


###### Iterator的遍历过程
- 创建一个指针对象，指向当前数据结构的起始位置。也就是说，遍历器对象本质上，就是一个指针对象。
- 第一次调用指针对象的next方法，可以将指针指向数据结构的第一个成员。
- 第二次调用指针对象的next方法，指针就指向数据结构的第二个成员。
- 不断调用指针对象的next方法，直到它指向数据结构的结束位置。


每一次调用next方法，都会返回数据结构的当前成员的信息。具体来说，就是返回一个包含value和done两个属性的对象。其中，value属性是当前成员的值，done属性是一个布尔值，表示遍历是否结束。

根据以上的规则，我们来简单实现一个next方法

```
let makeIterator = array => {
    let nextIndex = 0;
    return {
        next: _ => nextIndex < array.length ?
        { value: array[nextIndex++], done: false }:
        { value: undefined, done: true }
    }//返回的是一个指针对象
}

let it = makeIterator(['a','b','c','d'])
it.next()
it.next()
it.next()
it.next()
it.next()
it.next()
//next方法用来移动指针，第一次调用会指向a，并且返回一个对象，表示当前数据成员的信息，这个对象具有value和done两个属性，第二次调用指向b。。。
```

如果使用 TypeScript 的写法，遍历器接口（Iterable）、指针对象（Iterator）和next方法返回值的规格可以描述如下。

```
interface Iterable {
  [Symbol.iterator]() : Iterator,
}

interface Iterator {
  next(value?: any) : IterationResult,
}

interface IterationResult {
  value: any,
  done: boolean,
}
```
###### 接口部署
ES6规定，默认的Iterator接口部署在数据结构的Symbol.iterator属性上（[symbol是什么](http://es6.ruanyifeng.com/#docs/symbol)），就是说，一个数据结构只要具有Symbol.iterator属性，就可以认为是可遍历的（Iterable）。

再举个栗子：
```
let arr = ['a','b','c']
let iter = arr[Symbol.iterator]()
iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```

那么原生有哪些具备Iterator接口的数据结构呢
- Array
- Map
- Set
- String
- arguments对象
- NodeList 对象


##### 调用Iterator接口的场合
- 解构赋值
- 扩张运算符
- Array.form()
- Map(), Set()（比如new Map([['a',1],['b',2]])）
- Promise.all()
- Promise.race()
- ==for...of==
```
const arr = ['red', 'green', 'blue'];

for(let v of arr) {
  console.log(v); // red green blue
}

const obj = {};
obj[Symbol.iterator] = arr[Symbol.iterator].bind(arr);

for(let v of obj) {
  console.log(v); // red green blue
} 
```
- ==Generator函数==

==========================================我是分割线

### 什么是Generator函数

形式上，Generator函数有两个特征。
- function关键字与函数名之间有一个*号
- 函数体内部使用yield表达式，定义不同的内部状态

```
function* myGenerator() {
    yield 'hi'
    yield 'lueluelue'
    return 'byebye'
}
let myG = myGenerator()
myG.next() //{value: "hi", done: false}
myG.next() //{value: "lueluelue", done: false}
myG.next() //{value: "byebye", done: true}
myG.next() //{value: undefined, done: true}
```
通过上面的代码，可以得出，Generator函数是一个指针对象生成函数，返回的指针对象，可以依次遍历Generator函数内部的每一个状态。
与普通函数不同的是。调用Generator函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象。

###### Generator函数与Iterator接口的关系

根据之前

> 任意一个对象的Symbol.iterator方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个指针对象。

又因为Generator函数就是遍历器生成函数，因此可以把Generator函数赋值给对象的Symbol.iterator属性，从而使该对象具有Iterator接口。
比如：

```
let myIterable = {}
myIterable[Symbol.iterator] = function* (){
	yield 1
	yield 2
	yield 3
}
[...myIterable] // 1,2,3
```

那么aynsc和Generator又是什么关系呢？
回答这个问题之前，我们需要知道什么是异步。
=============================================我是分割线

### 异步

我们都知道，js是单线程的，通俗的讲就是代码一行行的执行呗。可是要是遇到了下面这种情况呢？
![image](https://camo.githubusercontent.com/5546b86424deda9066b57d6d98708d4da7fe21d9/687474703a2f2f7777772e7275616e796966656e672e636f6d2f626c6f67696d672f61737365742f3230313331302f323031333130323030322e706e67)
上图的绿色部分是程序的运行时间，红色部分是等待时间。可以看到，由于I/O操作很慢，所以这个线程的大部分运行时间都在空等I/O操作的返回结果。
###### 那为什么不用多线程呢？
如果采用多线程，同时运行多个任务，那很可能就是下面这样。
![image](http://www.ruanyifeng.com/blogimg/asset/201310/2013102003.png)
可以看到，多线程不仅占用多倍的系统资源，也闲置多倍的资源，这显然不合理。

那么要怎么解决这个问题呢？这时候就用到了Event Loop。

> Event Loop是一个程序结构，用于等待和发送消息和事件

简单说，就是在程序中设置两个线程：一个负责程序本身的运行，称为"主线程"；另一个负责主线程与其他进程（主要是各种I/O操作）的通信，被称为"Event Loop线程"（可以译为"消息线程"）。
![image](http://www.ruanyifeng.com/blogimg/asset/201310/2013102004.png)
上图主线程的绿色部分，还是表示运行时间，而橙色部分表示空闲时间。每当遇到I/O的时候，主线程就让Event Loop线程去通知相应的I/O程序，然后接着往后运行，所以不存在红色的等待时间。等到I/O程序完成操作，Event Loop线程再把结果返回主线程。主线程就调用事先设定的回调函数，完成整个任务。

###### 这种运行方式称为"异步模式"

我们怎么实现异步呢？

比较传统的几种方法：
- 回调函数
- 事件监听
- 发布/订阅
- Promise 对象


###### 比如回调函数（callback）
```
fs.readFile('/etc/passwd', 'utf-8', function (err, data) {
  if (err) throw err;
  console.log(data);
});
```
上面代码中，readFile函数的第三个参数，就是回调函数，也就是任务的第二段。等到操作系统返回了/etc/passwd这个文件以后，回调函数才会执行。

但是回调函数很容易出现一个问题

```
fs.readFile(fileA, 'utf-8', function (err, data) {
  //do something
  fs.readFile(fileB, 'utf-8', function (err, data) {
    //do something
    fs.readFile(fileC, 'utf-8', function (err, data) {
        // ...
      });
  });
});
```
这种时候就出现了多重嵌套，由于多个文件形成了强耦合，只要有一个操作需要修改，它的上层回调函数和下层回调函数，可能都要跟着修改。这种情况就称为"回调函数地狱"

###### Promise就很好的解决了这个问题

```
var fs = require('fs');

var readFile = function (fileName){
  return new Promise(function (resolve, reject){
    fs.readFile(fileName, function(error, data){
      if (error) return reject(error);
      resolve(data);
    });
  });
};


readFile(fileA)
.then(function (data) {
  console.log(data.toString());
})
.then(function () {
  return readFile(fileB);
})
.then(function (data) {
  console.log(data.toString());
})
.then(function () {
  return readFile(fileC);
})
.then(function (data) {
  console.log(data.toString());
})
.catch(function (err) {
  console.log(err);
});
```
可以看到使用了Promise以后，异步任务看的比较的清楚了，但又有了一个问题，那就是代码显得很冗余，一眼看去是一堆的then，语义变得不清楚。

###### 那么，有没有更好的写法呢？


---
###### 答案是Generator函数

```
var fs = require('fs');

var readFile = function (fileName){
  return new Promise(function (resolve, reject){
    fs.readFile(fileName, function(error, data){
      if (error) return reject(error);
      resolve(data);
    });
  });
};

var gen = function* (){
  var f1 = yield readFile('/etc/fstab');
  console.log(f1.toString());
  var f2 = yield readFile('/etc/shells');
  console.log(f2.toString());
};
```

然后，我们可以手动执行

```
var g = gen();

g.next().value.then(function(data){
  g.next(data).value.then(function(data){
    g.next(data);
  });
});

```

手动执行其实就是用then方法，层层添加回调函数。理解了这一点，就可以写出一个自动执行器。

```
function run(gen){
  var g = gen();

  function next(data){
    var result = g.next(data);
    if (result.done) return result.value;
    result.value.then(function(data){
      next(data);
    });
  }

  next();
}

run(gen);
```
[或者用co模块](https://github.com/tj/co)

上面代码中，只要 Generator 函数还没执行到最后一步，next函数就调用自身，以此实现自动执行。





---
说了这么多，开始切入正题

# 那么到底什么是 aynsc呢

```
// Generator
run(function*() {
  const res1 = yield readFile(path.resolve(__dirname, '../data/a.json'), { encoding: 'utf8' });
  console.log(res1);
  const res2 = yield readFile(path.resolve(__dirname, '../data/b.json'), { encoding: 'utf8' });
  console.log(res2);
});

// async/await
const readFile = async ()=>{
  const res1 = await readFile(path.resolve(__dirname, '../data/a.json'), { encoding: 'utf8' });
  console.log(res1);
  const res2 = await readFile(path.resolve(__dirname, '../data/b.json'), { encoding: 'utf8' });
  console.log(res2);
  return 'done'；
}
const res = readFile();
```

答案就是 async函数就是自带了执行器 的Generator函数的语法糖，我们只需要把'*'换成async，把yield换成await就可以了。
