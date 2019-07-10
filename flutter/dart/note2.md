## Exceptions 异常

1、Throw
抛出异常

``` throw FormatException('Expected at least 1 section')  ```

2、 Catch捕捉异常
````
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // A specific exception
  buyMoreLlamas();
} on Exception catch (e) {
  // Anything else that is an exception
  print('Unknown exception: $e');
} catch (e) {
  // No specified type, handles all
  print('Something really unknown: $e');
}
````

异常冒泡使用rethrow
````
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // Runtime error
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    rethrow; // Allow callers to see the exception.
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
````

3、 Finally

为了确保有些代码运行是否抛出异常，使用finally 分句。如果没有catch分句匹配这个异常，这个异常将会冒泡到finally分句中，在finally分句中执行， 例如：
````
try {
  breedMoreLlamas();
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}

````

finally分句在任何一个匹配catch分句运行之后 运行，
````
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // Handle the exception first.
} finally {
  cleanLlamaStalls(); // Then clean up.
}

````






## Classes
Dart是一种面向对象的语言， 具有类和基于混合的继承。每个对象都是一个类的实例，并且所有类都是从对象派生的。基于mixin的继承意味着尽管每个类（对象除外）只有一个超类， 但是一个类体可以在多个类层次结构中重用。

获取一个object的类型，可以使用对象的```runtimeType```属性， 如：
````
print('The type of a is ${a.runtimeType}');
````

如果没有定义构造函数，会有一个默认构造函数（无参数）

子类的构造函数并不从他的父类那里继承构造函数。子类如果没有定义构造函数，它将只有一个默认构造函数（no argument, no name）

### 构造函数
````
class Point {
    num x;
    num y;
    // 构造函数
    Point(num x, num y) {
      // There's a better way to do this, stay tuned.
      this.x = x;
      this.y = y;
    }
}

````
上述代码可简化
````
class Point{
  num x;
  num y;
  Point(this.x, this.y);
}

````

- Named constructors 
使用命名构造函数为一个类实现多个构造函数

使用命名构造函数从另一类或现有的数据中快速实现构造函数

````
class Point {
   num x;
   num y;

   Point(this.x, this.y);

   // 命名构造函数Named constructor
   Point.fromJson(Map json) {
     x = json['x'];
     y = json['y'];
   }
}

````
构造函数不能被继承，父类中的命名构造函数不能被子类继承。如果想要子类也拥有一个父类一样名字，必须在子类中实现这个构造函数

 调用父类的非默认构造函数 
默认情况下，子类只能调用父类的默认构造函数（无名，无参数的构造函数）； 父类的无名构造函数会在子类的构造函数前调用； 如果
如果initializer list 也同时定义了，则会先执行initializer list 中的内容，然后在执行父类的无名无参数构造函数，最后调用子类自己的无名无参数构造函数。即下面的顺序：
  - 1、initializer list（初始化列表）
  - 2、super class’s no-arg constructor（父类无参数构造函数）
  - 3、main class’s no-arg constructor （主类无参数构造函数）

如果父类不显示提供无名无参数构造函数的构造函数，在子类中必须手打调用父类的一个构造函数。这种情况下，调用父类的构造函数的代码放在子类构造函数名后，子类构造函数体前，中间使用:(colon) 分割。
````
class Person {
   String firstName;

   Person.fromJson(Map data) {
       print('in Person');
   }
}

class Employee extends Person {
   // 父类没有无参数的非命名构造函数，必须手动调用一个构造函数     
   super.fromJson(data)
   Employee.fromJson(Map data) : super.fromJson(data) {
      print('in Employee');
   }
}

main() {
   var emp = new Employee.fromJson({});

   // Prints:
   // in Person
   // in Employee
   if (emp is Person) {
     // Type check
     emp.firstName = 'Bob';
   }
   (emp as Person).firstName = 'Bob';
}

````

- 静态构造函数

如果你的类产生的对象永远不会改变，你可以让这些对象成为编译时常量。为此， 需要定义一个const 构造函数并确保所有的实例变量都是final的。
````
class ImmutablePoint {
    final num x;
    final num y;
    const ImmutablePoint(this.x, this.y);
    static final ImmutablePoint origin = const ImmutablePoint(0, 0);
}

````

- 重定向构造函数

有时候构造函数的目的只是重定向到该类的另一个构造函数。重定向构造函数没有函数体，使用冒号:分隔。
````
class Point {
    num x;
    num y;

    // 主构造函数
    Point(this.x, this.y) {
        print("Point($x, $y)");
    }

    // 重定向构造函数，指向主构造函数，函数体为空
    Point.alongXAxis(num x) : this(x, 0);
}

void main() {
    var p1 = new Point(1, 2);
    var p2 = new Point.alongXAxis(4);
}

````

- 常量构造函数

如果类的对象不会发生变化， 可以构造一个编译时的常量构造函数。定义格式如下：定义所有的实例变量是final 以及使用const声明构造函数
````
class ImmutablePoint {
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);

  final num x, y;

  const ImmutablePoint(this.x, this.y);
}
````

- 工厂构造函数

当实现一个使用factory关键词修饰的构造函数时， 这个构造函数不必创建类的新实例。例如， 工厂构造函数可能从缓存返回实例， 或者它可能返回子类型的实例。

下面例子是工厂函数时从缓存中返回实例
````
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to
  // the _ in front of its name.
  static final Map<String, Logger> _cache =
      <String, Logger>{};

  factory Logger(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}

````


### methods 方法

1、 实例方法
````
import 'dart:math';

class Point {
  num x, y;

  Point(this.x, this.y);

  num distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}

````

2、Getters and Setters
````
class Rectangle {
  num left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // Define two calculated properties: right and bottom.
  num get right => left + width;
  set right(num value) => left = value - width;
  num get bottom => top + height;
  set bottom(num value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}

````

3、抽象方法




