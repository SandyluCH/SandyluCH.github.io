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
  
## 声明合并

"声明合并"是TypeScript 在类型层面上描述JavaScript对象的模型独特概念。

声明合并是指编译器将针对同一个名字的两个独立声明合并为单一声明。合并后的声明同时拥有原先两个声明的特性。 任何数量的声明都可被合并；不局限于两个声明。

TypeScript中的声明会创建以下3中实体之一： 命名空间、类型或值。 创建命名空间的声明会新建一个命名空间，它包含了用（.）符号来访问时使用的名字。创建类型的声明是： 用声明的模型创建一个类型并绑定到给定的名字上。创建值的声明会创建在JavaScript输出中看到的值。

### 合并接口
从根本上说，合并的机制是把双方的成员放到一个同名的接口里。
````
interface Box {
    height: number;
    width: number;
}

interface Box {
    scale: number;
}

let box: Box = {height: 5, width: 6, scale: 10};

````
接口的非函数的成员应该是唯一的。如果它们不是唯一的，那么它们必须是相同的类型。如果两个接口中同时声明了同名的非函数成员且它们的类型不同，则编译器会报错。

对于函数成员，每个同名函数声明都会被当成这个函数的一个重载。同时需要注意，当接口A与后台的接口A合并时，后面的接口具有更高的优先级。

如下例所示：
````
interface Cloner {
    clone(animal: Animal): Animal;
}

interface Cloner {
    clone(animal: Sheep): Sheep;
}

interface Cloner {
    clone(animal: Dog): Dog;
    clone(animal: Cat): Cat;
}

````
这三个接口合并成一个声明：
````
interface Cloner {
    clone(animal: Dog): Dog;
    clone(animal: Cat): Cat;
    clone(animal: Sheep): Sheep;
    clone(animal: Animal): Animal;
}
````
* 注意每组接口里的声明顺序保持不变， 但各组接口之间的顺序是后台的接口重载出现在靠前的位置。

这个规则有一个例外是当出现特殊的函数签名时，如果签名里有一个参数类型是单一的字符串字面量（比如，不是字符串字面量的联合类型），那么它将会被提升到重载列表的最顶端。

例如：
````
interface Document {
    createElement(tagName: any): Element;
}
interface Document {
    createElement(tagName: "div"): HTMLDivElement;
    createElement(tagName: "span"): HTMLSpanElement;
}
interface Document {
    createElement(tagName: string): HTMLElement;
    createElement(tagName: "canvas"): HTMLCanvasElement;
}

````
合并后的Document将会像下面这样：
````
interface Document {
    createElement(tagName: "canvas"): HTMLCanvasElement;
    createElement(tagName: "div"): HTMLDivElement;
    createElement(tagName: "span"): HTMLSpanElement;
    createElement(tagName: string): HTMLElement;
    createElement(tagName: any): Element;
}

````

### 合并命名空间

与接口相似，同名的命名空间也会合并其成员。命名空间会创建出命名空间和值， 对于命名空间的合并，模块导出的同名接口进行合并，构成单一命名空间内含合并后的接口。

对于命名空间里值的合并， 如果当前已经存在给定名字的命名空间，那么后来的命名空间的导出成员会被加到已经存在的那个模块里。

示例：
````
namespace Animals {
    export class Zebra { }
}

namespace Animals {
    export interface Legged { numberOfLegs: number; }
    export class Dog { }
}
````
合并后：
````
namespace Animals {
    export interface Legged { numberOfLegs: number; }

    export class Zebra { }
    export class Dog { }
}

````
除了这些合并外，你还需要了解非导出成员是如何处理的。 非导出成员仅在其原有的（合并前的）命名空间内可见。这就是说合并之后，从其它命名空间合并进来的成员无法访问非导出成员。

例如：
````
namespace Animal {
    let haveMuscles = true;

    export function animalsHaveMuscles() {
        return haveMuscles;
    }
}

namespace Animal {
    export function doAnimalsHaveMuscles() {
        return haveMuscles;  // Error, because haveMuscles is not accessible here
    }
}
````
例子中，因为haveMuscles并没有导出，只有animalsHaveMuscles函数共享了原始未合并的命名空间可以访问这个变量。doAnimalsHaveMuscles函数虽然是合并命名空间的一部分，但是访问不了未导出的成员。

### 命名空间与类和函数和枚举类型合并
命名空间可以与其他类型的声明进行合并。只要命名空间的定义符合将要合并类型的定义。合并结果包含两者的声明类型，TypeScript使用这个功能去实现一些JavaScript里的设计模式。

#### 合并命名空间和类
这让我们可以表示内部类。
````
class Album {
    label: Album.AlbumLabel;
}
namespace Album {
    export class AlbumLabel { }
}

````
合并规则与上面合并命名空间小节里讲的规则一直，我们必须导出AlbumLabel类， 好让合并的类能访问。合并结果是一个类并带有一个内部类。你也可以使用命名空间为类增加一些静态属性。

除了内部类的模式， 你在JavaScript里，创建一个函数稍后扩展它增加一些属性也是很常见的。TypeScript使用声明合并来达到这个目的并保证类型安全。
````
function buildLabel(name: string): string {
    return buildLabel.prefix + name + buildLabel.suffix;
}

namespace buildLabel {
    export let suffix = "";
    export let prefix = "Hello, ";
}

console.log(buildLabel("Sam Smith"));
````
相似的，命名空间可以用来扩展枚举型：
````
enum Color {
    red = 1,
    green = 2,
    blue = 4
}

namespace Color {
    export function mixColor(colorName: string) {
        if (colorName == "yellow") {
            return Color.red + Color.green;
        }
        else if (colorName == "white") {
            return Color.red + Color.green + Color.blue;
        }
        else if (colorName == "magenta") {
            return Color.red + Color.blue;
        }
        else if (colorName == "cyan") {
            return Color.green + Color.blue;
        }
    }
}

````

### 非法的合并
TypeScript并非允许所有的合并。目前，类不能与其它类或变量合并。
