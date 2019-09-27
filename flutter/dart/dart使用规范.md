## 关于identifier的规则
### 名类型使用大驼峰

类,枚举,typedefs 和 type parameters, metadata annotation都应该首字母大写且不用分割符号.
例如:
````
class SliderMenu { ... }
class HttpRequest { ... }
typedef Predicate<T> = bool Function(T value);
class Foo {
  const Foo([arg]);
}

@Foo(anArg)
class A { ... }

@Foo()
class B { ... }

````

如果类注释没有参数,可以使用小驼峰,如:
````
const foo = Foo();

@foo
class C { ... }

````

### DO name libraries, packages, directories, and source files using lowercase_with_underscores.

例如:
````
library peg_parser.source_scanner;
import 'file_system.dart';
import 'slider_menu.dart';

````

### DO name import prefixes using lowercase_with_underscores.
正确使用例子:
````
import 'dart:math' as math;
import 'package:angular_components/angular_components'
    as angular_components;
import 'package:js/js.dart' as js;

````
错误使用例子:
````
import 'dart:math' as Math;
import 'package:angular_components/angular_components'
    as angularComponents;
import 'package:js/js.dart' as JS;


````

### DO name other identifiers using lowerCamelCase.
变量和参数变量使用小驼峰, 常量也最好使用小驼峰


### Prefer using lowerCamelCase for constant names

### DO capitalize acronyms and abbreviations longer than two letters like words. 首字母缩略词和缩写词要比单词等两个字母长

### DON’T use a leading underscore for identifiers that aren’t private.
不要对非私有标识符使用前导下划线
### DON’T use prefix letters.
不要使用前缀字母

## ordering
- DO place “dart:” imports before other imports.
在其他import之前 放 dart引入；
- DO place “package:” imports before relative imports.
在relative import之前放包引入
- PREFER placing external “package:” imports before other imports.
最好在其他import之前 防止外部包引入
- DO specify exports in a separate section after all imports.
请在所有imports 之后 在单独的section中指定exports
- DO sort sections alphabetically. 按字母顺序对sections排序


## 格式化
- DO format your code using dartfmt.
- CONSIDER changing your code to make it more formatter-friendly.
- AVOID lines longer than 80 characters.
- DO use curly braces for all flow control statements.

## 使用

### Libraries
- Do use strings in part of directives
- Don't import libraries that are inside the src directory of another package
- prefer reltive paths when importing libraries within your own package's lib directory

### Booleans
- Do use ?? to convert null to a boolean value.

### Strings
- Do use adjacent strings to concatenate string literals. 请使用相邻字符串连接字符串文本。
- prefer using interpolation to compose strings and values. 更喜欢使用插值来合成字符串和值。如：```'Hello, $name! You are ${year - birth} years old.'; ```
- avoid using curly braces in interpolation when not needed. 不需要时避免在插值中使用大括号。如：
```` 
    'Hi, $name!'
    "Wear your wildest $decade's outfit."
    'Wear your wildest ${decade}s outfit.' 
````

### Collections
- do use collection literals when possible.
好的写法：
````
var points = [];
var addresses = {};

var points = <Point>[];
var addresses = <String, Address>{};

````
坏的写法：
````
var points = List();
var addresses = Map();

var points = List<Point>();
var addresses = Map<String, Address>();

````
* 注意， 这条规范不适用于那些类的命名构造函数。list.from()、map.fromiterable()和friends都有其用途。同样，如果要将size传递给List() 以创建一个固定大小的集合，那么它是有意义的。

- don't use .length to  see if a collection is empty.
不要使用.length 来查看集合是否是空的。
iterable契约不要求集合知道其长度或能够在固定时间内提供它。调用 .length只是查看集合中是否包含任何内容，这可能会非常缓慢。
相反，这有更快更易阅读的获取方法：.isEmpty和.isNotEmpty. 
好的写法：
````
if(lunchBox.isEmpty) return 'so hungry...';
if(words.isNotEmpty) return words.join(' ');
````
坏的写法：
````
if(!words.isEmpty) return words.join(' ');

````

- consider using higher-order methods to transform a sequence.
考虑使用高阶方法来变换序列。
如果你有一个结合，并且希望从中生成一个新的修改过的集合，那么在iterable上使用.map()、where()和其他方便的方法通常会更简短、更具有声明性。使用这些替代命令式for循环可以清楚地表明您的意图是生成一个新的序列，而不是产生副作用。
好的写法：
````
var aquaticNames = animals
    .where((animal) => animal.isAquatic)
    .map((animal) => animal.name);

````

