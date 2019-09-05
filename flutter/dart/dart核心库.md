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

#### 操作DOM

使用DOM, 你需要知道windows, documents, elements 和nodes。

windows对象表示web浏览器的实际窗口。每个窗口都有一个document对象， 该对象只想当前加载的文档。window对象还具有对各种api的访问其， 如IndexedDB(用于存储数据)、requestanimationframe(用于动画)等。在选项卡式浏览器中，每个选项卡都有自己的窗口对象。

使用document对象， 可以在文档中创建和操作元素对象。请注意，文档本身是一个元素，可以对其进行操作。

Dom为节点树建模。这些节点通常是元素，但也可以是属性、文本、注释和其他dom类型。除了没有父节点的根节点之外，dom中的每个节点都有一个父节点，并且可能有许多子节点。

查询一个或多个元素使用方法querySelector() 和 querySelectorAll(). 你可以通过id,class,tag,name,或者这些的组合来查询。例如：
````
  // Find an element by id (an-id).
  Element elem1 = querySelector('#an-id');

  // Find an element by class (a-class).
  Element elem2 = querySelector('.a-class');

  // Find all elements by tag (<div>).
  List<Element> elems1 = querySelectorAll('div');

  // Find all text inputs.
  List<Element> elems2 = querySelectorAll(
    'input[type="text"]',
  );

  // Find all elements with the CSS class 'class'
  // inside of a <p> that is inside an element with
  // the ID 'id'.
  List<Element> elems3 = querySelectorAll('#id p.class');

````

操作元素
可以使用属性更改元素的状态。节点及其子类型元素定义所有元素都具有的属性。例如，所有元素都有classes、 hidden、 id、 style、 title属性， 你可以使用这些属性来设置状态。元素的子类定义其他属性， 如AnchorElement的href属性。如：
````
var anchor = querySelector('#example') as AnchorElement;
anchor.href = 'http://dartlang.org';

````

设置hidden属性为true，效果等同于```display:none ```

当right属性不可用或不方便时，可以使用元素的attributes属性，此属性是Map<String, String>, 其中键是属性名， 可以使用的键值列表及其意义，见[MDN attribute](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes)。 例如：
````
 elem.attributes['someAttribute'] = 'someValue';

````


创建元素， 例如：
````
// 方式一
 var elem = ParagraphElement();
  elem.text = 'Creating is easy!';

// 方式二
  var elem2 = Element.html(
    '<p>Creating <em>is</em> easy!</p>',
  );

````
注意elem2也是 ParagraphElement。


通过为新创建的元素分配父元素，将其附加到文档。可以将元素添加到任何现有元素的子元素中。在下面例子中， body是一个元素， 可以从children属性访问它的子元素（作为List<Element>）。``` document.body.children.add(elem2); ```


添加、替换、和移除节点。 元素只是一种节点。 使用节点的nodes属性可以找到节点的所有子节点， 该属性返回一个List<Node>(与忽略非元素节点的子节点不同)。拥有此列表后， 可以使用常用的列表方法和运算符来操作节点的子节点。

要将节点添加为其父节点的最后一个子节点，请使用List add()方法， 如：``` querySelector('#inputs').nodes.add(elem); ``` 

替换节点，使用节点replaceWith()方法， 如：``` querySelector('#status').replaceWith(elem); ```

移除节点， 使用节点remove()方法， 如: 
````  
// Find a node by ID, and remove it from the DOM.
  querySelector('#expendable').remove(); 

````

操作css样式。css或层叠样式表定义了dom元素的表示样式。可以通过将id和类属性附加到元素来更改元素的外观。 每个元素都有一个类字段，它是一个列表。只需在此集合中添加和移除字符串，即可添加或移除css类。例如：
````
  var elem = querySelector('#message');
  elem.classes.add('warning');

  var message = DivElement();
  message.id = 'message2';
  message.text = 'Please subscribe to the Dart mailing list.';
  // 或
  var message = DivElement()
    ..id = 'message2'
    ..text = 'Please subscribe to the Dart mailing list.';

  message.style
    ..fontWeight = 'bold'
    ..fontSize = '3em';

````

处理事件。要响应外部事件， 如click、 焦点更改、 selections， 请添加事件侦听器。你可以将事件侦听器添加到页面上的任何元素。 使用element.onEvent.listen(function)添加事件处理程序， 其中event是事件名称，function是事件处理程序。例如：
````
 // Find a button by ID and add an event handler.
  querySelector('#submitInfo').onClick.listen((e) {
    // When the button is clicked, it runs this code.
    submitData();
  });

````
事件可以通过dom树上下传播。要发现最初触发事件的元素， 请使用e.target, 例如：
````
document.body.onClick.listen((e) {
    final clickedElem = e.target;
    // ...
  });

````
要查看可以注册事件侦听器的所有事件，请在元素及其[子类的api文档](https://api.dart.dev/stable/2.4.1/dart-html/Element-class.html)中查找onEventType类型的属性， 如：onChange、 onBlur、 onKeyDown, 一些通用的EventType包括：change、 blur、 keyDown、 keyUp、 mouseDown、 mouseUp。




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
