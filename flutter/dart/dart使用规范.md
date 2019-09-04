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