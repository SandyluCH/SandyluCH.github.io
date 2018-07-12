## [less.md](http://lesscss.cn/features/#features-overview-feature)

### 父选择器 ‘&’
例子1：
a.less
````
a {
  color: blue;
  &:hover {
    color: green;
  }
}
````
生成的a.css
````
a {
  color: blue;
}

a:hover {
  color: green;
}

````
例子2：
b.less
````
.button {
  &-ok {
    background-image: url("ok.png");
  }
  &-cancel {
    background-image: url("cancel.png");
  }

  &-custom {
    background-image: url("custom.png");
  }
}
````
生成的b.css
````
.button-ok {
  background-image: url("ok.png");
}
.button-cancel {
  background-image: url("cancel.png");
}
.button-custom {
  background-image: url("custom.png");
}

````

例子3：
c.less
````
.link {
  & + & {
    color: red;
  }

  & & {
    color: green;
  }

  && {
    color: blue;
  }

  &, &ish {
    color: cyan;
  }
}

````
生成c.css
````
.link + .link {
  color: red;
}
.link .link {
  color: green;
}
.link.link {
  color: blue;
}
.link, .linkish {
  color: cyan;
}

````

例子4：
d.less
````
.grand {
  .parent {
    & > & {
      color: red;
    }

    & & {
      color: green;
    }

    && {
      color: blue;
    }

    &, &ish {
      color: cyan;
    }
  }
}
````
生成d.css
````
.grand .parent > .grand .parent {
  color: red;
}
.grand .parent .grand .parent {
  color: green;
}
.grand .parent.grand .parent {
  color: blue;
}
.grand .parent,
.grand .parentish {
  color: cyan;
}

````

例子5：
e.less
````
.header {
  .menu {
    border-radius: 5px;
    .no-borderradius & {
      background-image: url('images/button-background.png');
    }
  }
}

````
生成e.css
````
.header .menu {
  border-radius: 5px;
}
.no-borderradius .header .menu {
  background-image: url('images/button-background.png');
}

````


### 变量
````
@nice-blue: #5B83AD;
@light-blue: @nice-blue + #111;

#header {
  color: @light-blue;
}

````

### Mixins
如：
a.less代码：
````
.a, #b {
  color: red;
}
.mixin-class {
  .a();
}
.mixin-id {
  #b();
}

````
转成的a.css代码为：
````
.a, #b {
  color: red;
}
.mixin-class {
  color: red;
}
.mixin-id {
  color: red;
}

````

#### 不输出的mixin
a.less
````
.my-mixin {
  color: black;
}
.my-other-mixin() {
  background: white;
}
.class {
  .my-mixin;
  .my-other-mixin;
}
````
生成a.css
````
.my-mixin {
  color: black;
}
.my-other-mixin() {
  background: white;
}
.class {
  .my-mixin;
  .my-other-mixin;
}
````

#### 带参数的Mixins
也可以带默认值

例子：
a.less
````
.border-radius(@radius: 5px) {
  -webkit-border-radius: @radius;
     -moz-border-radius: @radius;
          border-radius: @radius;
}
#header {
  .border-radius;
}
#header1 {
  .border-radius(4px);
}
.button {
  .border-radius(6px);
}

````
生成a.css
````
#header {
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
}
#header1 {
  -webkit-border-radius: 4px;
  -moz-border-radius: 4px;
  border-radius: 4px;
}
.button {
  -webkit-border-radius: 6px;
  -moz-border-radius: 6px;
  border-radius: 6px;
}


````

#### 多参数的Mixins
规则如下：
- 两个参数，且每个参数的多个值用逗号隔开
```.name(1,12,3; something,else) ```
- 三个参数，且每个参数值包含一个值
```.name(1,2,3) ```
- 一个参数，且带多个值
```.name(1,2,3) ```
- 一个参数，带默认值
```.name(@params1:red,blue) ```


