## 高级类型
### 交叉类型（Intersection Types）
交叉类型是将多个类型合并为一个类型。这让我们可以把所有的多种类型叠加到一起成为一种类型， 它包含了所需的所有类型的特性。例如， ```Person & Serializable & Loggable ``` 同时是Person 和 Serializable 和Loggbale。 就是说这个类型的对象同时拥有了这三种类型的成员。

我们大多是在混入（mixins）或其他不适合典型面向对象模型的地方看到交叉类型的使用。
例如：
````
function extend<T,U>(first: T, second: U): T & U {
  let result = <T & U>{};
  for(let id in first){
    (<any>result)[id] = (<any>first)[id];
  }
  for(let id in second){
    if(!result.hasOwnProperty(id)){
      (<any>result)[id] = (<any>second)[id];
    } 
  }
  return result;
}

class Person{
  constructor(public name: string){}
}
interface Loggable{
  log():void;
}
class ConsoleLogger implements Loggable{
  log(){}
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();

````
### 联合类型（Union Types）
联合类型与交叉类型， 但是使用上却完全不同。偶尔你会遇到这种情况，一个代码库希望传入number或string类型的参数。例如下面的函数：
````
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: any) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"


````
padLeft存在一个问题， padding参数的类型指定成了any。 这就是说我们可以传入一个即不是number也不是string类型的参数，但是TypeScript却不报错。
````
let indentedString = padLeft("Hello world", true); // 编译阶段通过，运行时报错

````
代替any, 我们可以使用联合类型作为padding的参数：
````
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: string | number) {
    // ...
}

let indentedString = padLeft("Hello world", true); // errors during compilation

````
联合类型表示一个值可以是几种类型之一。 我们用竖线（|）分割每个类型，所以 ```number | string | boolean ``` 表示一个值可以是number, string, 或boolean。

如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员。
````
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors

````
如果一个值的类型是A|B, 我们能够确定的是它包含了A和B中共有的成员。这个例子里，Bird具有一个fly成员。我们不能确定一个Bird | Fish类型的变量是否有fly方法。如果变量在运行时是Fish类型，那么调用pet.fly()就出错了。

### 类型保护与区分类型（Type Guards and Differentiating Types）
联合类型适合于那些值可以为不同类型的情况。 我们只能访问联合类型中共同拥有的成员。
````
let pet = getSmallPet();

// 每一个成员访问都会报错
if (pet.swim) {
    pet.swim();
}
else if (pet.fly) {
    pet.fly();
}

````
改进方式：
````
let pet = getSmallPet();

if ((<Fish>pet).swim) {
    (<Fish>pet).swim();
}
else {
    (<Bird>pet).fly();
}

````

#### 用户自定义的类型保护
类型保护就是一些表达式，它们会在运行时检查以确保在某个作用域里的类型。要定义一个类型保护，我们只要简单地定义一个函数，它的返回值时一个类型谓词：
````
function isFish(pet : Fish | Bird): pet is Fish{
  return (<Fish>pet).swim !== undefined;
}

````
在这个例子里， pet is Fish就是类型谓词。谓词为parameterName is Type这种形式， parameterName 必须是来自于当前函数签名里的一个参数名。

每当使用一些变量调用isFish时，TypeScript会将变量缩减为那个具体的类型，只要这个类型与变量的原始类型是兼容的。
````
// swim 和 fly 调用都没有问题的写法
if(isFish(pet)){
  pet.swim();
}else{
  pet.fly();
}

````
#### typeof类型保护
使用联合类型书写padLeft代码， 例如：
````
function isNumber(x: any): x is number {
    return typeof x === "number";
}

function isString(x: any): x is string {
    return typeof x === "string";
}

function padLeft(value: string, padding: string | number) {
    if (isNumber(padding)) {
        return Array(padding + 1).join(" ") + value;
    }
    if (isString(padding)) {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}


````
由于typescript可以将typeof 识别为一种类型保护。即上述代码可以改写为：
````
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

````

#### instanceof 类型保护
instanceof类型保护是通过构造函数来细化类型的一种方式。例如：
````
interface Padder {
    getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces + 1).join(" ");
    }
}

class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}

function getRandomPadder() {
    return Math.random() < 0.5 ?
        new SpaceRepeatingPadder(4) :
        new StringPadder("  ");
}

// 类型为SpaceRepeatingPadder | StringPadder
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // 类型细化为'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // 类型细化为'StringPadder'
}
````

### 可以为null的类型
TypeScript具有两种特殊的类型，null和undefined, 他们分别具有值null和undefined。 默认情况下， 类型检查器认为null与undefined可以赋值给任何类型。null和undefined是所有其它类型的一个有效值。这也意味着，你阻止不了将它们赋值给其它类型， 就算是你想要阻止这种情况也不行。