- avoid using Iterable.forEach() with a function literal.
foreach()函数在javascript中被广泛使用，因为内置for in循环。在dart中，如果要在序列上迭代，惯用的方法是使用循环。
好的写法：
````
for(var person in people){}
````
坏的写法：
````
people.forEach((person){})

````
* 注意，这条准则特别指出了“function literal”。 如果要在每个元素上调用一些已经存在的函数，forEach()就可以了，如：
````
people.forEach(print);
````
* 注意， 使用map.forEach()总是可以的。Map是不可iterable, 所以这条准则不适用。


- don't use List.from() unless you intend to change the type of the result.
不要使用List.from()除非你打算改变结果的类型。
指定一个Iterable, 这有两个明显的方式去生产一个新的包含了相同元素的List。
````
var copy = iterable.toList();
var copy2 = List.from(iterable);

````
明显的区别是第一个更短。重要的区别在于，第一个保留了原始对象的类型参数。
好的写法：
````
// Creates a List<int>:
var iterable = [1, 2, 3];

// Prints "List<int>":
print(iterable.toList().runtimeType);
````
坏的写法：
````
// Creates a List<int>:
var iterable = [1, 2, 3];

// Prints "List<dynamic>":
print(List.from(iterable).runtimeType);

````

如果你想改变类型，就调用List.from()方法是有用的。
````
var numbers = [1,2.3,4]; // List<num>
numbers.removeAt(1); // Now it only contains integers
var ints = List<int>.from(numbers);

````

- do use whereType() to filter a collection by type.
坏的写法：
````
var objects = [1, "a", 2, "b", 3];
var ints = objects.where((e) => e is int);

var objects = [1, "a", 2, "b", 3];
var ints = objects.where((e) => e is int).cast<int>();

````
好的写法：
````
var objects = [1, "a", 2, "b", 3];
var ints = objects.whereType<int>();

````

- don't use cast() when a nearby operation will do.
通常在处理iterable或stream时，会对其执行几个转换。最后，你希望生成具有特定类型参数的对象。而不是跟踪调用cast(), 去看其中一个现有的转换是否可以改变类型。
好的做法：
````
var stuff = <dynamic>[1, 2];
var ints = List<int>.from(stuff);

var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map<double>((n) => 1 / n);
````
坏的做法：
````
var stuff = <dynamic>[1, 2];
var ints = stuff.toList().cast<int>();

var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map((n) => 1 / n).cast<double>();

````

- avoid using cast()
避免使用cast（）来改变集合类型
替代方案：
 + Create it with the right type.
 ````
// 好的写法
 List<int> singletonList(int value) {
  var list = <int>[];
  list.add(value);
  return list;
}

// 坏的写法
List<int> singletonList(int value) {
  var list = []; // List<dynamic>.
  list.add(value);
  return list.cast<int>();
}


 ````
 + Cast the elements on access.
 ````
 // 好的写法
 void printEvens(List<Object> objects) {
  // We happen to know the list only contains ints.
  for (var n in objects) {
    if ((n as int).isEven) print(n);
  }
}

 // 坏的写法
 void printEvens(List<Object> objects) {
  // We happen to know the list only contains ints.
  for (var n in objects.cast<int>()) {
    if (n.isEven) print(n);
  }
}

 ````
 + Eagerly cast using List.from()。
 ````
 // 好的写法
 int median(List<Object> objects) {
  // We happen to know the list only contains ints.
  var ints = List<int>.from(objects);
  ints.sort();
  return ints[ints.length ~/ 2];
}

 // 坏的写法
 int median(List<Object> objects) {
  // We happen to know the list only contains ints.
  var ints = objects.cast<int>();
  ints.sort();
  return ints[ints.length ~/ 2];
}


 ````


### Functions
- Do use a function declaration to bind a function to a name.
- Don't create a lambda when a tear-off will do.

### Parameters
- Do use = to separate a named parameter from its default value.
- Don't use an explicit default value of null

### Variables
- Don't explicitly initialize variables to null.
- Avoid storing what you can calculate.

### Members
- Don't wrap a field in a getter and setter unnecessarily.
- Prefer using a final field to make a read-only property.
- Consider using => for simple members.
- Don't use this.  except to redirect to a named constructor or to avoid shadowing.
- Do initialize fields at their declaration when possible.

### Constructors
- Do use initializing formals when possible.
- Don't type annotate initializing formals.
- Do use ; instead of {} for empty constructor bodies.
- Don't use new
- Don't use const redundantly

### Error handling
- Avoid catches without on clauses.
- Don't discard errors from catches without on clauses.
- Do throw objects that implement Error only for programmatic errors.
- Don't explicitly catch Error or types that implement it.
- Do use rethrow to rethrow a caught exception.

