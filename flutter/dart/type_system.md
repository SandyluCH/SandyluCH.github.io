## dart的类型系统
dart语言是一门类型安全的语言。它结合了静态类型检查和运行时检查来确保变量的值总是匹配变量的static type。 尽管type是强制的，但type annotations是可选的，因为有类型推断。

### 静态类型检查可以在编译时使用dart的静态分析器找出bug；

  你可以向泛型类添加类型annotation来解决大多数静态分析错误。

### 稳健性

  稳健性是为了确保程序不会进入某些无效状态。可靠的系统意味着你永远不会进入表达式的计算结果与表达式的静态类型不匹配的状态。例如，如果表达式的静态类型是String, 那么在运行时， 您讲保证只有在计算字符串时才能获得该字符串。

  dart 类型系统，就像java 和c#中的类型系统，是可靠的。它使用静态检查（编译时错误）和运行时检查相结合来加强这种稳定性。例如，将字符串赋给int是编译时错误。如果对象不是字符串，则使用as字符串将对象强制转换为字符串失败，并出现运行时错误。
#### 稳健性的好处
- 在编译时揭示类型相关的bug
- 代码更有可读性；
- 代码更易维护；
- 更好的AOT提前编译；

### 静态分析的温馨提示
大多数的静态类型的规则是很容易理解的。这里有一些不太明显的规则：
- use sound return types when overriding methods
- use sound parameter types when overriding methods
- don't use a dynamic list as a typed list

#### 规则1：use sound return types when overriding methods
![avatar](https://dart.dev/guides/language/images/type-hierarchy.png)

例如父类：
````
class Animal{
  void chase(Animal a){}
  Animal get parent => ...
}

````
子类HoneyBadger写法：
````
//正确写法
class HoneyBadger extends Animal {
  void chase(Animal a) { ... }
  HoneyBadger get parent => ...
}

// 错误写法
class HoneyBadger extends Animal {
  void chase(Animal a) { ... }
  Root get parent => ...
}

````
因为Root是相关的类型

#### 规则2：Use sound parameter types when overriding methods
重写方法的参数必须具有超类中相应参数的相同类型或超类型。不要用原始参数的子类型替换这个类型来tighten 参数类型。

如果您有充分的理由使用子类型，那么可以使用covariant关键字。

例如父类：
````
class Animal {
  void chase(Animal a) { ... }
  Animal get parent => ...
}
````
子类写法：
````
//正确写法
class HoneyBadger extends Animal {
  void chase(Object a) { ... }
  Animal get parent => ...
}

//错误写法
class Mouse extends Animal {...}

class Cat extends Animal {
  void chase(Mouse x) { ... }
}


````

#### 规则3：don't use a dynamic list as a typed list
当你想有不同类型数据的list时，动态list是好的选择。然而你不能用动态list作为一个typed list。
这个规则同样是适用于泛型类的实例。
例子：
````
class Cat extends Animal { ... }

class Dog extends Animal { ... }

void main() {
  List<Cat> foo = <dynamic>[Dog()]; // Error
  List<dynamic> bar = <dynamic>[Dog(), Cat()]; // OK
}

````
### 运行时检查

运行时检查工具像Dart VM 和 dartdevc

### 类型推断
分析器可以推断fields , methods, local variables, 和大多数泛型类型的参数。当分析器没有足够的信息去推断特定的类型时，可以使用dynamic 类型。

例如：
```Map<String, dynamic> arguments = { 'argA':'hello', 'argB':42 } ```
或者： ```var arguments = {'argA': 'hello', 'argB': 42}; // Map<String, Object>  ```