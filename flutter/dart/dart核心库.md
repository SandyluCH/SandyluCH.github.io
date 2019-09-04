## Dart core libraries

[Dart API reference](https://api.dart.dev/stable/2.4.1/index.html)

### dart:async
支持的平台： 所有

支持异步编程，包括Future和Stream类

异步编程通常使用回调函数， 但是dart提供了其他选择：Future and Stream 对象。

#### Future
Future就想是对将来某个时候所能提供的结果的承诺。

在直接使用Future的api之前，请考虑使用await替代。 使用await的表达式比使用Future api的代码更容易理解。

例如：
````
runUsingFuture() {
  // ...
  findEntryPoint().then((entryPoint) {
    return runExecutable(entryPoint, args);
  }).then(flushThenExit);
}

````
使用await的写法则为：
````
runUsingAsyncAwait() async {
  // ...
  var entryPoint = await findEntryPoint();
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
}

// 或者
var entryPoint = await findEntryPoint();
try {
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
} catch (e) {
  // Handle the error...
}

````


使用catchError来捕捉future对象可能抛出的错误
````
HttpRequest.getString(url).then((String result) {
  print(result);
}).catchError((e) {
  // Handle or ignore the error.
});

````
这个```then().catchError() ```的格式 是```try-catch ```的异步版本。


链接多个异步方法，例子如下：
````
Future result = costlyQuery(url);
result
    .then((value) => expensiveWork(value))
    .then((_) => lengthyComputation())
    .then((_) => print('Done!'))
    .catchError((exception) {
  /* Handle exception... */
});

````
在这个例子中，方法执行的顺序：
````costlyQuery()  ->  expensiveWork()  -> lengthyComputation()  ````
这个例子使用await的写法是：
````
try {
  final value = await costlyQuery(url);
  await expensiveWork(value);
  await lengthyComputation();
  print('Done!');
} catch (e) {
  /* Handle exception... */
}

````

有时候你的算法需要触发多个异步方法并且等待他们都执行完成之后才能继续，使用Future.await()静态方法来管理多个futures 并且等待他们执行完。
````
Future deleteLotsOfFiles() async =>  ...
Future copyLotsOfFiles() async =>  ...
Future checksumLotsOfOtherFiles() async =>  ...

await Future.wait([
  deleteLotsOfFiles(),
  copyLotsOfFiles(),
  checksumLotsOfOtherFiles(),
]);
print('Done with all the long steps!');

````


#### Stream
Stream是获取一系列值（如事件）的方法。

Stream对象出现在整个dart api中，表示数据序列。例如，诸如按钮单击之类的html事件是使用流传递的。您还可以将文件作为流读取。

有时你可以使用异步for循环（await for） ,而不是使用流api。

考虑下面的函数。它使用stream.listen()的方法订阅文件列表，如下：
````
void main(List<String> arguments) {
  // ...
  FileSystemEntity.isDirectory(searchPath).then((isDir) {
    if (isDir) {
      final startingDir = Directory(searchPath);
      startingDir
          .list(
              recursive: argResults[recursive],
              followLinks: argResults[followLinks])
          .listen((entity) {
        if (entity is File) {
          searchFile(entity, searchTerms);
        }
      });
    } else {
      searchFile(File(searchPath), searchTerms);
    }
  });
}

````
另一种写法：
````
Future main(List<String> arguments) async {
  // ...
  if (await FileSystemEntity.isDirectory(searchPath)) {
    final startingDir = Directory(searchPath);
    await for (var entity in startingDir.list(
        recursive: argResults[recursive],
        followLinks: argResults[followLinks])) {
      if (entity is File) {
        searchFile(entity, searchTerms);
      }
    }
  } else {
    searchFile(File(searchPath), searchTerms);
  }
}

````

监听Stream data, 去获取值， 要么使用```await for ``` ,要么使用```listen()```订阅Stream,例如：
````
// Find a button by ID and add an event handler.
querySelector('#submitInfo').onClick.listen((e) {
  // When the button is clicked, it runs this code.
  submitData();
});

````
在这个例子中， ```onClick ```属性是一个由‘submitInfo’的按钮提供的Stream对象。
如果你只关心一个事件，那么可以使用first、last或single等属性来获取它。要在处理事件之前测试该时间，
请使用诸如firstWhere()、lastWhere()或 singleWhere()等方法。 如果你关心事件的子集，可以使用skip()、
skipWhile()、 take()、takeWhile() 和 where()。

通常，在使用流数据之前，你需要转换流数据的格式。使用transform()方法去生成一个不同类型数据的流。
````
var lines = inputStream
    .transform(utf8.decoder)
    .transform(LineSplitter)

````



### dart:collections
支持的平台： 所有

补充dart:core中集合支持的类和使用程序

### dart:convert
支持的平台： 所有

用于在不同数据表示形式（包括JSON和UTF-8）之间转换的编码器和解码器

### dart:core
支持的平台： 所有
为每个dart程序内置类型、集合和其他核心功能。这个库在每个dart项目里面都会被自动引入。

### dart:developer
支持平台：JIT web(experimental, dartdevc only)

与开发人员工具（如调试器和检查器）的交互

### dart:html
支持平台：web

用于基于web的应用程序的html元素和其他资源

### dart:indexed_db
支持平台：web

支持索引的键值对存储

### dart:io 
支持平台：JIT  AOT

File, socket, HTTP, 和其他I/O的支持用于非web应用

### dart:isolate
支持平台： JIT AOT

使用隔离的并发编程： 类似于线程的独立worker

### dart:js 和dart:js_util
支持平台： web
不要使用， 相反，去使用js包， 如[javascript interoperability](https://dart.dev/web/js-interop) 中描述的那样去使用

### dart:math
支持平台：all

数学常数和函数，加上随机数发生器

### dart:mirrors
支持平台：JIT(experimental, not flutter)

基本反射，支持内省和动态调用。

### dart:typed_data
支持平台：all

列出有效处理固定大小数据（例如：无符号8字节整数）和simd数值类型的列表

### dart:web_audio
支持平台： web

在浏览器中进行高保真音频编程

### dart:web_gl
支持平台：web

在浏览器中进行3D编程
