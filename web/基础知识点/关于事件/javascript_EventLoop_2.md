摘自[EventLoop-微任务和宏任务](https://javascript.info/event-loop)

### Event Loop

基本概念

这有一个无休止的循环， JavaScript引擎等待任务，执行它们，然后休眠，等待更多的任务。

引擎通常算法是：
- 1. 任务出现的同时， 执行他们，并准备开始更老的任务
- 2. 休眠直至一个任务出现，然后执行第一步

当我们浏览一个网页的时候， javascript的引擎大多数时间是空闲的， 只有script/handler/event 激活的时候才会运行。

例如：
- 当外部的script ```<script src="..." > ```加载的时候
- 当用户移动他们的鼠标的时候， 会触发mousemove 事件，和执行hander
- setTimeout 


宏任务： 来自队列的任务
微任务： 只来自我们的代码，如： promise创建后的then/catch/finally handler的执行就是微任务


* 注意：
- 引擎执行任务时从不进行渲染。这项任务是否需要很长事件并不重要。只有在任务完成后， 才会绘制对DOM的更改。
- 如果一个任务花费的时间太长， 浏览器将无法执行其他任务，因此， 一段时间后， 它会引发一个类似的“页面无响应”的警报， 建议用整个页面终止任务。当有许多负责的计算或变成错误导致无线循环时就会发生这种情况。

event loop的算法的具体细节：
- 1、从宏任务队列中出列和运行更老的任务， 如script
- 2、运行所有的微任务 （当微任务队列表不为空的时候）
- 3、渲染render
- 4、如果宏任务队列为空， 等待直到宏任务出现
- 5、执行第一步

要计划一个新的宏任务， 这样做： ```setTimeout(f) ```

要计划一个新的微任务， 这样做：
- 使用queueMicrotask(f)
- Also promise handlers go through the microtask queue.



