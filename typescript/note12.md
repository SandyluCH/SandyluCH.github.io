## Mixins

例子
````
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }

}

// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}

class SmartObject implements Disposable, Activatable {
    constructor() {
        setInterval(() => console.log(this.isActive + " : " + this.isDisposed), 500);
    }

    interact() {
        this.activate();
    }

    // Disposable
    isDisposed: boolean = false;
    dispose: () => void;
    // Activatable
    isActive: boolean = false;
    activate: () => void;
    deactivate: () => void;
}
applyMixins(SmartObject, [Disposable, Activatable]);

let smartObj = new SmartObject();
setTimeout(() => smartObj.interact(), 1000);

////////////////////////////////////////
// In your runtime library somewhere
////////////////////////////////////////

function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name];
        });
    });
}

````
例子里定义了两个类，每个类都只定义了一个特定的行为或功能。
````
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }

}

// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}

````
下面创建一个类， 结合了这两个mixins。
````
class SmartObject implements Disposable, Activatable {

````
首先应该注意到的是， 没有使用extends而是使用implements。 把类当成了接口， 仅使用Disposable和Activatable的类型而非其实现。 这意味着我们需要在类里面实现接口。但是这是我们在用mixin时想避免的。

我们可以这么做来达到目的，为将要mixin进来的属性方法创建出占位属性。这告诉编译器这些成员在运行时是可用的。这样就能使用mixin带来的遍历， 虽说需要提前定义一些占位属性。
````
// Disposable
isDisposed: boolean = false;
dispose: () => void;
// Activatable
isActive: boolean = false;
activate: () => void;
deactivate: () => void;

````
最后，把mixins混入定义的类，完成全部实现的部分。
````
applyMixins(SmartObject, [Disposable, Activatable]);
````
最后创建这个帮助函数，帮我们做混入操作。它会遍历mixins上的所有属性， 并复制到目标上，把之前的占位属性替换成真正的实现代码。
````
function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name];
        })
    });
}

````


## 三斜线指令
三斜线指令是包含单个XML标签的单行注释。注释的内容会做为编译器指令使用。

三斜线指令仅可放在包含它的文件的最顶端。一个三斜线指令的前面只能出现单行或多行注释， 这包括其他的三斜线指令。如果他们出现在一个语句或声明之后， 那么它们会被当做普通的单行注释，并且不具有特殊的含义。
### ````/// <reference path="..."> ````
```/// <reference path="..." /> ```指令是三斜线指令中最常见的一种。它用于声明文件间的依赖。

三斜线引用告诉编译器在编译过程中要引入的额外的文件。

当使用--out或--outFile时，它也可以作为调整输出内容顺序的一种方法。文件在输出文件内容中的位置与经过预处理后的输入顺序一致。


#### 预处理输入文件
编译器会对输入文件进行预处理来解析所有三斜线引用指令。在这个过程中，额外的文件会加到编译过程中。

这个过程会以一些根文件开始；它们是在命令行中指定的文件或是在tsconfig.json中的files 列表里的文件。 这些根文件按指定的顺序进行预处理。在一个文件被加入列表前，它包含的所有三斜线引用都要被处理， 还有他们包含的目标。三斜线引用以它们在文件里出现的顺序，使用深度优先的方式解析。

一个三斜线引用路径是相对于包含它的文件的，如果不是根文件。

#### 错误
引用不存在的文件会报错。一个文件用三斜线指令引用自己会报错。

#### 使用```--noResolve ```
如果指定了```--noResolve ```编译选项， 三斜线引用会被忽略；他们不会增加新文件，也不会改变给定文件的顺序。

### ```/// <reference types="..." /> ```
与```///<reference path="..." /> ```指令相似，这个指令是用来声明依赖的； 一个```/// <reference types="..."> ``` 指令则声明了对某个包的依赖。

对这些包的名字的解析与在import语句里对模块名的解析类似。可以简单地把三斜线类型引用指令当做import声明的包。

例如，把```/// <reference types="node"/> ```引入到声明文件，表明这个文件使用了```@types/node/index.d.ts ```里面声明的名字； 并且，这个包需要在编译阶段与声明文件一起被包含进来。

仅当在你需要写一个d.ts文件时才使用这个指令。

对于那些在编译阶段生成的声明文件，编译器会自动添加```///<reference types="..."/> ```; 当且仅当结果文件中使用了引用的包里的声明时才会在生成的声明文件里添加```/// <reference types="..."/> ```语句。

