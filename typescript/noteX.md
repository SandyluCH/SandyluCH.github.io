## 模块
模块在其自身的作用域里执行，而不是在全局作用域里；这意味着定义在一个模块里的变量，函数，类等等在模块外部是不可见的，除非你明确地使用export形式之一导出它们。 相反，如果想使用其它模块导出的变量，函数，类，接口等的时候，你必须要导入它们，可以使用 import形式之一。

模块是自声明的；两个模块之间的关系是通过在文件级别上使用imports和exports建立的。

模块使用模块加载器去导入其它的模块。 在运行时，模块加载器的作用是在执行此模块代码前去查找并执行这个模块的所有依赖。 大家最熟知的JavaScript模块加载器是服务于Node.js的 CommonJS和服务于Web应用的Require.js。

TypeScript与ECMAScript 2015一样，任何包含顶级import或者export的文件都被当成一个模块。相反地，如果一个文件不带有顶级的import或者export声明，那么它的内容被视为全局可见的（因此对模块也是可见的）。

### export
#### 导出声明
任何声明（比如变量，函数，类，类型别名或接口）都能够通过添加export关键字来导出。
如：
````
export interface StringValidator {
    isAcceptable(s: string): boolean;
}

export const numberRegexp = /^[0-9]+$/;
export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}

````

#### 导出语句
导出语句很遍历，因为我们可能需要对导出的部分重命名， 所以上面的例子可以改写：
````
class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export { ZipCodeValidator };
export { ZipCodeValidator as mainValidator };

````
#### 重新导出
我们经常会去扩展其它模块，并且只导出那个模块的部分内容。重新导出功能并不会在当前模块导入那个模块或定义一个新的局部变量。

ParseIntBasedZipCodeValidator.ts
````
export class ParseIntBasedZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && parseInt(s).toString() === s;
    }
}

// 导出原先的验证器但做了重命名
export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";

````
或者一个模块可以包裹多个模块，并把他们导出的内容联合在一起通过语法：``` export * from "module" ```。
AllValidators.ts
````
export * from "./StringValidator"; // exports interface StringValidator
export * from "./LettersOnlyValidator"; // exports class LettersOnlyValidator
export * from "./ZipCodeValidator";  // exports class ZipCodeValidator

````

### 导入
模块的导入操作与导出一样简单。可以使用一下import形式之一来导入其它模块中的导出内容。
#### 导入一个模块中的某个导出内容
````
import { ZipCodeValidator } from "./ZipCodeValidator";

let myValidator = new ZipCodeValidator();

````
可以对导入内容重名
````
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
let myValidator = new ZCV();

````
#### 将整个模块导入到一个变量，并通过它来访问模块的导出部分
````
import * as validator from "./ZipCodeValidator";
let myValidator = new validator.ZipCodeValidator();

````
#### 具有副作用的导入模块
一些模块会设置一些全局状态供其它模块使用。这些模块可能没有任何的导出或用户根本就不关注它的导出。使用下面的方法来导入这类模块：
```` 
import './my-module.js';

````
### 默认导出
每个模块都可以有一个default导出。默认导出使用default关键字标记；并且一个模块只能够有一个default导出。需要使用一种特殊的导入形式来导入default导出。
````
// 例子1
declare let $: JQuery;
export default $;

import $ from "JQuery";
$("button.continue").html( "Next Step..." );

// 例子2
export default class ZipCodeValidator {
    static numberRegexp = /^[0-9]+$/;
    isAcceptable(s: string) {
        return s.length === 5 && ZipCodeValidator.numberRegexp.test(s);
    }
}

import validator from "./ZipCodeValidator";
let myValidator = new validator();

// 例子3
const numberRegexp = /^[0-9]+$/;
export default function (s: string) {
    return s.length === 5 && numberRegexp.test(s);
}

import validate from "./StaticZipCodeValidator";
let strings = ["Hello", "98052", "101"];
// Use function validate
strings.forEach(s => {
  console.log(`"${s}" ${validate(s) ? " matches" : " does not match"}`);
});

// 例子4
export default "123";

import num from "./OneTwoThree";
console.log(num); // "123"

````

#### ```export =``` 和 ```import = require() ```
CommonJS和AMD的环境里都有一个exports变量，这个变量包含了一个模块的所有导出内容。

CommonJS和AMD的exports都可以被赋值为一个对象， 这种情况下其作用就类似于es6语法里的默认导出， 即export default语法。 虽然作用相似，但是export default语法并不能兼容CommonJS和AMD的exports。

