### [dart语言样本概览](https://dart.dev/guides/language/language-tour)
#### 1、每个应用都有一个main()功能.
````
void main(){
  print('Hello world');
}
````
#### 2、在类型的安全的dart代码中，由于type reference功能，大多数变量都不需要显示类型；
````
var name = 'Jack';
var year = 1977;

````
#### 3、控制流方式
dart语言支持通常的控制流方式
````
if (year >= 2001) {
  print('21st century');
} else if (year >= 1901) {
  print('20th century');
}

for (var object in flybyObjects) {
  print(object);
}

for (int month = 1; month <= 12; month++) {
  print(month);
}

while (year < 2016) {
  year += 1;
}

````
#### 函数
建议制定每个函数的参数和返回值的类型
````
int fibonacci(int n) {
  if (n == 0 || n == 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

var result = fibonacci(20);

````
#### 注释
````
// This is a normal, one-line comment.

/// This is a documentation comment, used to document libraries,
/// classes, and their members. Tools like IDEs and dartdoc treat
/// doc comments specially.

/* Comments like these are also supported. */

````

#### 引入
获取定义在其他库的api使用 import
````
// Importing core libraries
import 'dart:math';

// Importing libraries from external packages
import 'package:test/test.dart';

// Importing files
import 'path/to/my_other_file.dart';

````
#### 类

如类Spacecraft的定义
````
class Spacecraft {
  String name;
  DateTime launchDate;

  // Constructor, with syntactic sugar for assignment to members.
  Spacecraft(this.name, this.launchDate) {
    // Initialization code goes here.
  }

  // Named constructor that forwards to the default one.
  Spacecraft.unlaunched(String name) : this(name, null);

  int get launchYear =>
      launchDate?.year; // read-only non-final property

  // Method.
  void describe() {
    print('Spacecraft: $name');
    if (launchDate != null) {
      int years =
          DateTime.now().difference(launchDate).inDays ~/
              365;
      print('Launched: $launchYear ($years years ago)');
    } else {
      print('Unlaunched');
    }
  }
}

````

使用spacecraft
````
var voyager = Spacecraft('Voyager I', DateTime(1977, 9, 5));
voyager.describe();

var voyager3 = Spacecraft.unlaunched('Voyager III');
voyager3.describe();

````

#### 继承(Inheritance)
#### Mixins
#### Interfaces and abstract classes
#### async 和 await 同步和异步
#### 异常Exceptions
#### 关键字
![avatar](./keywords.png)
说明：
Avoid using these words as identifiers. However, if necessary, the keywords marked with superscripts can be identifiers:

- 上下文变量  Words with the superscript 1 are contextual keywords, which have meaning only in specific places. They’re valid identifiers everywhere.

- 内置变量  Words with the superscript 2 are built-in identifiers. To simplify the task of porting JavaScript code to Dart, these keywords are valid identifiers in most places, but they can’t be used as class or type names, or as import prefixes.

- 新变量  Words with the superscript 3 are newer, limited reserved words related to the asynchrony support that was added after Dart’s 1.0 release. You can’t use await or yield as an identifier in any function body marked with async, async*, or sync*.

## variables
例如初始化变量
```
var name = 'Bob';
```
Variables store references. 即这个变量包含了信息： “Bob”字符串是string object类型。

如果你想改变name的类型且在初始化的情况下，即如果一个对象没有被限制成单一对象， 指定这个对象或者动态类型，
````
dynamic name = 'Bob';
````
或者指定类型
````
String name = 'Bob';

````
### 默认值
未被初始化的变量有一个初始值null.
````
int lineCount;
assert(lineCount == null);

````
### Final and const
如果你打算不改变变量， 使用final和const

### 内置变量类型
- numbers
- strings
- booleans
- lists (also known as arrays)
- sets
- maps
- runes ( for expressing Unicode characters in a string )
- symbols

在dart中， 每个变量即对象（an instance of a class）, 故你可以使用构造函数来初始化变量。一些
内置变量有他们自己的构造函数，例如， 你可以使用Map()构造方法来创建一个map对象。

#### 数字类型
- int
Integer values no larger than 64 bits, depending on the platform. On the Dart VM, values can be from -263 to 263 - 1. Dart that’s compiled to JavaScript uses JavaScript numbers, allowing values from -253 to 253 - 1.

- double
64-bit (double-precision) floating-point numbers, as specified by the IEEE 754 standard.

字符串与数字之间的互转：
````
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');

````

#### 字符串String类型
A Dart string is a sequence of UTF-16 code units。你可以使用单引号和引号来创建一个字符串。

你也可以使用${expression}在字符串中放表达式的值。如果expression是一个变量，可以取消{}。如：
````
var s = 'string interpolation';

assert('Dart has $s, which is very handy.' ==
    'Dart has string interpolation, ' +
        'which is very handy.');
assert('That deserves all caps. ' +
        '${s.toUpperCase()} is very handy!' ==
    'That deserves all caps. ' +
        'STRING INTERPOLATION is very handy!');

````

你可以使用相邻的字符串或+号来连接字符串
````
var s1 = 'String '
    'concatenation'
    " works even over line breaks.";
assert(s1 ==
    'String concatenation works even over '
        'line breaks.');

var s2 = 'The + operator ' + 'works, as well.';
assert(s2 == 'The + operator works, as well.');

````
创建多行文本字符串，可以使用带单引号或双引号的三引号：
````
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";

````
你可以在字符串前面加r 来创建一个“raw”字符串：
````
var s = r'In a raw string, not even \n gets special treatment.';

````
var和const定义的变量不能连接成const类型的字符串


#### Booleans
dart语言中只有两个值true和false（都是编译时变量）是bool类型的。
dart类型的安全性意味着你不能使用如下代码表达式```if(nonbooleanValue)``` 和 ```assert (nonbooleanValue) ```. 相反，需要明确指定检查的值，如下所示：
````
// Check for an empty string.
var fullName = '';
assert(fullName.isEmpty);

// Check for zero.
var hitPoints = 0;
assert(hitPoints <= 0);

// Check for null.
var unicorn;
assert(unicorn == null);

// Check for NaN.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);

````

#### Lists
在dart语言中，数组就是List 对象。
````
var list = [1, 2, 3];
````
Dart 2.3里有扩展操作符(...) 和 允许空值的扩展操作符(...?),都提供了将多个元素插入到集合中的简洁方法。
如：
````
// 不允许空值
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);


// 允许空值
var list;
var list2 = [0, ...?list];
assert(list2.length == 1);

````

也可以使用conditional (if) 和 repetition (for) 来建立集合。
如：
````
 var promoActive = false;
var nav = [
  'Home',
  'Furniture',
  'Plants',
  if (promoActive) 'Outlet'
]; // promoActive 为false时， nav:['Home', 'Furniture','Plants']
// promoActive 为true 时， nav:['Home', 'Furniture','Plants', 'Outlet'],

````
````
var listOfInts = [1, 2, 3];
var listOfStrings = [
  '#0',
  for (var i in listOfInts) '#$i'
];
assert(listOfStrings[1] == '#1');

````

list<T> 构造函数:
- List([int length ])
Creates a list of the given length. [...]
factory
- List.filled(int length, E fill, { bool growable: false })
Creates a list of the given length with fill at each position. [...]
factory
- List.from(Iterable elements, { bool growable: true })
Creates a list containing all elements. [...]
factory
- List.generate(int length, E generator(int index), { bool growable: true })
Generates a list of values. [...]
factory
- List.of(Iterable<E> elements, { bool growable: true })
Creates a list from elements. [...]
factory
- List.unmodifiable(Iterable elements)
Creates an unmodifiable list containing all elements. [...]
factory

List<T>方法：
- add(E value) → void
Adds value to the end of this list, extending the length by one. [...]
- addAll(Iterable<E> iterable) → void
Appends all objects of iterable to the end of this list. [...]
- asMap() → Map<int, E>
Returns an unmodifiable Map view of this. [...]
- cast<R>() → List<R>
Returns a view of this list as a list of R instances. [...]
override
- clear() → void
Removes all objects from this list; the length of the list becomes zero. [...]
- fillRange(int start, int end, [ E fillValue ]) → void
Sets the objects in the range start inclusive to end exclusive to the given - - fillValue. [...]
- getRange(int start, int end) → Iterable<E>
Returns an Iterable that iterates over the objects in the range start inclusive to end exclusive. [...]
- indexOf(E element, [ int start = 0 ]) → int
Returns the first index of element in this list. [...]
- indexWhere(bool test(E element), [ int start = 0 ]) → int
Returns the first index in the list that satisfies the provided test. [...]
- insert(int index, E element) → void
Inserts the object at position index in this list. [...]
- insertAll(int index, Iterable<E> iterable) → void
Inserts all objects of iterable at position index in this list. [...]
- lastIndexOf(E element, [ int start ]) → int
Returns the last index of element in this list. [...]
- lastIndexWhere(bool test(E element), [ int start ]) → int
Returns the last index in the list that satisfies the provided test. [...]
- remove(Object value) → bool
Removes the first occurrence of value from this list. [...]
- removeAt(int index) → E
Removes the object at position index from this list. [...]
- removeLast() → E
Pops and returns the last object in this list. [...]
- removeRange(int start, int end) → void
Removes the objects in the range start inclusive to end exclusive. [...]
- removeWhere(bool test(E element)) → void
Removes all objects from this list that satisfy test. [...]
- replaceRange(int start, int end, Iterable<E> replacement) → void
Removes the objects in the range start inclusive to end exclusive and inserts the contents of replacement in its place. [...]
- retainWhere(bool test(E element)) → void
Removes all objects from this list that fail to satisfy test. [...]
- setAll(int index, Iterable<E> iterable) → void
Overwrites objects of this with the objects of iterable, starting at position index in this list. [...]
- setRange(int start, int end, Iterable<E> iterable, [ int skipCount = 0 ]) → void
Copies the objects of iterable, skipping skipCount objects first, into the range start, inclusive, to end, exclusive, of the list. [...]
- shuffle([Random random ]) → void
Shuffles the elements of this list randomly.
- sort([int compare(E a, E b) ]) → void
Sorts this list according to the order specified by the compare function. [...]
- sublist(int start, [ int end ]) → List<E>
Returns a new list containing the elements between start and end. [...]
- any(bool test(E element)) → bool
Checks whether any element of this iterable satisfies test. [...]
inherited
- contains(Object element) → bool
Returns true if the collection contains an element equal to element. [...]
inherited
- elementAt(int index) → E
Returns the indexth element. [...]
inherited
- every(bool test(E element)) → bool
Checks whether every element of this iterable satisfies test. [...]
inherited
- expand<T>(Iterable<T> f(E element)) → Iterable<T>
Expands each element of this Iterable into zero or more elements. [...]
inherited
- firstWhere(bool test(E element), { E orElse() }) → E
Returns the first element that satisfies the given predicate test. [...]
inherited
- fold<T>(T initialValue, T combine(T previousValue, E element)) → T
Reduces a collection to a single value by iteratively combining each element of the collection with an existing value [...]
inherited
- followedBy(Iterable<E> other) → Iterable<E>
Returns the lazy concatentation of this iterable and other. [...]
inherited
- forEach(void f(E element)) → void
Applies the function f to each element of this collection in iteration order.
inherited
- join([String separator = "" ]) → String
Converts each element to a String and concatenates the strings. [...]
inherited
- lastWhere(bool test(E element), { E orElse() }) → E
Returns the last element that satisfies the given predicate test. [...]
inherited
- map<T>(T f(E e)) → Iterable<T>
Returns a new lazy Iterable with elements that are created by calling f on each element of this Iterable in iteration order. [...]
inherited
- noSuchMethod(Invocation invocation) → dynamic
Invoked when a non-existent method or property is accessed. [...]
inherited
- reduce(E combine(E value, E element)) → E
Reduces a collection to a single value by iteratively combining elements of the collection using the provided function. [...]
inherited
- singleWhere(bool test(E element), { E orElse() }) → E
Returns the single element that satisfies test. [...]
inherited
- skip(int count) → Iterable<E>
Returns an Iterable that provides all but the first count elements. [...]
inherited
- skipWhile(bool test(E value)) → Iterable<E>
Returns an Iterable that skips leading elements while test is satisfied. [...]
inherited
- take(int count) → Iterable<E>
Returns a lazy iterable of the count first elements of this iterable. [...]
inherited
- takeWhile(bool test(E value)) → Iterable<E>
Returns a lazy iterable of the leading elements satisfying test. [...]
inherited
- toList({bool growable: true }) → List<E>
Creates a List containing the elements of this Iterable. [...]
inherited
- toSet() → Set<E>
Creates a Set containing the same elements as this iterable. [...]
inherited
- toString() → String
Returns a string representation of this object.
inherited
- where(bool test(E element)) → Iterable<E>
Returns a new lazy Iterable with all elements that satisfy the predicate test. [...]
inherited
- whereType<T>() → Iterable<T>
Returns a new lazy Iterable with all elements that have type T. [...]
inherited



#### Sets 集合
set是一组无序且无重复的item的集合。

创建一个简单的set:
````
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};

