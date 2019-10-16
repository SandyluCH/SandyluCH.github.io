### 变量声明

let和const是JavaScript里面相对较新的变量声明方式。const是对let的一个增强，它能阻止对一个变量再次赋值。

因为TypeScript是JavaScript的超集，所以它本身就支持let和const.

- 解构

````
let input = [1, 2];
let [first, second] = input;
console.log(first); // outputs 1
console.log(second); // outputs 2
````

作用于函数参数
````
function f([first, second]: [number, number]) {
    console.log(first);
    console.log(second);
}
f(input);

````

你可以在数组里使用...语法创建剩余变量：
````
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // outputs 1
console.log(rest); // outputs [ 2, 3, 4 ]

````

当然，由于是JavaScript， 你可以忽略你不关心的尾随元素；
````
let [first] = [1, 2, 3, 4];
console.log(first); // outputs 1
````
或 其它元素：
````
let [, second, , fourth] = [1, 2, 3, 4];

````

你也可以解构对象：
````
let o = {
    a: "foo",
    b: 12,
    c: "bar"
};
let { a, b } = o;

````
这通过o.a 和o.b创建了a和b。 注意， 如果你不需要c， 你可以忽略它。
就像数组解构， 你可以用没有声明的复制：
````
({a, b} = {a: 'baz', b: 101});

````
*注意， 我们需要用括号将它括起来， 因为JavaScript通常会将以```{``` 起始的语句解析为一个块。你可以在对象里使用...语法创建剩余变量。
````
let { a, ...passthrough } = o;
let total = passthrough.b + passthrough.c.length;

````

你也可以给属性以不同的名字：
````
let { a: newName1, b: newName2 } = o;
````

如果你想要指定它的类型，仍然需要在其后写上完整的模式。
````
let {a, b} : { a: string, b:number } = o;

````

默认值可以让你在属性为undefined时使用缺省值：
````
function keepWholeObject(wholeObject: { a: string, b?: number }) {
   let { a, b = 1001 } = wholeObject;
}

````
现在， 即使b为undefined, keepWholeObject函数的变量wholeObject的属性a和b都会有值.


解构也能用于函数声明。看以下简单的情况:
````
type C = { a: string, b?: number }
function f({ a, b }: C): void {
    // ...
}
````

但是， 通常情况下更多的是指定默认值，解构默认值有些棘手。首先， 您需要在默认值之前设置其格式。
````
// 类型推断
function f({ a="", b=0 } = {}): void {
    // ...
}
f();

````
其次， 你需要知道在解构属性上给予一个默认或可选的属性用来替换主初始化列表。要知道C的定义有一个b可选属性：
````
function f({ a, b = 0 } = { a: "" }): void {
    // ...
}
f({ a: "yes" }); // ok, default b = 0
f(); // ok, default to {a: ""}, which then defaults b = 0
f({}); // error, 'a' is required if you supply an argument

````

要小心使用解构。 从前面的例子可以看出，就算是最简单的解构表达式也是难以理解的。 尤其当存在深层嵌套解构的时候，就算这时没有堆叠在一起的重命名，默认值和类型注解，也是令人难以理解的。 解构表达式要尽量保持小而简单。 你自己也可以直接使用解构将会生成的赋值表达式。

- 展开

展开操作符正与结构相反。它允许你将一个数组展开为另一个数组，或将一个对象展开为另一个对象。
例如：
````
let first = [1, 2];
let second = [3, 4];
let bothPlus = [0, ...first, ...second, 5];

````
这会令bothPlus的值为[0,1,2,3,4,5]。 展开操作创建了first和second的一份浅拷贝。
它们不会被展开操作所改变。

你还可以展开对象：
````
let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { ...defaults, food: "rich" };

````
search的值为```{food: "rich", price:"$$", ambiance:"noisy"} ```。 对象的展开比数组的展开要复杂的多。 像数组展开一样，它是从左至右进行处理，但结果仍为对象。 这就意味着出现在展开对象后面的属性会覆盖前面的属性。 因此，如果我们修改上面的例子，在结尾处进行展开的话：

那么， defaults里的food属性会重写food: "rich"。

