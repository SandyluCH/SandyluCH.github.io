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


### 最好使用小驼峰