````
创建一个空的set:
````
var names = <String>{};
// Set<String> names = {}; // This works, too.
// var names = {}; // Creates a map, not a set.
````
set 也支持扩展操作符 (... 和 ...?)


#### Maps

Maps是键值对的集合。key值具有唯一性，value不具有唯一性。
使用map literals创建 dart map:
````
var gifts = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};

var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};

````
* 上述代码中的gifts对象相当于类型为Map<String, String>的集合， nobleGases相当于类型为Map<int, String>的集合。如果你尝试添加错误类型的值到任意一个map对象中， 都会报错。

你也可以使用map的构造函数来创建map对象
````
var gifts = Map();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = Map();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';

````

向已存在map中添加键值对：
````
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds'; // Add a key-value pair

````

获取map中的某个键值，如```gifts['fourth'] ```,如果键值不存在，返回的记过为null.

maps 同样支持操作符（... 和 ...?）.

#### Runes（符号文字）
在dart中， runes是UTF-32编码的字符串。它可以通过文字转换成符号表情或者代表特定的文字。 String类型是UTF-16编码， 所以Rune是一种特殊的字符串，有自己相对独有的声明方式。

通常一个Unicode code point 表达方式是 \uXXXX, 其中XXXX是4位的16进制值。 例如 \u2665 表示❤。 如果要表示多于或者少于4位的16进制字节的表情符号， 可以使用大括号括起来。如：笑脸 是\u{1f600}。
例如：
````
 var clapping = '\u{1f44f}';
  print(clapping);
  print(clapping.codeUnits);
  print(clapping.runes.toList());

  Runes input = new Runes(
      '\u2665  \u{1f605}  \u{1f60e}  \u{1f47b}  \u{1f596}  \u{1f44d}');
  print(new String.fromCharCodes(input));

