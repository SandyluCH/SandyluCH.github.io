## 类型推论

TypeScript里， 在有些没有明确指出类型的地方，类型推论会帮助提供类型。如
````
let x = 3;

````
变量x的类型被推断为数字。这种推断发生在初始化变量和成员， 设置默认参数值和决定函数返回值时。

### 最佳通用类型

当需要从几个表达式中推断类型时候，会使用这些表达式的类型来推断出一个最合适的通用类型。例如，
````
let x = [0, 1, null];

````
为了推断x的类型， 我们必须考虑所有元素的类型。这里有两种选择：number和null。计算通用类型算法会考虑所有的候选类型，并给出一个兼容所有候选类型的类型。

由于最终的通用类型取自候选类型，有些时候候选类型共享相同的通用类型，但是却没有一个类型能作为所有候选类型的类型。例如：
````
let zoo = [new Rhino(), new Elephant(), new Snake()];

````
我们想让zoo被推断为Animal[]类型，但是这个数组里没有对象是Animal类型的，因此不能推断出这个结果。为了更正， 当候选类型不能使用的时候我们需要明确的指出类型：
````
let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()];

````
如果没有找到最佳通用类型的话，类型推断的结果为联合数组类型，```(Rhino | Elephant | Snake)[] ```.

### 上下文类型
TypeScript类型推论也可能按照相反的方向进行， 这叫“按上下文归类”。 按上下文归类会发生在表达式的类型与所处的位置相关时。例如：
````
window.onmousedown = function(mouseEvent) {
    console.log(mouseEvent.button);  //<- Error
};
````
这个例子会得到一个类型错误， TypeScript类型检查器使用window.onmousedown函数的类型来推断右边函数表达式类型。因此，就能推断出mouseEvent参数的类型了。 如果函数表达式不是在上下文类型的位置， mouseEvent参数的类型需要指定为any,这样也不会报错了。
如果上下文类型表达式包含了明确的类型信息，上下文的类型被忽略。例如：
````
window.onmousedown = function(mouseEvent: any) {
    console.log(mouseEvent.button);  //<- Now, no error is given
};

````
这个函数表达式有明确的参数类型注解，上下文类型被忽略。这样的话就不报错了，因为这里不会使用到上下文类型。

上下文归类会在很多情况下使用到。通常包含函数的参数，赋值表达式的右边，类型断言，对象成员和数组字面量和返回值语句。 上下文类型也会作为最佳通用类型的候选类型。比如：
````
function createZoo(): Animal[]{
  return [new Rhino(), new Elephant(), new Snake()];
}

````
这个例子里，最佳通用类型有4个候选者：Animal，Rhino，Elephant和Snake。 当然， Animal会被做为最佳通用类型。

## 类型兼容性
TypeScript里的类型兼容性是基于结构子类型的。结构类型是一种只使用其他成员来描述类型的方式。他正好与名义（nominal）类型形成对比。在基于名义类型的类型系统中，数据类型的兼容性或等价性是通过明确的声明和或类型的名称来决定的。这与结构性类型系统不同，他是基于类型的组成结构，且不要求明确地声明。 例如：
````
interface Named {
    name: string;
}

class Person {
    name: string;
}

let p: Named;
// OK, because of structural typing
p = new Person();

````
在使用基于名义类型的语言，比如C#或Java中，这段代码会报错，因为Person类没有明确说明其实现了Named接口。
TypeScript的结构性子类型是根据JavaScript代码的典型写法来设计的。因为JavaScript里广泛地使用匿名对象，例如函数表达式和对象字面量，所以使用结构类型系统来描述这些类型比使用名义类型系统更好。

### 关于可靠性的注意事项
TypeScript的类型系统允许某些在编译阶段无法确认其安全性的操作。当一个类型系统具有此属性时，被当作是不可靠的。TypeScript允许这种不可靠行为的发生是经过仔细考虑的。

TypeScript结构化类型系统的基本规则是， 如果x要兼容y, 那么y至少具有与x相同的属性。例如：
````
interface Named {
    name: string;
}

let x: Named;
// y's inferred type is { name: string; location: string; }
let y = { name: 'Alice', location: 'Seattle' };
x = y;

````
这里要检查y是否能赋值给x, 编译器检查x中的每个属性，看是否能在y中也找到对象的属性。在这个例子中， y必须包含名字是name的string类型成员。y满足条件，因此赋值正确。