```--strictNullChecks ```标记可以解决此错误：挡你声明一个变量时，它不会自动地包含null或undefined。 你可以使用联合类型明确的包含他们：
````
let s = "foo";
s = null; // 错误, 'null'不能赋值给'string'
let sn: string | null = "bar";
sn = null; // 可以

sn = undefined; // error, 'undefined'不能赋值给'string | null'
````
* 注意：TypeScript会把null和undefined区别对待。 ```string | null ``` , ```string | undefined ```, 和```string | undefined | null ```是不同的类型。

#### 可选参数和可选属性
使用了--strictNullChecks, 可选参数会被自动地加上 ```| undefined ```
````
function f(x: number, y?: number) {
    return x + (y || 0);
}
f(1, 2);
f(1);
f(1, undefined);
f(1, null); // error, 'null' is not assignable to 'number | undefined'
````

可选属性也会有同样的处理：
````
class C {
    a: number;
    b?: number;
}
let c = new C();
c.a = 12;
c.a = undefined; // error, 'undefined' is not assignable to 'number'
c.b = 13;
c.b = undefined; // ok
c.b = null; // error, 'null' is not assignable to 'number | undefined'
````

#### 类型保护和类型断言
由于可以为null的类型是通过联合类型实现, 那么你需要使用类型保护来去除null。
````
function f(sn: string | null): string {
    if (sn == null) {
        return "default";
    }
    else {
        return sn;
    }
}
````
或者：
````
function f(sn: string | null): string {
    return sn || "default";
}
````
如果编译器不能够去除null或 undefined, 你可以使用类型断言手动去除。语法是添加！后缀： identifier从identifier的类型里去除了null和undefined:
````
function broken(name: string | null): string {
  function postfix(epithet: string) {
    return name.charAt(0) + '.  the ' + epithet; // error, 'name' is possibly null
  }
  name = name || "Bob";
  return postfix("great");
}

function fixed(name: string | null): string {
  function postfix(epithet: string) {
    return name!.charAt(0) + '.  the ' + epithet; // ok
  }
  name = name || "Bob";
  return postfix("great");
}

````

### 类型别名
类型别名会给一个类型起个新名字。类型别名有时和接口很像，但是可以作用于原始值，联合类型，元组以及其它任何你需要手写的类型。
````
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
}
````
起别名不会新建一个类型-它创建了一个新名字来引用那个类型。
同接口一样， 类型别名也可以是泛型， 我们可以添加类型参数并且在别名声明的右侧传入：
````type Container<T> = { value: T }; ````
我们也可以使用类型别名来在属性里引用自己：
````
type Tree<T> = {
  value: T;
  left: Tree<T>;
  right: Tree<T>;
}
````
与交叉类型一起使用，我们可以创建出一些十分稀奇的类型：
````
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
    name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;

````
* 注意： 类型别名不能出现在声明右侧的任何地方。
```type Yikes = Array<Yikes>; // error ```

#### 接口 vs 类型别名
类型别名可以像接口一样，然而，仍有一些细微差别。
区别有：
- 接口创建了一个新的名字，可以在其他任何地方使用。类型别名并不创建新名字。比如， 错误信息就不会使用别名。
例如：
````
type Alias = { num: number }
interface Interface {
    num: number;
}
declare function aliased(arg: Alias): Alias; // 返回的是对象字面量类型
declare function interfaced(arg: Interface): Interface; // 返回的是Interface
````
- 类型别名不能被extends 和 implements (自己也不能extends 和 implements其它类型)。

如果你无法通过接口来描述一个类型并且需要使用联合类型或元组类型，这时通常会使用类型别名。

### 字符串字面量类型
字符串字面量类型允许你指定字符串必须的固定值。在实际应用中，字符串字面量类型可以与联合类型，类型保护和类型别名很好的配合。通过结合使用这些特性，你可以实现类似枚举类型的字符串。
````
type Easing = "ease-in" | "ease-out" | "ease-in-out";
class UIElement{
  animate(dx: number, dy: number, easing: Easing){
    if(easing === "ease-in"){

    }else if(easing === "ease-out"){

    }else if(easing === "ease-in-out"){

    }else{
      // error ! should not pass null or undefined.
    }
  }
}

let button = new UIElement();
button.animate(0,0, "ease-in");
button.animate(0,0, "uneasy"); // error: "uneasy" is not allowed here

````

你只能从三种允许的字符中选择其一来作为参数传递，传入其它值则会产生错误。
````
Argument of type '"uneasy"' is not assignable to parameter of type '"ease-in" | "ease-out" | "ease-in-out"'

````
字符串字面量类型还可以用于区分函数重载：
````
function createElement(tagName: "img"): HTMLImageElement;
function createElement(tagName: "input"): HTMLInputElement;
// ... more overloads ...
function createElement(tagName: string): Element {
    // ... code goes here ...
}

````




