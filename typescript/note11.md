## 命名空间
TypeScript里， “内部模块”现在称做“命名空间”， “外部模块”现在则简称为“模块”。

使用场景例子：
````
interface StringValidator {
    isAcceptable(s: string): boolean;
}

let lettersRegexp = /^[A-Za-z]+$/;
let numberRegexp = /^[0-9]+$/;

class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}

class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validators: { [s: string]: StringValidator; } = {};
validators["ZIP code"] = new ZipCodeValidator();
validators["Letters only"] = new LettersOnlyValidator();

// Show whether each string passed each validator
for (let s of strings) {
    for (let name in validators) {
        let isMatch = validators[name].isAcceptable(s);
        console.log(`'${ s }' ${ isMatch ? "matches" : "does not match" } '${ name }'.`);
    }
}
````

随着更多验证器的加入， 我们需要一种手段来组织代码，以便于在记录它们类型的同时还不用担心与其它对象产生命名冲突。因此，我们把验证器包裹到一个命名空间内，而不是把他们放在全局命名空间下。

使用命名空间的验证器
````
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    const lettersRegexp = /^[A-Za-z]+$/;
    const numberRegexp = /^[0-9]+$/;

    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }

    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();

// Show whether each string passed each validator
for (let s of strings) {
    for (let name in validators) {
        console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
    }
}


````

### 分离到多文件
当应用变得越来越大时，我们需要将代码分离到不同的文件中以便于维护。

#### 多文件中的命名空间
现在， 我们把Validation命名空间分割成多个文件。尽管是不同的文件，它们仍是同一个命名空间，并且在使用的时候就如同它们在一个文件中定义的一样。因为不同文件之间存在依赖关系，所以我们加入了引用标签来告诉编译器文件之间的关联。例如：
Validation.ts
````
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
````

LettersOnlyValidator.ts
````
/// <reference path="Validation.ts" />
namespace Validation {
    const lettersRegexp = /^[A-Za-z]+$/;
    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }
}
````

ZipCodeValidator.ts
````
/// <reference path="Validation.ts" />
namespace Validation {
    const numberRegexp = /^[0-9]+$/;
    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}
````

Test.ts
````
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();

// Show whether each string passed each validator
for (let s of strings) {
    for (let name in validators) {
        console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
    }
}

````
当涉及到多文件时， 我们必须确保所有编译后的代码都被加载了。我们有两种方式。
第一种方式， 把所有的输入文件编译成一个输出文件，需要使用--outFile标记：
````
tsc --outFile sample.js Test.ts
````
编译器会根据源码里的引用标签自动地对输出进行排序。你也可以单独地指定每个文件。```tsc --outFile sample.js Validation.ts LettersOnlyValidator.ts ZipCodeValidator.ts Test.ts ```

第二种方式， 我们可以编译每一个文件（默认方式）， 那么每个源文件都会对应生成一个JavaScript文件。然后， 在页面上通过```<script>```标签把所有生成的JavaScript文件把正确的顺序引进来，如：
````
<script src="Validation.js" type="text/javascript" />
    <script src="LettersOnlyValidator.js" type="text/javascript" />
    <script src="ZipCodeValidator.js" type="text/javascript" />
    <script src="Test.js" type="text/javascript" />
````

### 别名
另一种简化命名空间操作的方法是使用```import q = x.y.z ```给常用的对象起一个短的名字。不要与用来加载模块的```import x = require('name') ```语法弄混了， 这里的语法是为指定的符号创建一个别名。你可以用这种方法为任意标识符创建别名，也包括导入的模块中的对象。
````
namespace Shapes {
    export namespace Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
let sq = new polygons.Square(); // Same as "new Shapes.Polygons.Square()"
````
* 注意， 我们并没有使用require关键字， 而是直接使用导入符号的限定名赋值， 这与使用var相似，但它还适用于类型和导入的具有命名空间含义的符号。重要的是， 对于值来讲， import会生成原始符号不同的引用，所以改变别名的var值并不会影响原始变量的值。

### 使用其它的JavaScript库
为了描述不是用TypeScript编写的类库的类型，我们需要声明类库导出的API。 由于大部分程序库只提供了少数的顶级对象， 命名空间是用来表示它们的一个好办法。

我们称其为声明时因为它不是外部程序的具体实现。我们通常在.d.ts里写这些声明。

## 命名空间和模块

### 使用命名空间
命名空间是位于全局命名空间下的一个普通的带有名字的JavaScript对象。这令命名空间十分容易使用。 它们可以在多文件中同时使用，并通过--outFile结合在一起。命名空间是帮你阻止web应用不错的方式， 你可以把所有依赖都放在HTML页面的```<script> ```标签里。

但就像其它的全局命名空间污染一样，它很难去识别组件之间的依赖关系，尤其是在大型的应用中。

### 使用模块
像命名空间一样，模块可以包含代码和声明。不同的是模块可以声明它的依赖。

模块会把依赖添加到模块加载器上（例如CommonJs / Require.js）。 对于小型的JS应用来说可能没必要，但是对于大型应用，这一点点的花费会带来长久的模块化和可维护性上的便利。 模块也提供了更好的代码重用，更强的封闭性以及更好的使用工具进行优化。

对于Node.js应用来说，模块是默认并推荐的组织代码的方式。

### 命名空间和模块的陷阱
- 对模块使用/// <reference>
  一个常见的错误是使用/// <reference>引用模块文件，应该使用import。 要理解这之间的区别，我们首先应该弄清编译器是如何根据 import路径（例如，import x from "...";或import x = require("...")里面的...，等等）来定位模块的类型信息的。
  编译器首先尝试去查找相应路径下的.ts，.tsx再或者.d.ts。 如果这些文件都找不到，编译器会查找 外部模块声明。 回想一下，它们是在 .d.ts文件里声明的。
  myModules.d.ts
  ````
  // In a .d.ts file or .ts file that is not a module:
  declare module "SomeModule" {
      export function fn(): string;
  }
  ````
  myOtherModule.ts
  ````
  /// <reference path="myModules.d.ts" />
  import * as m from "SomeModule";
  ````
  这里的引用标签指定了外来模块的位置。 这就是一些TypeScript例子中引用 node.d.ts的方法。
- 不必要的命名空间
  如果你想把命名空间转换为模块，它可能会像下面这个文件一样：
  shapes.ts
  ````
  export namespace Shapes {
    export class Triangle { /* ... */ }
    export class Square { /* ... */ }
  }
  ````  
  顶层的模块Shapes包裹了Triangle和Square。 对于使用它的人来说这是令人迷惑和讨厌的：
  shapeConsumer.ts
  ````
  import * as shapes from "./shapes";
  let t = new shapes.Shapes.Triangle(); // shapes.Shapes?

  ````
  