例子1:
a.less
````
.mixin(@color) {
  color-1: @color;
}
.mixin(@color; @padding: 2) {
  color-2: @color;
  padding-2: @padding;
}
.mixin(@color; @padding; @margin: 2) {
  color-3: @color;
  padding-3: @padding;
  margin: @margin @margin @margin @margin;
}
.some .selector div {
  .mixin(#008000);
}

````
生成a.css
````
.some .selector div {
  color-1: #008000;
  color-2: #008000;
  padding-2: 2;
}

````

#### 命名参数
例子:
a.less
````
.mixin(@color: black; @margin: 10px; @padding: 20px) {
  color: @color;
  margin: @margin;
  padding: @padding;
}
.class1 {
  .mixin(@margin: 20px; @color: #33acfe);
}
.class2 {
  .mixin(#efca44; @padding: 40px);
}

````
生成a.css
````
.class1 {
  color: #33acfe;
  margin: 20px;
  padding: 20px;
}
.class2 {
  color: #efca44;
  margin: 10px;
  padding: 40px;
}

````

#### 使用@arguments的参数
@argument表示传入的所有的参数
例子：
a.less
````
.box-shadow(@x: 0; @y: 0; @blur: 1px; @color: #000) {
  -webkit-box-shadow: @arguments;
     -moz-box-shadow: @arguments;
          box-shadow: @arguments;
}
.big-block {
  .box-shadow(2px; 5px);
}

````
生成a.css
````
.big-block {
  -webkit-box-shadow: 2px 5px 1px #000;
     -moz-box-shadow: 2px 5px 1px #000;
          box-shadow: 2px 5px 1px #000;
}

````

#### Avanced Arguments 和 @rest变量
规则例子如下：
````
.mixin(...){}//matches 0-N arguments
.mixin(){}// matches exactly 0 arguments
.mixin(@a:1){} //matches 0-1 arguments
.mixin(@a:1,...){}//match 0-N arguments
.mixin(@a;...){} //matches 1-N arguments

.mixin(@a;@rest...){
	//@rest is bound to arguments after @a
	// arguments is bound to  all arguments
}

````

#### Pattern-matching
规则如：
````
.mixin(@s;@color){ ... }

.class{
	.mixin(@switch;#888);
}

````

例子：
a.less
````
.mixin(dark; @color) {
  color: darken(@color, 10%);
}
.mixin(light; @color) {
  color: lighten(@color, 10%);
}
.mixin(@_; @color) {
  display: block;
}

````
生成a.css
````
.class {
  color: #a2a2a2;
  display: block;
}

````

#### Mixins as Function 
返回变量或mixins 从mixins中

例子1：
a.less
````
.mixin() {
  @width:  100%;
  @height: 200px;
}

.caller {
  .mixin();
  width:  @width;
  height: @height;
}

````
生成a.css
````
.caller {
  width:  100%;
  height: 200px;
}

````

例子2：
a.less
````
.average(@x, @y) {
  @average: ((@x + @y) / 2);
}

div {
  .average(16px, 50px); // "call" the mixin
  padding: @average;    // use its "return" value
}

````
生成a.css
````
div {
  padding: 33px;
}

````

在调用者区域中直接定义的变量是不能被覆盖的。然而，定义在调用者的父区域的变量是不被保护的，会被覆盖的。
例子3：
a.less
````
.mixin() { 
 @size: in-mixin; 
  @definedOnlyInMixin: in-mixin;
}

.class {
  @size: ain-mixin;
  margin: @size @definedOnlyInMixin;
  .mixin();
}

@size: globaly-defined-value;  // callers parent scope - no protection

````
生成a.css
````
.class {
  margin: ain-mixin in-mixin;
}

````

定义在mixin中的mixin 也可以作为返回值例如：
a.less
````
.unlock(@value) { // outer mixin
  .doSomething() { // nested mixin
    declaration: @value;
  }
}

#namespace {
  .unlock(5); // unlock doSomething mixin    运行这个返回  mixin作为变量定义
  .doSomething(); //nested mixin was copied here and is usable   执行变量
}

````
生成a.css
````
#namespace {
  declaration: 5;
}

````

#### 传递Rulesets to Mixins
允许在mixin中定义一个包裹css
例如：
a.less
````
// declare detached ruleset
@detached-ruleset: { background: red; };

// use detached ruleset
.top {
    @detached-ruleset(); 
}

````
生成a.css
````
.top {
  background: red;
}

````

#### Mixin Guards  (即条件mixins)
例子：
a.less
````
.mixin (@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin (@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin (@a) {
  color: @a;
}
.class1 { .mixin(#ddd) }
.class2 { .mixin(#555) }


````
运行出的a.css
````
.class1 {
  background-color: black;
  color: #ddd;
}
.class2 {
  background-color: white;
  color: #555;
}


````
下面两种表达方式等价：
````
.truth(@a) when (@a){ ... }
.truth(@a) when (@a = true){ ... }

````
传入的值如果不是true, 都是无法匹配的
````
.class{
	.truth(40);//will not match any of the above definitions
}

.class{
	.truth(true);// will match any of the above definitions
}

````


使用‘and’关键字来调试‘与’条件：
```
 .minxin(@a) when (isnumber(@a)) and (@a > 0){ ... }
 //只有传入的@a的值满足即是数字且数字大于0才能匹配

```
使用“,”关键字表示“或”条件：
````
.mixin(@a) when(@a > 10), (@a <10){ ... }
//传入的@a的值大于10或小于10  才匹配

````
使用“not”关键字 表示“非”条件
````
.mixin(@b) when not(@b > 0){ ... }
//传入的@b的值 不满足大于0时，才匹配

````

内置的type检查函数：
- iscolor
- isnumber
- isstring
- iskeyword
- isurl

If you want to check if a value is in a specific unit in addition to being a number, you may use one of :
- ispixel
- ispercentage
- isem
- isunit

例子：
````
.mixin (@a; @b: 0) when (isnumber(@b)) { ... }
.mixin (@a; @b: black) when (iscolor(@b)) { ... }

````



其他条件mixins
````
.mixin (@a) when(@a > 0){ ... }
.mixin (@a) when(default()){ ... } //matches only if first mixin does not ,即when @a <= 0

````




### 嵌套规则

#### 嵌套的directives 和 bubbling
directives :如 media,keyframe , support, document
例子1：
a.less
````
.screen-color {
  @media screen {
    color: green;
    @media (min-width: 768px) {
      color: red;
    }
  }
  @media tv {
    color: black;
  }
}

````
生成a.css
````
@media screen {
  .screen-color {
    color: green;
  }
}
@media screen and (min-width: 768px) {
  .screen-color {
    color: red;
  }
}
@media tv {
  .screen-color {
    color: black;
  }
}


````
没有条件的diretive,例如：font-face 或keyframes
例子2：
a.less
````
#a {
  color: blue;
  @font-face {
    src: made-up-url;
  }
  padding: 2 2 2 2;
}

````
生成a.css
````
#a {
  color: blue;
  padding: 2 2 2 2;
}
@font-face {
  src: made-up-url;
}

````


操作符+、-、*、/,如：
````
// example with variables
@base: 5%;
@filler: @base * 2; // result is 10%
@other: @base + @filler; // result is 15%

````


### 变量

例子：
````
// Variables
@link-color:        #428bca; // sea blue
@link-color-hover:  darken(@link-color, 10%);

// Usage
a,
.link {
  color: @link-color;
}
a:hover {
  color: @link-color-hover;
}
.widget {
  color: #fff;
  background: @link-color;
}

````

#### 变量插值
- selector例子：
a.less
````
// Variables
@my-selector: banner;

// Usage
.@{my-selector} {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}

````
生成a.css
````
.banner {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}

````

- url例子：
a.less
````
// Variables
@images: "../img";

// Usage
body {
  color: #444;
  background: url("@{images}/white-sand.png");
}

````
生成a.css
````
body {
  color: #444;
  background: url("../img/white-sand.png");
}

````

- import statements
例子：
a.less
````
// Variables
@themes: "../../src/themes";

// Usage
@import "@{themes}/tidal-wave.less";  //编译之后相当于 @import "../../src/themes/tidal-wave.less"

````

- Properties
例子：
a.less
````
@property: color;

.widget {
  @{property}: #0ee;
  background-@{property}: #999;
}

````
生成a.css
````
.widget {
  color: #0ee;
  background-color: #999;
}

````

- variable names
例子：
a.less
````
@fnord:'I am fnord';
@var :'fnord';
content:@@var;

````
生成a.css
````
content:'I am fnord';

````

- lazy loading
less片段1：
````
.lazy-eval {
  width: @var;
}

@var: @a;
@a: 9%;

````

less片段2：
````
.lazy-eval-scope {
  width: @var;
  @a: 9%;
}

@var: @a;
@a: 100%;

````
less片段1和2编译的结果一样：
````
.lazy-eval-scope {
  width: 9%;
}

````

例子：
````
@var: 0;
.class {
  @var: 1;
  .brass {
    @var: 2;
    three: @var;
    @var: 3;
  }
  one: @var;
}

````
编译后生成css:
````
.class {
  one: 1;
}
.class .brass {
  three: 3;
}

````