为了支持CommonJS和AMD的exports, TypeScript提供了```export = ```语法。

```export = ```语法定义一个模块的导出对象。这里的对象一词指的是类，接口，命名空间，函数或枚举。

若使用```export = ```导出一个模块，则必须使用TypeScript的特定语法```import module = require("module") ```来导入此模块。

ZipCodeValidator.ts
````
let numberRegexp = /^[0-9]+$/;
class ZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;

````
test.ts
````
import zip = require("./ZipCodeValidator");

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validator = new zip();

// Show whether each string passed each validator
strings.forEach(s => {
  console.log(`"${ s }" - ${ validator.isAcceptable(s) ? "matches" : "does not match" }`);
});

````

### 生成模块代码
根据编译时指定的模块目标参数，编译器会生成相应的供Node.js (CommonJS)，Require.js (AMD)，UMD，SystemJS或ECMAScript 2015 native modules (ES6)模块加载系统使用的代码。 想要了解生成代码中 define，require 和 register的意义，请参考相应模块加载器的文档。

下面的例子说明了导入导出语句里使用的名字是怎么转换为相应的模块加载器代码的。
SimpleModule.ts
````
import m = require("mod");
export let t = m.something + 1;
````

AMD / RequireJS SimpleModule.js
````
define(["require", "exports", "./mod"], function (require, exports, mod_1) {
    exports.t = mod_1.something + 1;
});
````

CommonJS / Node SimpleModule.js
````
let mod_1 = require("./mod");
exports.t = mod_1.something + 1;
````

UMD SimpleModule.js
````
(function (factory) {
    if (typeof module === "object" && typeof module.exports === "object") {
        let v = factory(require, exports); if (v !== undefined) module.exports = v;
    }
    else if (typeof define === "function" && define.amd) {
        define(["require", "exports", "./mod"], factory);
    }
})(function (require, exports) {
    let mod_1 = require("./mod");
    exports.t = mod_1.something + 1;
});
````

System SimpleModule.js
````
System.register(["./mod"], function(exports_1) {
    let mod_1;
    let t;
    return {
        setters:[
            function (mod_1_1) {
                mod_1 = mod_1_1;
            }],
        execute: function() {
            exports_1("t", t = mod_1.something + 1);
        }
    }
});
````

Native ECMAScript 2015 modules SimpleModule.js
````
import { something } from "./mod";
export let t = something + 1;
````

#### 简单示例
每个莫夸只有一个命名的导出。
为了编译， 我们必须要在命令行上指定一个模块目标。对于Node.js来说，使用```--module commonjs ```; 对于Require.js来说，使用```--module amd ```。 比如
````
tsc --module commonjs Test.ts
````

### 可选的模块加载和其他高级加载场景
有时候，你只想在某种条件下才加载某个模块。 在TypeScript里，使用下面的方式来实现它和其它的高级加载场景，我们可以直接调用模块加载器并且可以保证类型完全。

编译器会检测是否每个模块都会在生成的JavaScript中用到。 如果一个模块标识符只在类型注解部分使用，并且完全没有在表达式中使用时，就不会生成 require这个模块的代码。 省略掉没有用到的引用对性能提升是很有益的，并同时提供了选择性加载模块的能力。

这种模式的核心是```import id = require("...")```语句可以让我们访问模块导出的类型。 模块加载器会被动态调用（通过 require），就像下面if代码块里那样。 它利用了省略引用的优化，所以模块只在被需要时加载。 为了让这个模块工作，一定要注意 import定义的标识符只能在表示类型处使用（不能在会转换成JavaScript的地方）。

为了确保类型安全性，我们可以使用typeof关键字。typeof关键字，当在表示类型的地方使用时，会得出一个类型值，这里就标识模块的类型，例如：
示例：Node.js里的动态模块加载
````
declare function require(moduleName: string): any;
import { ZipCodeValidator as Zip } from "./ZipCodeValidator";
if (needZipValidation) {
    let ZipCodeValidator: typeof Zip = require("./ZipCodeValidator");
    let validator = new ZipCodeValidator();
    if (validator.isAcceptable("...")) { /* ... */ }
}
````

示例：require.js里的动态模块加载
````
declare function require(moduleNames: string[], onLoad: (...args: any[]) => void): void;

import * as Zip from "./ZipCodeValidator";

