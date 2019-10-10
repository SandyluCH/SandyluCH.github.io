## 介绍
Rollup是一个JavaScript模块打包器，可以将小块代码编译成大块复杂的代码，例如library或引用程序。Rollup对代码模块使用的新的标准化格式，这些标准都包含在JavaScript的ES6版本中，而不是以前的特殊解决方案，如CommonJS和AMD。ES6模块可以使你自由、无缝地使用你最喜爱的library中的那些最有用的独立函数，而你的项目不必携带其他未使用的代码。ES6模块最终还是要由浏览器原生实现，但当前Rollup可以使你提前体验。

## Tree-shaking
除了使用ES6模块之外，Rollup还静态分析代码中的import, 并将排除任何未实际使用的代码。这允许您架构于现有的工具和模块之上，而不会增加额外的依赖或使项目的大小膨胀。
例如， 在使用CommonJS时，必须导入（import）完整的工具（tool）或库（library）对象。
````
// 使用 CommonJS 导入(import)完整的 utils 对象
var utils = require( 'utils' );
var query = 'Rollup';
// 使用 utils 对象的 ajax 方法
utils.ajax( 'https://api.example.com?search=' + query ).then( handleResponse );

````

但是在使用ES6模块时，无需导入整个utils对象， 我们可以只导入（import）我们需要的ajax函数：
````
// 使用 ES6 import 语句导入(import) ajax 函数
import { ajax } from 'utils';
var query = 'Rollup';
// 调用 ajax 函数
ajax( 'https://api.example.com?search=' + query ).then( handleResponse );

````


因为Rollup只引入最基本最精简代码，所以可以生成轻量、快速，以及低复杂度的library和应用程序。因为这种基于显式的import和export语句的方式，它远比“在编译后的输出代码中，简单地运行自动minifier检查未使用的变量”更有效。


## 教程

### 创建第一个bundle

1、 全局安装Rollup, 使用命令
````
npm install rollup --global
或者
npm i rollup -g

````

2、创建项目
````
mkdir -p my-rollup-project/src
cd my-rollup-project
````
新建文件src/main.js:
````
import foo from './foo.js';
export default function(){
  console.log(foo);
}

````
创建foo.js模块：
````
export default 'hello world';

````

3、 创建bundle
````
rollup src/main.js -f cjs

````

### 使用配置文件（Using config files）

如果添加更多的选项，这种命令行的方式就显得麻烦。为此，我们可以创建配置文件来囊括所需的选项。配置文件由JavaScript写成，比Cli更加灵活， 在项目中创建一个名为rollup.config.js, 增加如下代码：
````
// rollup.config.js
export default{
  input:'src/main.js',
  output:{
    file:'bundle.js',
    format:'cjs'
  }
}

````
我们用--config或-c来使用配置文件；
````
rm bundle.js
rollup -c
````
同样的命令行选项将会覆盖配置文件中的选项：
````
rollup -c -o bundle-2.js # `-o` is short for `--output.file`

````
注意Rollup本身会处理配置文件，所以可以使用export default语法——代码不会经过Babel等类似工具编译，所以只能使用所用Node.js版本支持的ES2015语法。

也可以指定与默认rollup.config.js文件不同的配置文件：
````
rollup --config rollup.config.dev.js
rollup --config rollup.config.prod.js

````

### 使用插件
构建更复杂的bundle， 通常需要更大的灵活性——引入npm安装的模块，通过Babel编译代码、和JSON文件打交道等。
我们可以用插件plugins在打包的关键过程中更改Rollup的行为。
例如： 使用rollup-plugin-json, 令Rollup从JSON文件中读取数据。
将rollup-plugin-json安装为开发依赖：
```npm install --save-dev rollup-plugin-json```

更新src/main.js文件， 从package.json而非src/foo.js中读取数据：
````
// src/main.js
import { version } from '../package.json';

export default function () {
  console.log('version ' + version);
}

````

编辑rollup.config.js文件，加入JSON插件：
````
// rollup.config.js
import json from 'rollup-plugin-json';

export default {
  input: 'src/main.js',
  output: {
    file: 'bundle.js',
    format: 'cjs'
  },
  plugins: [ json() ]
};

````

npm run build 执行Rollup。结果如下：
````
'use strict';

var version = "1.0.0";

var main = function () {
  console.log('version ' + version);
};

module.exports = main;

````



## 配置文件详解（Configuration files）
Rollup的配置文件是可选的，但是使用配置文件的作用很强大，而且很方便。
配置文件是一个ES6模块，它对外暴露一个对象，这个对象包含了一些Rollup需要的一些选项。通常，我们把这个配置文件叫做rollup.config.js, 它通常位于项目的根目录。

[配置选项列表说明](https://www.rollupjs.com/guide/big-list-of-options/)
例如：
````
// rollup.config.js
export default {
  // 核心选项
  input,     // 必须
  external,
  plugins,

  // 额外选项
  onwarn,

  // danger zone
  acorn,
  context,
  moduleContext,
  legacy

  output: {  // 必须 (如果要输出多个，可以是一个数组)
    // 核心选项
    file,    // 必须
    format,  // 必须
    name,
    globals,

    // 额外选项
    paths,
    banner,
    footer,
    intro,
    outro,
    sourcemap,
    sourcemapFile,
    interop,

    // 高危选项
    exports,
    amd,
    indent
    strict
  },
};

````
你必须使用配置文件才能执行以下操作：
- 把一个项目打包，然后输出多个文件；
- 使用Rollup插件，例如rollup-plugin-node-resolve 和 rollup-plugin-commonjs。 这两个插件可以让你加载Node.js里面的CommonJS模块。

如果你想使用Rollup的配置文件，记得在命令行里加上--config或者-c
````
# 默认使用rollup.config.js
$ rollup --config

# 或者, 使用自定义的配置文件，这里使用my.config.js作为配置文件
$ rollup --config my.config.js

````

## 命令行的参数（Command line flags）
配置文件中的许多选项和命令行的参数是等价的。如果你使用这里的参数， 那么将重写配置文件。
````
-i, --input                 要打包的文件（必须）
-o, --output.file           输出的文件 (如果没有这个参数，则直接输出到控制台)
-f, --output.format [es]    输出的文件类型 (amd, cjs, es, iife, umd)
-e, --external              将模块ID的逗号分隔列表排除
-g, --globals               以`module ID:Global` 键值对的形式，用逗号分隔开 
                              任何定义在这里模块ID定义添加到外部依赖
-n, --name                  生成UMD模块的名字
-m, --sourcemap             生成 sourcemap (`-m inline` for inline map)
--amd.id                    AMD模块的ID，默认是个匿名函数
--amd.define                使用Function来代替`define`
--no-strict                 在生成的包中省略`"use strict";`
--no-conflict               对于UMD模块来说，给全局变量生成一个无冲突的方法
--intro                     在打包好的文件的块的内部(wrapper内部)的最顶部插入一段内容
--outro                     在打包好的文件的块的内部(wrapper内部)的最底部插入一段内容
--banner                    在打包好的文件的块的外部(wrapper外部)的最顶部插入一段内容
--footer                    在打包好的文件的块的外部(wrapper外部)的最底部插入一段内容
--interop                   包含公共的模块（这个选项是默认添加的）

````
