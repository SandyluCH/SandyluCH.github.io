## Symbols
自ECMAScript2015起， symbol成为了一种新的原生类型，就像number和string一样。

symbol类型的值是通过Symbol构造函数创建的。
````
let sym1 = Symbol();
let sym2 = Symbol("key"); // 可选的字符串key
````
Symbols是不可改变且唯一的。
````
let sym2 = Symbol("key");
let sym3 = Symbol("key");
sym2 === sym3; // false, symbols是唯一的
````
像字符串一样， symbols也可以被用做对象属性的键。
````
let sym = Symbol();
let obj = {
  [sym]: "value"
};
console.log(obj[sym]);
console.log(obj.sym); // "value"
````

Symbols也可以与计算出的属性名声明相结合来声明对象的属性和类成员。
````
const getClassNameSymbol = Symbol();

class C {
    [getClassNameSymbol](){
       return "C";
    }
}

let c = new C();
let className = c[getClassNameSymbol](); // "C"

````
### 众所周知的Symbols
除了用户定义的symbols， 还有一些已经众所周知的内置symbols。内置symbols用来表示语言内部的行为。
一下为内置symbols的列表：
- Symbol.hashInstance
  方法， 会被instanceof运算符调用。构造器对象用来识别一个对象是否是其实例。
- Symbol.isConcatSpreadable
  布尔值， 表示当在一个对象上调用Array.prototype.concat时，这个对象的数组元素是否可展开。
- Symbol.iterator
  方法， 被for-of语句调用。 返回对象的默认迭代器
- Symbol.match
  方法，被String.prototype.match调用。正则表达式用来匹配字符串。
- Symbol.replace
  方法，被String.prototype.replace调用。正则表达式用来替换字符串中匹配的子串。

- Symbol.search
  方法，被String.prototype.search调用。正则表达式返回被匹配部分在字符串中的索引。

- Symbol.species
  函数值，为一个构造函数。用来创建派生对象。

- Symbol.split
  方法，被String.prototype.split调用。正则表达式来用分割字符串。

- Symbol.toPrimitive
  方法，被ToPrimitive抽象操作调用。把对象转换为相应的原始值。

- Symbol.toStringTag
  方法，被内置方法Object.prototype.toString调用。返回创建对象时默认的字符串描述。

- Symbol.unscopables
  对象，它自己拥有的属性会被with作用域排除在外。
      


## 迭代器和生成器
### 可迭代性
当一个对象实现了Symbol.iterator属性时，我们认为它是可迭代的。一些内置的类型如Array, Map, Set, String, Int32Array, Unit32Array等都已经实现了各自的Symbol.iterator.对象上的Symbol.iterator函数负责返回供迭代的值。
#### for..of语句
for..of会遍历可迭代的对象，调用对象上的Symbol.iterator方法。下面是在数组上使用for..of的简单例子：
````
let someArray = [1, "string", false];
for(let entry of someArray){
  console.log(entry); // 1, "string", false
}

````

#### ```for..of ``` vs. ```for...in ```语句
```for..of```和 ```for..in ```均可迭代一个列表； 但是用于迭代的值却不同， for..in迭代的是对象的键的列表，而```for..of ```则迭代对象的键对象的值。
例如：
````
let list = [4, 5, 6];

for (let i in list) {
    console.log(i); // "0", "1", "2",
}

for (let i of list) {
    console.log(i); // "4", "5", "6"
}

````
另一个区别是```for..in```可以操作任何对象； 它提供了查看对象属性的一种方法。但是```for..of```关注迭代对象的值。内置对象Map和Set已经实现了Symbol.iterator方法， 让我们可以访问它们保存的值。
````
let pets = new Set(["Cat", "Dog", "Hamster"]);
pets["species"] = "mammals";

for (let pet in pets) {
    console.log(pet); // "species"
}

for (let pet of pets) {
    console.log(pet); // "Cat", "Dog", "Hamster"
}

````