若要在.ts文件里声明一个对@types的包的依赖， 使用--types命令行选项或在tsconfig.json里指定。查看在tsconfig.json里使用@types, typeRoots和types了解详情。

### ```/// <reference no-default-lib="true"/> ```
这个指令把一个文件标记成默认库。你会在lib.d.ts文件和它不同的变体的顶端看到这个注释。

这个指令告诉编译器在编译过程中不要包含这个默认库（比如：lib.d.ts）。 这与在命令行上使用--noLib相似。

还要注意， 当传递了--skipDefaultLibCheck时，编译器只会忽略检查带有```/// <reference no-default-lib="true"> ```的文件。

### ```///<amd-module /> ```
默认情况下生成的AMD模块都是匿名的。但是， 当一些工具需要处理生成的模块时会产生问题， 比如```r.js ```。

```amd-module ```指令允许给编译器传入一个可选的模块名：
amdModule.ts
````
///<amd-module name='NamedModule'/>
export class C {
}

````
这会将NamedModule传入AMD define函数里：
amdModule.js
````
define("NamedModule", ["require", "exports"], function(require, exports){
  var c = (function(){
    function C(){}
    return C;
  })();
  exports.C = C;
});
````

## 装饰器
一些场景下我们需要额外的特性来支持标注或修改类及其成员。装饰器（Decorators）为我们在类的声明及成员上通过元编程语法添加标注提供了一种方式。

* 注意： 装饰器是一项实验性特性。

如果要启用实验性的装饰器特性，你必须在命令行或tsconfig.json里启用experimentalDecorators编译器选项：

命令行：
````
tsc --target ES5 --experimentalDecorators
````

tsconfig.json:
````
{
    "compilerOptions": {
        "target": "ES5",
        "experimentalDecorators": true
    }
}

````

装饰器是一种特殊类型的声明， 它能够被附加到类声明， 方法，访问符，属性或参数上。装饰器使用@expression这种方式， expression求值后必须为一个函数，它会在运行时被调用， 被装饰的声明信息作为参数传入。

例如， 有一个@sealed装饰器， 我们会这样定义sealed函数：
````
function sealed(target) {
    // do something with "target" ...
}
````

### 装饰器工厂
如果要定制一个装饰器如何应用到一个声明上， 我们得写一个装饰器工厂函数。装饰器工厂就是一个简单的函数， 它返回一个表达式， 以供装饰器在运行时调用。

我们可以通过下面的方式来写一个装饰器工厂函数：
````
function color(value: string) { // 这是一个装饰器工厂
    return function (target) { //  这是装饰器
        // do something with "target" and "value"...
    }
}

````

### 装饰器组合
多个装饰器可以同时应用到一个声明上，它们可以书写在同一行上也可以写在多行上。
````
@f @g x
// 或
@f
@g
x
````
当多个装饰器应用于一个声明上，它们求值方式与复合函数相似。在这个模型下，当符合f和g时，复合的结果(f ∘ g)(x)等同于f(g(x))。

同样的， 在TypeScript里， 当多个装饰器应用在一个声明上时会进行如下步骤的操作：
- 由上至下依次对装饰器表达式求值
- 求值的结果会被当作函数，由下至上一次调用

下例中使用装饰器工厂：
````
function f() {
    console.log("f(): evaluated");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("f(): called");
    }
}

function g() {
    console.log("g(): evaluated");
    return function (target, propertyKey: string, descriptor: PropertyDescriptor) {
        console.log("g(): called");
    }
}

class C {
    @f()
    @g()
    method() {}
}

````
在控制台里会打印出如下结果：
````
f(): evaluated
g(): evaluated
g(): called
f(): called

````

### 装饰器求值
类中不同声明上的装饰器将按以下规定的顺序应用：
- 参数装饰器，然后依次是方法装饰器， 访问符装饰器，或属性装饰器应用到每个实例成员
- 参数装饰器，然后依次是方法装饰器，访问符装饰器，或属性装饰器应用到每个静态成员
- 参数装饰器应用到构造函数
- 类装饰器应用到类

### 类装饰器
类装饰器在类声明之前被声明（紧靠着类声明）。类装饰器应用于类构造函数，可以用来见识， 修改或替换类定义。 类装饰器不能用在声明文件中（.d.ts）, 也不能用在任何外部上下文中（比如declare的类）。

