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