if (needZipValidation) {
    require(["./ZipCodeValidator"], (ZipCodeValidator: typeof Zip) => {
        let validator = new ZipCodeValidator.ZipCodeValidator();
        if (validator.isAcceptable("...")) { /* ... */ }
    });
}
````

示例：System.js里的动态模块加载
````
declare const System: any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
    System.import("./ZipCodeValidator").then((ZipCodeValidator: typeof Zip) => {
        var x = new ZipCodeValidator();
        if (x.isAcceptable("...")) { /* ... */ }
    });
}
````

### 使用其他JavaScript库
要想描述非TypeScript编写的类库的类型， 我们需要声明类库所暴露出的API。

我们叫它声明 ，因为它不是外部程序的具体实现。它们通常是在``` .d.ts```文件里定义的。

#### 外部模块
在Node.js里大部分工作是通过加载一个或多个模块实现的。 我们可以使用顶级的 export声明来为每个模块都定义一个.d.ts文件，但最好还是写在一个大的.d.ts文件里。 我们使用与构造一个外部命名空间相似的方法，但是这里使用 module关键字并且把名字用引号括起来，方便之后import。 例如：
node.d.ts (simplified excerpt)
````
declare module "url" {
    export interface Url {
        protocol?: string;
        hostname?: string;
        pathname?: string;
    }

    export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}

declare module "path" {
    export function normalize(p: string): string;
    export function join(...paths: any[]): string;
    export let sep: string;
}
````
现在我们可以/// <reference> node.d.ts并且使用import url = require("url");或import * as URL from "url"加载模块。
````
/// <reference path="node.d.ts"/>
import * as URL from "url";
let myUrl = URL.parse("http://www.typescriptlang.org");
````

##### 外部模块简写
可以采用声明的简写形式以便能够快速使用它。
declarations.d.ts
````
declare module "hot-new-module";
````
简写模块里所有导出的类型将是any。
````
import x, {y} from "hot-new-module";
x(y);
````

##### 模块声明通配符
某些模块加载器如SystemJS和AMD支持导入非JavaScript内容。它们通常会使用一个前缀或后缀来表示特殊的加载语法。 模块声明通配符可以用来表示这些情况。
````
declare module "*!text" {
    const content: string;
    export default content;
}
// Some do it the other way around.
declare module "json!*" {
    const value: any;
    export default value;
}

````
现在你可以就导入匹配"*!text" 或"json!*" 的内容了。
````
import fileContent from "./xyz.txt!text";
import data from "json!http://example.com/data.json";
console.log(data, fileContent);

````
#### UMD模块
有些模块被设计成兼容多个模块加载器， 或者不使用模块加载器（全局变量）。它们以UMD模块为代表。这些库可以通过导入的形式或全局变量的形式访问。例如：
math-lib.d.ts
````
export function isPrime(x: number): boolean;
export as namespace mathLib;
````
之后，这个库可以在某个模块里通过导入来使用：
````
import { isPrime } from "math-lib";
isPrime(2);
mathLib.isPrime(2); // 错误: 不能在模块内使用全局定义。

````
它同样可以通过全局变量的形式使用， 但是只能在某个脚本（指不带有模块导入或导出的脚本文件）里。
````
mathLib.isPrime(2);

````

### 创建模块结构指导
- 尽可能地在顶层导出

用户应该更容易地使用你模块导出的内容。 嵌套层次过多会变得难以处理，因此仔细考虑一下如何组织你的代码。

从你的模块中导出一个命名空间就是一个增加嵌套的例子。 虽然命名空间有时候有它们的用处，在使用模块的时候它们额外地增加了一层。 这对用户来说是很不便的并且通常是多余的。

导出类的静态方法也有同样的问题 - 这个类本身就增加了一层嵌套。 除非它能方便表述或便于清晰使用，否则请考虑直接导出一个辅助方法。

- 如果仅导出单个class或function, 使用export default

- 如果要导出多个对象，把他们放在顶层里导出