检查函数参数时使用相同的规则：
````
function greet(n: Named) {
    console.log('Hello, ' + n.name);
}
greet(y); // OK

````
注意， y有个额外的location属性，但这不会引发错误。只有目标类型（这里是Named）的成员会被一一检查是否兼容。

这个比较过程是递归进行的，检查每个成员及子成员。

### 比较两个函数
例如：
````
let x = (a:number) => 0;
let y = (b:number, s:string) => 0;
y = x; // ok
x = y; // error

````
要查看x是否能赋值给y, 首先看它们的参数列表。x的每个参数必须能在y里找到对应类型的参数。注意的是参数的名字相同与否无所谓，只看它们的类型。这里，x的每个参数在y中都能找到对应的参数，所以允许赋值。 但y不能赋值给x， 因为y有必需的第二个参数，但是x并没有，所以不允许赋值。

如何处理返回值类型，例如，创建两个仅是返回值类型不同的函数：
````
let x = () => ({name:'Alice'});
let y = () => ({name:'Alice',location:'Seattle'});
x = y; // ok
y = x; // error , because x() lacks a location property

````
类型系统强制源函数的返回值类型必须是目标函数返回值类型的子类型。

### 函数参数双向协变
当比较函数参数类型时，只有当源函数参数能够赋值给目标函数或者反过来时才能赋值成功。这是不稳定的，因为调用者可能传入一个具有更精确类型信息的函数，但是调用这个传入的函数的时候却使用了不是那么精确的类型信息。
````
enum EventType { Mouse, Keyboard }

interface Event { timestamp: number; }
interface MouseEvent extends Event { x: number; y: number }
interface KeyEvent extends Event { keyCode: number }

function listenEvent(eventType: EventType, handler: (n: Event) => void) {
    /* ... */
}

// Unsound, but useful and common
listenEvent(EventType.Mouse, (e: MouseEvent) => console.log(e.x + ',' + e.y));

// Undesirable alternatives in presence of soundness
listenEvent(EventType.Mouse, (e: Event) => console.log((<MouseEvent>e).x + ',' + (<MouseEvent>e).y));
listenEvent(EventType.Mouse, <(e: Event) => void>((e: MouseEvent) => console.log(e.x + ',' + e.y)));

// Still disallowed (clear error). Type safety enforced for wholly incompatible types
listenEvent(EventType.Mouse, (e: number) => console.log(e));

````

### 可选参数及剩余参数
比较函数兼容性的时候，可选参数与必须参数是可互换的。源类型上有额外的可选参数不是错误，目标类型的可选参数在源类型里没有对应的参数也不是错误的。

当一个函数有剩余参数时，它被当作无限个可选参数。

这对于系统类型来说是不稳定的，但从运行的角度来看，可选参数一般来说是不强制的，因为对于大多数函数来说相当于传递了一些undefined.

有一个好的例子，常见的函数接收一个回调函数并用对于程序员来说是可预知的参数但对类型系统来说是不确定的参数来调用：
````
function invokeLater(args: any[], callback: (...args: any[]) => void) {
    /* ... Invoke callback with 'args' ... */
}

// Unsound - invokeLater "might" provide any number of arguments
invokeLater([1, 2], (x, y) => console.log(x + ', ' + y));

// Confusing (x and y are actually required) and undiscoverable
invokeLater([1, 2], (x?, y?) => console.log(x + ', ' + y));

````

### 函数重载
对于有重载的函数，源函数的每个重载都要在目标函数上找到对应的函数签名。这确保了目标函数可以在所有源函数可调用的地方调用。

#### 枚举
枚举类型与数字类型兼容，并且数字类型与枚举类型兼容。不同枚举类型之间是不兼容的。比如，
````
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };
let status = Status.Ready;
status = Color.Green; // Error

````
#### 类
类与对象字面量和接口差不多，但有一点不同：类有静态部分和实力部分的类型。比较两个类类型的对象时，只有实例成员会被比较。静态成员和构造函数不在比较的范围内。
````
class Animal {
    feet: number;
    constructor(name: string, numFeet: number) { }
}

class Size {
    feet: number;
    constructor(numFeet: number) { }
}

let a: Animal;
let s: Size;

a = s;  // OK
s = a;  // OK

````
