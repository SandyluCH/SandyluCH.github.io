## 将string类型转为number类型的方法有:
### Converting String to Number
As with the previous shown methods, JavaScript also provides functions to easily transform a String to a primitive number. These are parseInt(), parseFloat(), Math.floor(), Math.ceil(), Unary Operator / Multiply by 1.
.parseInt() takes a String as a first argument, and a base to which that String will be converted to. This method always returns an integer.
.parseFloat() takes a String as an argument, and returns the Float point number equivalent.
Math.floor() is used to round an integer or floating point number. It returns the nearest integer rounded down.
Math.ceil() can be used to round an integer or floating point number.
Unary Operator By adding a + sign before a String, it will be converted into a number if it follows the right format.
Multiply by 1 If a String is multiplied by the primitive number 1, the string will become a number.


### .parseInt()
The base can be defined by adding prefixes to the numbers we want to parse:
No prefix - If there isn't a prefix, the radix is 10 (decimal).
0 - If the prefix is 0, then the radix is 8 (octal). Though, this feature is deprecated.
0x - If the prefix is 0x, then the radix is 16 (hexadecimal).
Although, we can simply add an optional argument to the method call, defining the base:
let str = '353';
let fltStr = '353.56';
let binStr = '7';
let nanStr = 'hello';

parseInt(str);       // 353
parseInt(fltStr);    // 353
parseInt(binStr, 2); // 111 (Binary)
parseInt(nanStr);    // NaN (Not a Number)

### .parseFloat()
let str = '100';
let fltStr = '100.21';
let nanStr = 'bye';

parseFloat(str);    // 353
parseFloat(fltStr); // 353.21
parseFloat(nanStr); // NaN
Math.floor()
Surprisingly, this method can also accept Strings, making it yet a way to convert a String to an integer.
let str = '100';
let fltStr = '99.89';
let nanStr = 'bye';

Math.floor(str);    // 100
Math.floor(fltStr); // 99
Math.floor(nanStr); // NaN
### Math.ceil()
Very similar to the previous method, Though, this time it returns the nearest integer rounded up.
The method can accept Strings, also making it a way to convert a String to a number:
let str = '100';
let fltStr = '100.21';
let nanStr = 'bye';

Math.ceil(str);    // 100
Math.ceil(fltStr); // 101
Math.ceil(nanStr); // NaN
Keep in mind that if you're needing to parse floats then both Math.floor and Math.ceil are probably not good options since they'll always convert the strings to the nearest integer equivalent.
### Unary Operator
As with Concatenating an Empty String, there is also a workaround that has better performance but lacks readability.
let str = '100';
let fltStr = '100.21';
let nanStr = 'greetings';

+str    // 100
+fltStr // 100.21
+nanStr // NaN
+'1000' // 1000
+10.25  // 10.25
While concise and effective, this isn't a very well-known feature of JavaScript, so it's not advised to use since it may make your code not as easy to understand.
### Multiplying by 1
This approach is arguably the fastest one:
let str = '100';
let fltStr = '100.21';
let nanStr = 'greetings';

str * 1;      // 100
fltStr * 1;   // 100.21
nanStr * 1;   // NaN
'2000' * 1;   // 2000
'102.15' * 1; // 102.15
The previous two approaches work simply due to the fact that JavaScript tries to assimilate the data-types used in a statement such as addition or multiplication.
### Using String and Number Objects
Another way of transforming a String to number or a number to String is creating a new String or Number object with the new keyword.
// Number to String
let numToStr = new String(2);   // String {'2'}

// String to Number
let strToNum = new Number('2'); // Number {2}
This practice is, however, discouraged. When using primitive data the object methods should not be used to create them. Instantiating a primitive data type with the String or Number class wrapper brings performance and memory issues.
### Conclusion
There are many valid ways of manipulating data. It is up to the programmer to decide which one they prefer, choosing performance over readability or a balance between the two.