对象展开还有其它一些意想不到的限制。 首先，它仅包含对象自身的可枚举属性。大体上是说当你展开一个对象实例时，你会丢失其方法：
````
class C {
  p = 12;
  m() {
  }
}
let c = new C();
let clone = { ...c };
clone.p; // ok
clone.m(); // error!

````

* TypeScript编译器不允许展开泛型函数上的类型参数。


### 接口

  在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约

  类型检查器不会去检查属性的顺序， 只要相应的属性存在并且类型也是对的就可以。

- 可选属性

接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。 可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象中只有部分属性赋值了。
例如：
````
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});

````

带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个```? ```符号。

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获引用了不存在的属性时的错误。
````
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  let newSquare = {color: "white", area: 100};
  if (config.clor) {
    // Error: Property 'clor' does not exist on type 'SquareConfig'
    newSquare.color = config.clor;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});

````

- 只读属性
一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名钱用readonly来指定只读属性：
````
interface Point {
    readonly x: number;
    readonly y: number;
}
````
你可以通过赋值一个对象字面量来构造一个Point。 赋值后， x和y再也不能被改变了。
````
let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!

````

TypeScript具有ReadonlyArray<T>类型，他与Array<T>相似， 只是把所有可变方法去掉了，因此可以确保数组创建后再不能被修改；
````
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // error!
ro.push(5); // error!
ro.length = 100; // error!
a = ro; // error!

````
上面代码的最后一行，可以看到就算把整个ReadonlyArray赋值到一个普通数组也是不可以的。但是你可以用类型断言重写：
```a = ro as number[]; ```


readonly VS const
最简单判断该用readonly还是const的方法是看要把它做成变量使用还是作为属性使用。 作为变量，则使用const; 作为属性则使用readonly。


- 额外的属性检查
````
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}

let mySquare = createSquare({ colour: "red", width: 100 });

````

注意传入createSquare的参数拼写是colour而不是color. TypeScript会认为这段代码可能存在bug. 对象字面量会被特殊对待而且
会经过额外属性检查， 当将它们赋值给变量或作为参数传递的时候。如果一个对象字面量存在任何“目标类型”不包含的属性时，会得
导一个错误。
````
// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });

````
绕开这些检查非常简单。最简便的方法是使用类型断言：
````
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);

````
如果SquareConfig带有上面定义的类型color和width属性，并且还会带有任意数量的其它属性，那么我们可以这样定义它：
````
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}

````

- 函数类型

接口能够描述JavaScript中对象拥有的各种各样的外形。除了描述带有属性的普通对象外，接口也可以描述函数类型。

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数
都需要名字和类型。
````
interface SearchFunc{
  (source: String, subString: string): boolean;
};

````

这样定义后，我们可以像使用其他接口一样使用这个函数类型的接口。下列展示了如何创建一个函数类型的变量，并将一个同类型的函数赋值给这个变量。
````
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}

````

对于函数类型的类型检查来说， 函数的参数名不需要与接口里定义的名字相匹配。比如，我们使用下面的代码重写上面的例子：
````
let mySearch:SearchFunc;
mySearch = function(src: String, sub: string): boolean{
  let result = src.search(sub);
  return result > -1;
}

````
函数的参数会逐个进行检查，要求对应位置上的参数类型是兼容。如果你不想指定类型，TypeScript的类型系统会推断出参数类型，因为函数直接赋值给SearchFunc类型变量。 函数的返回值类型是通过其返回值推算出来的（此例中是false和true）。 如果让这个函数返回数字或字符串，类型检查器会警
告我们函数的返回值类型与SearchFunc接口中的定义不匹配。
````
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}

````

- 可索引的类型

与使用接口描述函数类型差不多，我们也可以描述哪些能够“通过索引得到”的类型。 可索引类型具有一个索引签名，它描述了对象索引的类型，还有相应的索引返回值类型。
````
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];

````
上面例子中，我们定义了StringArray 接口， 它具有索引签名。这个索引签名表示了当用number去索引StringArray时会得到string类型的返回值。

TypeScript支持两种索引签名，字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。这是因为当使用number来索引时，JavaScript会将它转换成string 然后再去索引对象。
````
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}

