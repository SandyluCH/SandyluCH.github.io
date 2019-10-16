## 泛型

### 使用泛型变量
````
function identity<T>(arg: T): T{
  return arg;
}

````
如果我们想同事打印出arg的长度。 我们很可能会这样做：
````
funtion loggingIdentity<T>(arg: T): T{
  console.log(arg.length); // Error: T doesn't have .length
  return arg;
}
````

如果这么做，编译器会报错说我们使用了arg的.length属性，但是没有地方指明arg具有这个属性。注意，这些类型变量代表的是任意类型， 所以使用这个函数的人可能传入的是个数字，而数字是没有.length属性的。

现在假设我们想操作T类型的数组而不直接是T。 由于我们操作的是数组，所以.length属性是应该存在的。我们可以向创建其他数组一样创建这个数组：
````
function loggingIdentity<T>(arg: T[]): T[]{
  console.log(arg.length); // Array has a .length, so no more error
  return arg;
}

````
泛型函数loggingIdentity, 接收类型参数T和参数arg, 它是个元素类型是T的数组， 并返回元素类型是T的数组。如果我们传入数字数组，将返回一个数字数组， 因为此时T的类型为number。这可以让我们把泛型变量T当做类型的一部分使用，而不是整个类型，增加了灵活性。

### 泛型类型

泛型函数的类型与非泛型函数的类型没什么不同，只是有一个类型参数在最前面，像函数声明一样：
````
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <T>(arg: T) => T = identity;

````
我们也可以使用不同的泛型参数名，只要在数量上和使用方式上能对应上就可以。
````
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;

````
我们还可以使用带有调用签名的对象字面量来定义泛型函数：
````
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: {<T>(arg: T): T} = identity;

````
这引导我们去写第一个泛型接口了。我们把上面例子里的对象字面量拿出来作为一个接口：
````
interface GenericIdentityFn {
    <T>(arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn = identity;


````
一个相似的例子，我们可能想把泛型参数当作整个接口的一个参数。这样我们就能清楚的知道使用具体是哪个泛型类型。如：
````
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;

````

### 泛型类

泛型类看上去与泛型接口差不多。泛型类使用（<>）括起泛型类型，跟在类名后面。
````
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };

````
GenericNumber类的使用是十分直观的，没有什么去限制它只能使用number类型，也可以使用字符串或其它更复杂的类型。
````
let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function(x, y) { return x + y; };

console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));

````
与接口一样，直接把泛型类型放在类后面，可以帮助我们确认类的所有属性都在使用相同的类型。

泛型类值的是实例部分的类型，类的静态属性不能使用这个泛型类型。

### 泛型约束
````
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}

````
相比于操作any所有类型，我们想要限制函数去处理任意带有.length属性的所有类型。只要传入的类型有这个属性，我们就允许。

为此，我们定义一个接口来描述约束条件。创建一个包含.length属性的接口， 使用这个接口和extends关键字来实现约束：
````
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
````
现在这个泛型函数被定义了约束，因此它不再是适用于任意类型：
````
loggingIdentity(3); // Error, number doesn't have a .length property

````

我们需要传入符合约束类型的值，必须包含必须的属性：
``` loggingIdentity({length:10, value:3});   ```

### 在泛型约束中使用类型参数

你可以声明一个类型参数，且它被另一个类型参数所约束。
