## Generators
当你需要惰性地生成一系列值时，请考虑使用生成器函数。Dart内置了两种生成器功能：
- Synchronous generator(同步生成器)： 返回一个Iterable object
- Asynchronous generator(异步生成器)：返回一个stream object

1、去实现一个同步生成器方法， 使用sync*标记函数的body, 使用yield statements 去传送values:
````
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}

````

2、去实现一个异步生成器方法， 使用async*标记函数的body, 使用yield statements 去传送values:
````
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while(k < n) yield k++;
}

````

3、 如果你的生成器是递归的，你可以使用yield* 是提高它的表现
````
Iterable<int> naturalsDownFrom(int n) sync*{
  if(n > 0){
    yield n;
    yield* naturalsDownFrom(n-1);
  }
}

````

## Callable classes
要允许像函数一样调用dart类的实例，请实现call（）方法。



## Isolates(隔离区)
大多数计算机， 即使在移动平台上，也有多核CPU。为了利用所有这些核心，开发人员通常使用并发运行的共享内存线程。但是，共享状态并发很容易出错，并可能导致复杂的代码。
所有的dart代码都在隔离区内运行，而不是线程。每个隔离区都有自己的内存堆， 确保任何其他隔离区都无法访问任何隔离区的状态。

所有的dart代码都在隔离区中运行， 代码只能从同一个隔离区访问类和值。不同的隔离区可以通过端口发送值进行通信(ReceivePort,sendPort)。

一个isolate对象对应一个隔离区，通常不同于当前的隔离区。

当产生一个新的isolate, 这个产生的isolate接收一个isolate对象, 当spawn操作成功时，这个isolate对象将会代表一个新的isolate。

isolate在其自己的事件循环中运行代码，并且每个事件可以在嵌套的微任务队列中运行较小的任务。

由于isolate之间没有共享内存，所以他们之间的通信唯一方式只能通过Port进行，而且Dart中的消息传递总是异步的。

isolate与普通线程的区别是，线程可以有共享内存， 而isolate没有。

isolate创建时候有以下几个主要步骤：
- 初始化isolate数据结构
- 初始化堆内存（Heap）
- 进入新创建的isolate, 使用跟isolate一对一的线程运行isolate
- 配置Port
- 配置消息处理机制（Message Hanlder）
- 配置Debugger, 如果有必要的话
- 将isolate注册到全局监控器（Monitor）