- 明确地列出导入的名字
````
import { SomeType, someFunc } from "./MyThings";
let x = new SomeType();
let y = someFunc();
````
- 使用命名空间导入模式挡你要导出大量内容的时候
````
export class Dog { ... }
export class Cat { ... }
export class Tree { ... }
export class Flower { ... }
````
- 使用重新导出进行扩展
  你可能经常需要去扩展一个模块的功能。 JS里常用的一个模式是JQuery那样去扩展原对象。 如我们之前提到的，模块不会像全局命名空间对象那样去 合并。 推荐的方案是 不要去改变原来的对象，而是导出一个新的实体来提供新的功能。
  假设Calculator.ts模块里定义了一个简单的计算器实现。 这个模块同样提供了一个辅助函数来测试计算器的功能，通过传入一系列输入的字符串并在最后给出结果。
  Calculator.ts
  ````
  export class Calculator {
    private current = 0;
    private memory = 0;
    private operator: string;
    protected processDigit(digit: string, currentValue: number) {
        if (digit >= "0" && digit <= "9") {
            return currentValue * 10 + (digit.charCodeAt(0) - "0".charCodeAt(0));
        }
    }
    protected processOperator(operator: string) {
        if (["+", "-", "*", "/"].indexOf(operator) >= 0) {
            return operator;
        }
    }
    protected evaluateOperator(operator: string, left: number, right: number): number {
        switch (this.operator) {
            case "+": return left + right;
            case "-": return left - right;
            case "*": return left * right;
            case "/": return left / right;
        }
    }
    private evaluate() {
        if (this.operator) {
            this.memory = this.evaluateOperator(this.operator, this.memory, this.current);
        }
        else {
            this.memory = this.current;
        }
        this.current = 0;
    }
    public handleChar(char: string) {
        if (char === "=") {
            this.evaluate();
            return;
        }
        else {
            let value = this.processDigit(char, this.current);
            if (value !== undefined) {
                this.current = value;
                return;
            }
            else {
                let value = this.processOperator(char);
                if (value !== undefined) {
                    this.evaluate();
                    this.operator = value;
                    return;
                }
            }
        }
        throw new Error(`Unsupported input: '${char}'`);
    }
    public getResult() {
        return this.memory;
    }
  }
  export function test(c: Calculator, input: string) {
      for (let i = 0; i < input.length; i++) {
          c.handleChar(input[i]);
      }
      console.log(`result of '${input}' is '${c.getResult()}'`);
  }
  ````
  下面使用导出的test函数来测试计算器。
  TestCalculator.ts
  ````
  import { Calculator, test } from "./Calculator";
  let c = new Calculator();
  test(c, "1+2*33/11="); // prints 9
  ````
  现在扩展它， 添加支持输入其它进制（十进制以外）， 创建ProgrammerCalculator.ts。
  ProgrammerCalculator.ts
  ````
  import { Calculator } from "./Calculator";
  class ProgrammerCalculator extends Calculator {
      static digits = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "A", "B", "C", "D", "E", "F"];
      constructor(public base: number) {
          super();
          const maxBase = ProgrammerCalculator.digits.length;
          if (base <= 0 || base > maxBase) {
              throw new Error(`base has to be within 0 to ${maxBase} inclusive.`);
          }
      }
      protected processDigit(digit: string, currentValue: number) {
          if (ProgrammerCalculator.digits.indexOf(digit) >= 0) {
              return currentValue * this.base + ProgrammerCalculator.digits.indexOf(digit);
          }
      }
    }
    // Export the new extended calculator as Calculator
    export { ProgrammerCalculator as Calculator };
    // Also, export the helper function
    export { test } from "./Calculator";
  ````
  新的ProgrammerCalculator模块导出的API与原先的Calculator的模块很相似， 但却没有改变原模块里的对象。
  下面是测试ProgrammerCalculator类的代码：
  TestProgrammerCalculator.ts
  ````
  import { Calculator, test } from "./ProgrammerCalculator";
  let c = new Calculator(2);
  test(c, "001+010="); // prints 3
  ````
- 模块里不要使用命名空间
  当初此进入基于模块的开发模式时， 可能总会控制不住要将导出包裹在一个命名空间里。 模块具有其自己的作用域， 并且只有导出的声明才会在模块外部可见。记住这点，命名空间在使用模块时几乎没什么价值。
  在组织方面，命名空间对于在全局作用域内对逻辑上相关的对象和类型进行分组是很便利的。
- 危险信号
  以下均为模块结构上的危险信号。重新检查以确保你没有在对模块使用命名空间：
  + 文件的顶层声明是export namespace Foo { ... } （删除Foo并把所有内容向上层移动一层）
  + 文件只有一个export class或export function （考虑使用export default）
  + 多个文件的顶层具有同样的export namespace Foo { （不要以为这些会合并到一个Foo中！）

