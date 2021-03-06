[TypeScript](http://www.typescriptlang.org/)


## TypeScript手册指南

### 基础类型
TypeScript支持与JavaScript几乎相同的数据类型， 此外还提供了使用的枚举类型方便我们使用；
使用案例：
````
let isDone: boolean = false;
````

- 数字

和JavaScript一样，TypeScript里的所有数字都是浮点数。 这些浮点数的类型是 number。 除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript 2015中引入的二进制和八进制字面量。
````
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;

````

- 字符串

JavaScript程序的另一项基本操作是处理网页或服务器端的文本数据。 像其它语言里一样，我们使用 string表示文本数据类型。 和JavaScript一样，可以使用双引号（ "）或单引号（'）表示字符串。

你还可以使用模版字符串，它可以定义多行文本和内嵌表达式。 这种字符串是被反引号包围（ `），并且以${ expr }这种形式嵌入表达式

````
let name: string = "bob";
name = "smith";

let name: string = `Gene`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ name }.

I'll be ${ age + 1 } years old next month.`;
// 或
let sentence: string = "Hello, my name is " + name + ".\n\n" +
    "I'll be " + (age + 1) + " years old next month.";
````

- 数组

TypeScript像JavaScript一样可以操作数组元素。 有两种方式可以定义数组。 第一种，可以在元素类型后面接上 []，表示由此类型元素组成的一个数组：
````
let list: number[] = [1, 2, 3];

// 使用数组泛型 Array<元素类型>
let list: Array<number> = [1, 2, 3];

````

- Tuple

元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。
````
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10]; // OK
// Initialize it incorrectly
x = [10, 'hello']; // Error
````
当访问一个已知索引的元素，会得到正确的类型：
````
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'

````

- 枚举

enum类型是对JavaScript标准数据类型的一个补充。默认情况下，从0开始为元素编号。你可以手动的指定成员的数值。
````
// 默认情况
enum Color {Red, Green, Blue}

// 手动指定成员的数值
enum Color {Red = 1, Green, Blue}
let c:Color = Color.Green;
// 或者 全部都采用手动赋值；
enum Color {Red = 1, Green = 2, Blue = 4}
let c:Color = Color.Green;

````

- Any

有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 any类型来标记这些变量：
````
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
````
在对现有代码进行改写的时候，any类型是十分有用的，它允许你在编译时可选择地包含或移除类型检查。 你可能认为 Object有相似的作用，就像它在其它语言中那样。 但是 Object类型的变量只是允许你给它赋任意值 - 但是却不能够在它上面调用任意的方法，即便它真的有这些方法：
````
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
````
当你只知道一部分数据的类型时，any类型也是有用的。 比如，你有一个数组，它包含了不同的类型的数据：
````
let list: any[] = [1, true, "free"];

list[1] = 100;

````

- Void

某种程度上来说，void类型像是与any类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void：
````
function warnUser(): void {
    console.log("This is my warning message");
}
````
声明一个void类型的变量没有什么大用，因为你只能为它赋予undefined和null:
````
let unusable: void = undefined;
````

- Null和Undefined

TypeScript中，undefined和null两者各自有自己的类型分别讲座undefined和null。 和void相似，他们本身的类型用处不是很大：
````
// Not much else we can assign to these variables!
let u: undefined = undefined;
let n: null = null;
````
默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量。

然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自。 这能避免 很多常见的问题。 也许在某处你想传入一个 string或null或undefined，你可以使用联合类型string | null | undefined。 再次说明，稍后我们会介绍联合类型。

* 注意：我们鼓励尽可能地使用--strictNullChecks，但在本手册里我们假设这个标记是关闭的。

- Never

never类型表示的是那些永不存在的值的类型。 例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 never类型，当它们被永不为真的类型保护所约束时。

never类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never。
````
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
    return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
````

- Object

object表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型。

使用object类型，就可以更好的表示像Object.create这样的API。例如：
````
declare function create(o: object | null): void;

create({ prop: 0 }); // OK
create(null); // OK

create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error

````

- 类型断言

有时候你会遇到这样的情况，你会比TypeScript更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。

通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”。 类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。

类型断言有两种形式。
````
// 其一是“尖括号”语法：
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

// 另一个为as语法：
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;

````
两种形式是等价的。 至于使用哪个大多数情况下是凭个人喜好；然而，当你在TypeScript里使用JSX时，只有 as语法断言是被允许的


### 变量声明

### 接口

### 类

### 函数

### 泛型

### 枚举

### 类型推论

### 类型兼容性

### 高级类型

### Symbols

### 迭代器和生成器

### 模块

### 命名空间

### 命名空间和模块

### 模块解析

### 声明合并

### JSX

### 装饰器

### Mixins

### 三斜线指令

### JavaScript文件类型检查

### 其他
1、安全导航操作符```?. ```和空属性路径，
为了解决导航时变量值为null时，页面运行时出错的问题。
````The null hero's name is {{nullHero?.name}} ````

2、非空断言操作符，
能确定变量值一定不为空时使用。与安全导航操作符不同的是，非空断言操作符不会防止出现null或undefined。它只是告诉TypeScript的类型检查器对特定的属性表达式，不做严格空值检测。
````this.listeners.get(type)!.size > 0 ````

3、 void 0的作用

  void 其实是javascript中的一个函数， 接受一个参数， 返回值永远是undefined。所以void 0 是一种正确且标准的产生undefined的方式。
````
// 方式1
void 0
// 方式2
void(0)
// 方式3
void "hello"
// 方式4
void(new Date())
````
使用undefined的问题是 undefined不是保留关键字。即， undefined是允许作为变量名使用的， 你可以给它指定一个新的值。
````
alert(undefined); // alerts 'undefined'
var undefined = 'new value';
alert(undefined) // alerts 'new value'

````
注意： 在任何支持ECMAScript 5 或者更新的环境中， 这将不成问题（即，undefined作为变量名被重新赋值的问题）， 因为这些环境中undefined作为全局对象的undefined 只读属性。
````
alert(window.hasOwnProperty('undefined')); // alerts "true"
alert(window.undefined); // alerts "undefined"
alert(undefined === window.undefined); // alerts "true"
var undefined = "new value";
alert(undefined); // alerts "new value"
alert(undefined === window.undefined); // alerts "false"

````
另一方面, void 不会被覆盖, void 0 总是返回undefined。

