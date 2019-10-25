## 枚举

使用枚举我们可以定义一些带名字的常量。 使用枚举可以清晰地表达意图或创建一组有区别的用例。 TypeScript支持数字的和基于字符串的枚举。

### 数字枚举
````
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}

````

### 字符串枚举
````
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}

````

### 异构枚举 (不推荐)
从技术的角度来说，枚举可以混合字符串和数字成员，但是似乎你并不会这么做：
````
enum BooleanLikeHeterogeneousEnum {
    No = 0,
    Yes = "YES",
}

````

### 计算和常量成员
每个枚举成员都带有一个值，它可以是常量或计算出来的。 当满足如下条件时，枚举成员被当作是常量：
- 它是枚举的第一个成员且没有初始化其，这种情况下它被赋值0：
````
// E.X is constant:
enum E { X }

````
- 它不带有初始化器且它之前的枚举成员是一个数字常量。这种情况下，当前枚举成员的值为它上一个枚举成员的值加1.
````
// All enum members in 'E1' and 'E2' are constant.

enum E1 { X, Y, Z }

enum E2 {
    A = 1, B, C
}

````
- 枚举成员使用常量枚举表达式初始化。常数枚举表达式是TypeScript表达式的子集，它可以在编译阶段求值。 当一个表达式满足下面条件之一时，它就是一个常量枚举表达式。
  + 一个枚举表达式字面量（主要是字符串字面量或数字字面量）
  + 一个对之前定义的常量枚举成员的引用（可以是在不同的枚举类型中定义的）
  + 带括号的常量枚举表达式
  + 一元运算符```+,-,~ ```其中之一应用在了常量枚举表达式
  + 常量枚举表达式做为二元运算符```+, -, *, /, %, <<, >>, >>>, &, |, ^ ```的操作对象。若常数枚举表达式求值后为NaN或Infinity, 则会在编译阶段报错。

所有其他情况的枚举成员被当作是需要计算得出的值。
````
enum FileAccess {
    // constant members
    None,
    Read    = 1 << 1,
    Write   = 1 << 2,
    ReadWrite  = Read | Write,
    // computed member
    G = "123".length
}

````

### 联合枚举与枚举成员的类型

存在一种特殊的非计算的常量枚举成员的子集：字面量枚举成员。字面量枚举成员是指不带有初始值的常量枚举成员， 或者是值被初始化为：
 - 任何字符串字面量， 如 “foo”
 - 任何数字字面量， 如： 1,100
 - 应用了一元```- ```符号的数字字面量， 例如：-1， -100

当所有枚举成员都拥有字面量枚举值时，它带有了一种特殊的语义。

首先， 枚举成员成为了类型！例如， 我们可以说某些成员只能是枚举成员的值：
````
enum ShapedKind {
    Circle,
    Square
}

interface Circle {
    kind: ShapeKind.Circle;
    radius:number;
}

interface Square {
    kind: ShapedKind.Square;
    sideLength: number;
}

let c:Circle = {
    kind: ShapeKind.Square,  //    ~~~~~~~~~~~~~~~~ Error!
    radius: 100,
};

````
另一个变化是枚举类型本身变成了每个枚举成员的联合。

### 运行时的枚举
枚举是在运行时真正存在的对象。例如：下面的枚举：
````
enum E {
    X,Y,Z
}

````

### 反向映射
除了创建一个属性名做为对象成员的对象之外，数字枚举成员还具有了反向映射，从枚举值到枚举名字。例如，在下面的例子中：
````
enum Enum{
    A
}
let a = Enum.A;
let nameOfA = Enum[a]; // "A"

````
typescript可能会将这段代码编译为下面的JavaScript:
````
var Enum;
(function (Enum) {
    Enum[Enum["A"] = 0] = "A";
})(Enum || (Enum = {}));
var a = Enum.A;
var nameOfA = Enum[a]; // "A"

````

生成的代码中，枚举类型被编译成一个对象，它包含了正向映射（name->value）和反向映射（value->name）。引用枚举成员总会生成为对属性访问并且永远也不会内联代码。

要注意的是不会为字符串枚举成员生成反向映射。

### const枚举
大多数情况下，枚举是十分有效的方案。然而在某些情况下需求很严格。为了避免在额外生成的代码上的开销和额外的非直接的对枚举成员的访问，我们可以使用const枚举。常量枚举通过在枚举上使用const修饰符来定义。
````
const enum Enum {
    A = 1,
    B = A * 2
}

````
常量枚举只能使用常量枚举表达式，并且不同于常规的枚举， 它们在编译阶段会被删除。常量枚举成员在使用的地方会被内联进来。之所以可以这么做是因为，常量枚举不允许包括计算成员。
````
const enum Directions {
    Up,
    Down,
    Left,
    Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]

````
生成的代码为：
````
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];
````

### 外部枚举
外部枚举用来描述已经存在的枚举类型的形状。
````
declare enum Enum{
  A = 1,
  B,
  C = 2
}

````
外部枚举和非外部枚举之间有一个重要的区别，在正常的枚举里，没有初始化方法的成员被当成常数成员。对于非常数的外部枚举而言，没有初始化方法时被当作需要经过计算的。