### Asynchrony
- prefer async/await over using raw futures.
众所周知，异步代码很难阅读和调试，即使你使用了像futures这样的。async/await语法提高了可读性，并允许
您在异步代码中使用所有dart控制流结构。
例如：
好的写法：
````
Future<int> countActivePlayers(String teamName) async {
  try {
    var team = await downloadTeam(teamName);
    if (team == null) return 0;

    var players = await team.roster;
    return players.where((player) => player.isActive).length;
  } catch (e) {
    log.error(e);
    return 0;
  }
}

````
坏的写法：
````
Future<int> countActivePlayers(String teamName) {
  return downloadTeam(teamName).then((team) {
    if (team == null) return Future.value(0);

    return team.roster.then((players) {
      return players.where((player) => player.isActive).length;
    });
  }).catchError((e) {
    log.error(e);
    return 0;
  });
}

````

- Don't use async when it has no useful effect.
很容易养成在任何执行与异步相关的操作的函数上使用async的习惯。但在某些情况下，这是无关的。如果可以省略异步而不更改函数的
行为，请执行此操作。
好的写法：
````
Future afterTwoThings(Future first, Future second) {
  return Future.wait([first, second]);
}

````
坏的写法：
````
Future afterTwoThings(Future first, Future second) async {
  return Future.wait([first, second]);
}

````

async有用的案例：
 + You are using await.
 + You are returning an error asynchronously. async and then throw is shorter than return Future.error(...)
 + You are returning a value and you want it implicitly wrapped in a future. async is shorter than Future.value()
 
例子：
````
Future usesAwait(Future later) async {
  print(await later);
}

Future asyncError() async {
  throw 'Error!';
}

Future asyncValue() async => 'value';

````


- Consider using higher-order methods to transform a stream.
- Avoid using completer directly.
许多刚接触异步编程的人都想编写能够产生未来的代码。Future的构造函数似乎不适合他们的需求。
所以他们最终会找到completer类并使用它。
坏的写法：
````
Future<bool> fileContainsBear(String path){
  var completer = Completer<bool>();
  File(path).readAsString().then((contents){
    completer.complete(contents.contains('bear'));
  });
  return completer.future;
}

````
completer的两种使用场景：新的异步原语， 以及与不使用futures的异步代码接口。大多数其他代码应该使用async/await 或 Future.then(), 因为它们更清晰，更容易处理错误。
好的写法：
````
Future<bool> fileContainsBear(String path) {
  return File(path).readAsString().then((contents) {
    return contents.contains('bear');
  });
}

Future<bool> fileContainsBear(String path) async {
  var contents = await File(path).readAsString();
  return contents.contains('bear');
}

````

- Do test for Future<T> when disambiguating a FutureOr<T> whose type argument could be Object.

在使用FutureOr<T>做任何有用的事情之前，通常需要检查是否有Future<T>或bare T。如果类型参数是FutureOr<int>的某个特定类型，使用 is int 方法还是使用 is future<int>并不重要，两种方法都可以，因为这两种方法是不相交。

但是， 如果值类型是object或可能用object实例化的类型参数，则两个分支重叠。Future<Object>本身实现了object, object或is T 也实现了， 其中T是一些类型参数， 即使对象是Future, 也可以用object实例化， 返回true.

好的写法：
````
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is Future<T>) {
    var result = await value;
    print(result);
    return result;
  } else {
    print(value);
    return value as T;
  }
}

````
坏的写法：
````
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is T) {
    print(value);
    return value;
  } else {
    var result = await value;
    print(result);
    return result;
  }
}

````

## Design

### Names
- Do use terms consistently.
- Avoid abbreviations.
- Prefer putting the most descriptive noun last.
- Consider making the code read like a sentence.
- prefer a noun phrase for a non-boolean property or variable.
- prefer a non-imperative verb phrase for a boolean property or variable
- consider omitting the verb for a named boolean parameter.
- prefer the positive name for a boolean property or variable
- prefer an imperative verb phrase for a function or method whose main purpose is a side effect.
- prefer a noun phrase or non-imperative verb phrase for a function or method if returning a value is its primary purpose.
- consider an imperative verb phrase for a function or method if you want to draw attention to the work it performs.
- avoid starting a method name with get.
- prefer naming a method to___() if it copies the object's state to a new object.
- prefer naming a method as___() if it returns a different representation backed by the original object.
- avoid describing the parameters in the function's or method's name.
- do following existing mnemonic conventions when naming type parameters.
 