类装饰器表达式会在运行时当作函数被调用， 类的构造函数作为其唯一的参数。

如果类装饰器返回一个值，它会使用提供的构造函数来替换类的声明。

* 注意，如果你要返回一个新的构造函数， 你必须注意处理好原来的原型链。在运行时的装饰器调用逻辑中不会为你做这些。

下面是使用类装饰器（@sealed）的例子， 应用在Greeter类：
````
@sealed
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

````
我们可以定义@sealed装饰器：
````
function sealed(constructor: Function){
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

````
当@sealed被执行的时候，它将密封此类的构造函数和原型。

下面是一个重载构造函数的例子：
````
function classDecorator<T extends {new(...args:any[]):{}}>(constructor:T) {
    return class extends constructor {
        newProperty = "new property";
        hello = "override";
    }
}

@classDecorator
class Greeter {
    property = "property";
    hello: string;
    constructor(m: string) {
        this.hello = m;
    }
}

console.log(new Greeter("world"));

````

### 方法装饰器
方法装饰器声明在一个方法的声明之前（紧靠着方法声明）。它会被应用到方法的属性描述符上， 可以用来监视、修改或者替换方法定义。方法装饰器不能用在声明文件（.d.ts）, 重载或者任何外部上下文（比如declare的类）中。

方法装饰器表达式会在运行时当作函数被调用，传入下列3个参数：
- 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 成员的名字
- 成员的属性描述符

* 注意，如果代码输出目标版本小于ES5, 属性描述符将会是undefined。

如果方法装饰器返回一个值，它会被用做方法的属性描述符。
* 注意，如果代码输出目标版本小于ES5,返回值会被忽略。

下面是一个方法装饰器（@enumberable）的例子，应用于Greeter类的方法上：
````
class Greeter{
  greeting: string;
  constructor(message: string){
    this.greeting = message;
  }
  @enumerable(false)
  greet(){
    return "Hello," + this.greeting;
  }
}

````
我们可以用下面的函数声明来定义@enumerable装饰器：
````
function enumerable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.enumerable = value;
    };
}

````
这里的@enumerable(false)是一个装饰器工厂。 当装饰器@enumerable(false)被调用时，它会修改属性描述符的enumerable属性。

### 访问器装饰器
访问器装饰器声明在一个访问器的声明之前（紧靠着访问器声明）。访问器装饰器应用于访问器的属性描述符并且可以用来监视、修改或替换一个访问器的定义。访问器装饰器不能用在声明文件中（.d.ts）, 或者任何外部上下文（比如declare的类）里。

* 注意， TypeScript不允许同时装饰一个成员的get和set访问器。取而代之的是，一个成员的所有装饰的必须应用在文档顺序的第一个访问器上。这是因为，在装饰器应用于一个属性描述符时，它联合了get和set的访问器，而不是分开声明的。

访问器装饰器表达式会在运行时当作函数被调用， 传入下列3个参数：
- 对于静态成员来说是类的构造函数， 对于实例成员是类的原型对象
- 成员的名字
- 成员的属性描述符

* 注意， 如果代码输出目标版本小于ES5, Property Descriptor将会是undefined.

如果访问器装饰器返回一个值，它会被用做方法的属性描述符。
* 注意， 如果代码输出目标版本小于ES5 返回值会被忽略。

下例是使用了访问器装饰器（@configurable）的例子，应用于Point类的成员上：
````
class Point {
    private _x: number;
    private _y: number;
    constructor(x: number, y: number) {
        this._x = x;
        this._y = y;
    }

    @configurable(false)
    get x() { return this._x; }

    @configurable(false)
    get y() { return this._y; }
}
````
我们可以通过如下函数声明来定义@configurable装饰器
````
function configurable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.configurable = value;
    };
}
````

### 属性装饰符
属性装饰器声明在一个属性声明之前（紧靠着属性声明）。属性装饰器不能用在声明文件中（.d.ts）, 或者任何外部上下文（比如declare的类）里。
属性装饰器表达式会在运行时当作函数被调用，传入下列2个参数：
- 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
- 成员的名字

* 注意， 属性描述符不会作为参数传入属性装饰器，这与TypeScript是如何初始化属性装饰器的有关。因为目前没有办法在定义一个原型对象的成员时描述一个实例属性，并且没办法监视或修改一个属性的初始化方法。返回值也会被忽略。因此，属性描述符只能用来监视类中是否声明了某个名字的属性。