````

翻转字符串例子1：
````
var input = "Music \u{1d11e} for the win"; // Music 𝄞 for the win
print(new String.fromCharCodes(input.runes.toList().reversed)); // niw eht rof 𝄞 cisuM

````
翻转字符串例子2：
````
var input =  'Ame\u{301}lie'; // Amélie
print(new String.fromCharCodes(input.runes.toList().reversed)); // eiĺemA

````

#### Symbols
一个symbol对象表示在dart项目中定义的运算符和标识符。

#### Functions
例子1：
````
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}

````

例子2：
````
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
````

例子3：
````
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;

````

##### 可选的参数
可选的参数要么是 指定位置参数，要么是命名参数

1、可选的命令参数 paramName: value

如：
定义一个函数：
````
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold, bool hidden}) {...}
````
调用这个函数：
````
enableFlags(bold:true, hidden:false);
````

可以使用@required来标识一个必须要的参数，例如：
````
const Scrollbar({Key key, @required Widget child})

````

2、可选的位置参数

函数的参数集合中 用[]标识的就是可选的位置参数，如：
````
String say(String from, String msg, [String device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}
````
调用该函数的方法是:
````
  var msg = say('Bob', 'Howdy');
  var msg2 = say('Bob', 'Howdy', 'smoke singal');
  print(msg); // Bob says Howdy
  print(msg2); // Bob says Howdy with a smoke singal
````

3、 默认的参数值

你的函数可以使用=号给命令参数或者位置参数来定义默认值，默认值必须是编译时常量， 如果没有默认值提供，那么默认值就认为是null. 例如：
````
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold = false, bool hidden = false}) {...}