// 错误：使用数值型的字符串索引，有时会得到完全不同的Animal!
interface NotOkay {
    [x: number]: Animal;
    [x: string]: Dog;
}

````

字符串索引签名能够很好的描述dictionary模式，并且他们也会确保所有属性与其返回值类型相匹配。因为字符串索引声明了obj.property 和obj["property"]两种形式都可以。下面的例子里，name的类型与字符串索引类型不匹配，所以类型检查器给出一个错误提示:
````
interface NumberDictionary {
  [index: string]: number;
  length: number;    // 可以，length是number类型
  name: string       // 错误，`name`的类型与索引类型返回值的类型不匹配
}

````

最后， 你可以将索引签名设置为只读，这样就防止了给索引赋值：
````
interface ReadonlyStringArray{
  readonly [index: number]: string;
}
let myArray: ReadonlyStringArray = ['Alice', 'Bob'];
myArray[2] = 'Mallory'; // error！

````
你不能设置myArray[2], 因为索引签名是只读的。

- 类类型

与java里的接口的基本作用一样， TypeScript也能够用它来明确的强制一个类去符合某种契约。
````
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

````

你也可以在接口中描述一个方法，在类里实现它， 如同下面的setTime方法一样：
````
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}

````

接口描述了类的公共部分，而不是公共和私有两部分。 它不会帮你检查类是否具有某些私有成员。

类静态部分与实力部分区别

当你操作类和借口的时候，你要知道类是具有两个类型：静态部分的类型和实例的类型。你会注意到，当你用构造器签名去定义一个接口并试图定义一个类去实现这个接口时会得到一个错误：
````
interface ClockConstructor{
  new (hour: number, minute: number);
}

class Clock implements ClockConstructor{
  currentTime: Date;
  constructor(h: number, m: number){ }
}

````

这里因为当一个类实现了一个接口时，只对其实例部分进行类型检查。constructor存在于类的静态部分，所以不在检查的范围内。

因此，我们应该直接操作类的静态部分。如下例， 我们定义了两个接口，ClockConstructor为构造函数所用和ClockInterface为实例方法所用。为了方便我们定义一个构造函数createClock, 用传入的类型创建实例：
````
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
digital.tick(); // beep beep
analog.tick(); // tick tock

````
因为createClock的第一个参数是ClockConstructor类型， 在createClock(AnalogClock, 7, 32)里，会检查AnalogClock是否符合构造函数签名。


继承接口

和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。
````
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;

````

一个接口可以继承多个接口，创建出多个接口的合成接口。
````
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;

````


混合类型

接口能够描述JavaScript里的丰富类型。因为JavaScript其动态灵活的特点，有时你会希望一个对象可以同时具有上面提到的多种类型。如下面例子中， 一个对象可以同时作为函数和对象使用，并带有额外的属性。
````
interface Counter {
    (start: number): string; // 函数
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;

````
在使用JavaScript第三方库的时候，你可能需要像上面那样去完整地定义类型。


接口继承类

当接口继承了一个类类型时，它会继承类的成员但不包括其实现。就好像接口声明了所有类中存在成员，但并没有提供具体实现一样。 接口同样会继承到类的private和protected成员。这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现。

当你有一个庞大的继承结构时这很有用，但你的代码只在子类拥有特定属性时起作用。这个子类除了继承基类外与基类没有任何关系， 例如：
````
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control implements SelectableControl {
    select() { }
}

class TextBox extends Control {
    select() { }
}

// 错误：“Image”类型缺少“state”属性。
class Image implements SelectableControl {
    select() { }
}

class Location {

}

````
例子里，SelectableControl 包含了Control的所有成员， 包括私有成员state。 因为state是私有成员，所以只能够是Control的子类们才能实现SelectableControl接口。 因为只有Control的子类才能够拥有一个声明于Control的私有成员state, 这对私有成员的兼容性是必需的。

在Control类内部，是允许通过SelectableControl的实例来访问私有成员state的，实际上，SelectableControl接口和拥有select方法的control类是一样的。Button和TextBox类是SelectableControl的子类（因为它们都继承自Control并有select方法）， 但Image和Location类并不是这样的。
