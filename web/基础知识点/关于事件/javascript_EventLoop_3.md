## [Engine, runtime, call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)

### Javascript Engine

以谷歌的V8引擎为例， 它被chrome和node.js使用.

引擎主要包含两个组件：
- Memory Heap - 内存分配发生的地方
- Call Stack - 代码执行时堆栈帧所在的位置


### javascript内存泄漏
主要有4种：
- 1、全局变量Global variables 
 全局变量不能被垃圾收集器收集，需要特别注意用于临时存储和处理大量信息的全局变量。如果必须使用全局变量来存储数据， 请使用全局变量， 不用了请确保将其赋值为null
- 2、 被遗忘的定时器timer 或回调callback
 如setInterval, element.addEventListener('click', onClick)， 不用的时候使用removeEventListener移除事件
- 3、Closures闭包
- 4、Out of DOM references


## [EventLoop](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5)

Event Loops 有一个简单的job, 去监事Call Stack和Callback Queue。 如果Call Stack是空的， 它将会从队列中取出第一个event并放到Call Stack中。
这种迭代称为事件循环中的tick, 每个事件只是一个函数回调。

* 注意： setTimeout() 不会自动的把回调放到event loop 队列中。它设置了一个定时器。当定时器到期时，回调将会被房钱event loop中， 因此一些未来的tick被捡起来并执行。

例如： ````setTimeout(mycallback, 1000)  ````
这并不意味着mycallback 会在1000ms时执行， 而是， mycallback 会被加入队列中。然而这个队列中可能有其他更早的事件， 那么mycallback将不得不等待执行

### ES6 中 Jobs

ES6中有一个新的概念叫Job Queue。 它是Event Loop队列顶部的一层。在处理Promise的异步行为的时候，可能会碰到，

### Callback
回调是在JavaScript程序中表示和管理异步的最常见的方法。

### Nested Callback

### Promise



## [Event Loops 演示工具](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)