// bold will be true; hidden will be false.
enableFlags(bold: true);

````

4、 main()函数

每个app都有一个最高级的main函数，作为app的入口。 main函数返回void， 有一个可选的List<String>作为参数， 例如：
````
// Run the app like this: dart args.dart 1 test
void main(List<String> arguments) {
  print(arguments);

  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
````

5、函数作为first-class对象

你可以把函数作为一个参数传递给另一个函数， 例如：
````
void printElement(int element) {
  print(element);
}

var list = [1, 2, 3];

// Pass printElement as a parameter.
list.forEach(printElement);

````

你也可以把函数作为一个变量， 例如：
````
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
assert(loudify('hello') == '!!! HELLO !!!');
````

##### 匿名函数
````
([[Type] param1[, …]]) { 
  codeBlock; 
}; 
````
实例：
````
var list = ['apples', 'bananas', 'oranges'];
list.forEach((item) {
  print('${list.indexOf(item)}: $item');
});

````

##### lexical scope 作用域
##### lexical closures 闭包
##### 测试函数相等性
下面是测试顶级函数、静态方法和实例方法是否相等的示例：
````
void foo() {} // A top-level function

class A {
  static void bar() {} // A static method
  void baz() {} // An instance method
}

void main() {
  var x;

  // Comparing top-level functions.
  x = foo;
  assert(foo == x);

  // Comparing static methods.
  x = A.bar;
  assert(A.bar == x);

  // Comparing instance methods.
  var v = A(); // Instance #1 of A
  var w = A(); // Instance #2 of A
  var y = w;
  x = w.baz;

  // These closures refer to the same instance (#2),
  // so they're equal.
  assert(y.baz == x);

  // These closures refer to different instances,
  // so they're unequal.
  assert(v.baz != w.baz);
}
````
#### Operators

Description	| Operator
:-: | :-: 
unary postfix	| expr++    expr--    ()    []    .    ?.
unary prefix	| -expr    !expr    ~expr    ++expr    --expr   
multiplicative	| *    /    %  ~/
additive	| +    -
shift	| <<    >>    >>>
bitwise AND	| &
bitwise XOR	| ^
bitwise OR	| |
relational and type test	| >=    >    <=    <    as    is    is!
equality	| ==    !=   
logical AND	| &&
logical OR	| ||
if null	| ??
conditional	| expr1 ? expr2 : expr3
cascade	| ..
assignment	| =    *=    /=   +=   -=   &=   ^=   etc.

1、 算数运算符
+	| Add
:-: | :-: 
–	| Subtract
-expr	| Unary minus, also known as negation (reverse the sign of the expression)
*	| Multiply
/	| Divide
~/	| Divide, returning an integer result
%	| Get the remainder of an integer division (modulo)

2、前缀和后缀自增自减运算符
Operator	| Meaning
:-: | :-: 
++var	| var = var + 1 (expression value is var + 1)
var++	| var = var + 1 (expression value is var)
--var	| var = var – 1 (expression value is var – 1)
var--	| var = var – 1 (expression value is var)

3、Equality and relational operators
Operator	| Meaning
:-: | :-: 
==	| Equal; see discussion below
!=	| Not equal
>	| Greater than
<	| Less than
>=	| Greater than or equal to
<=	| Less than or equal to

4、类型测试运算符

as, is , is! 运算符用来检查在运行时的类型
Operator	| Meaning
:-: | :-: 
as	| Typecast (also used to specify library prefixes)
is	| True if the object has the specified type
is!	| False if the object has the specified type

5、 Assignment operators
=	| –=	| /=	| %=	| >>=	| ^=
:-: | :-: | :-: | :-: | :-: | :-: 
+=	| *=	| ~/=	| <<=	| &=	| |=

工作方式：
 	| Compound assignment	| Equivalent expression
:-: | :-: | :-:
For an operator op:	| a op= b	| a = a op b
Example:	| a += b	| a = a + b


6、逻辑运算符
Operator	| Meaning
:-: | :-:
!expr	| inverts the following expression (changes false to true, and vice versa)
||	| logical OR
&&	| logical AND

7、位运算符和移位运算符
Operator	| Meaning
:-: | :-:
&	| AND
|	| OR
^	| XOR
~expr	| Unary bitwise complement (0s become 1s; 1s become 0s)
<<	| Shift left
>>	| Shift right

8、条件表达

```condition ? expr1 : expr2```
If condition is true, evaluates expr1 (and returns its value); otherwise, evaluates and returns the value of expr2.
```expr1 ?? expr2```
If expr1 is non-null, returns its value; otherwise, evaluates and returns the value of expr2.

9、Cascade notation (..) 级联符号

示例：
````
querySelector('#confirm') // Get an object.
  ..text = 'Confirm' // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
````
上述代码等价于：
````
var button = querySelector('#confirm');
button.text = 'Confirm';
button.classes.add('important');
button.onClick.listen((e) => window.alert('Confirmed!'));
````

10、其他操作符
Operator	| Name	| Meaning
:-: | :-:| :-:
()	| Function application	| Represents a function call
[]	| List access	| Refers to the value at the specified index in the list
.	| Member access	| Refers to a property of an expression; example: foo.bar selects property bar from expression foo
?.	| Conditional member access	| Like ., but the leftmost operand can be null; example: foo?.bar selects property bar from expression foo unless foo is null (in which case the value of foo?.bar is null) 即，foo可为null

#### Control flow statements
dart代码中控制流程的表达有：
- if and else
- for 
- while and do-while
- break and continue
- switch and case
- assert

1、 Assert

在开发模式中， 使用断言表达式—— assert(condition, optionalMessage); 如果condition的值为false, 中断正常的执行。
断言表达式起作用于：
  - Flutter enables assertions in debug mode
  - Development-only tools such as dartdevc typically enable assertions by default.
  - Some tools, such as dart and dart2js, support assertions through a command-line flag: --enable-asserts. 如：```dart --enable-asserts main.dart ```






















