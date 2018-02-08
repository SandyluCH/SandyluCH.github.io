### render-list.js    : reading notes
 所在目录：src\core\instance\render-helpers
 
#### flow-bin  
Flow是一个静态的检查类型检查工具，设计之初的目的就是为了可以发现JavaScript脚本里不容易被发现的错误。
在js开发的过程中，总会遇到一些问题。小的还可以，比如用alert或者console等输出一些信息可以debug，并解决。
但是如果项目比较大的时候，这些手法只能起到一定的辅助作用。更有甚者，有些问题不运行到那段代码，根本不会
发现错误。Facebook的兄弟们就是为了解决这个问题，于是开发了flow。
[flow-bin](https://github.com/flowtype/flow-bin);
[查看官网](https://flow.org/);

1. flow中支持的 primitive types  有  Booleans,String,Numbers,null,void(相当于js中的undefined).
2. flow支持
[Type Annotations](https://flow.org/en/docs/types/);
[Primitive Types](https://flow.org/en/docs/types/primitives/);
[Literal Types](https://flow.org/en/docs/types/literals/);
[Mixed Types](https://flow.org/en/docs/types/mixed/);
[Any Types](https://flow.org/en/docs/types/any/);
[Maybe Types](https://flow.org/en/docs/types/maybe/);
[Variable Types (current)](https://flow.org/en/docs/types/variables/);
[Function Types](https://flow.org/en/docs/types/functions/);
[Object Types](https://flow.org/en/docs/types/objects/);
[Array Types](https://flow.org/en/docs/types/arrays/);
[Tuple Types](https://flow.org/en/docs/types/tuples/);
[Class Types](https://flow.org/en/docs/types/classes/);
[Type Aliases](https://flow.org/en/docs/types/aliases/);
[Opaque Type Aliases](https://flow.org/en/docs/types/opaque-types/);
[Interface Types](https://flow.org/en/docs/types/interfaces/);
[Generic Types](https://flow.org/en/docs/types/generics/);
[Union Types](https://flow.org/en/docs/types/unions/);
[Intersection Types](https://flow.org/en/docs/types/intersections/);
[Typeof Types](https://flow.org/en/docs/types/typeof/);
[Type Casting Expressions](https://flow.org/en/docs/types/casting/);
[Utility Types](https://flow.org/en/docs/types/utilities/);
[Module Types](https://flow.org/en/docs/types/modules/);
[Comment Types](https://flow.org/en/docs/types/comments/);

## vue使用的测试框架
1. jasmine  单元测试（test/ssr）
2. karma-jasmine   是jasmine 测试框架的适配器
3. karma    是一个简单的工具，允许你可在多个真实的浏览器中执行JavaScript代码。
karma的主要目的是使你的测试驱动开发简单、快速和有趣。
相关的学习文章：[karma 测试框架的前世今生](https://juejin.im/entry/56c80331efa631005c1aeacc);
karma是 AngularJS team开发的工具
它的特点：
 + 1、真实设备测试
    测试你的真正的浏览器和真正的设备如手机代码，片或无头PhantomJS实例。
 + 2、远程控制
    从命令行或IDE控制整个工作流程-只需保存一个文件，业力就会运行所有的测试。
 + 3、测试框架无关
    描述 Jasmine, Mocha, QUnit的测试，或写任何你喜欢的一个简单的适配器框架。
 + 4、开放源代码
    开发和维护由开源社区在GitHub上。
 + 5、易于调试
    调试容易直接从您的IDE通过webstorm或谷歌浏览器。
 + 6、持续集成
    与 Jenkins, Travis or Semaphore的简单集成。
	
	#### 测试代码中使用到的
	1. es6-promise   .
	它是 es6-promise  的polyfill .  它的实现是  rsvp.js的子集（被 @jakearchibald从rsvp.js
	中抽取的）。如果你想要额外的特点和更多的debugging options,   那么就check out下所有的库。
	2、webpack  中require.context功能
	    我们可以使用require.context()函数来创建自己的上下文。它允许你传入参数：一个文件夹名去搜索；
	一个flag标记去表明子文件夹是否也需要被搜索到；一个正则表达式去去匹配需要查找的文件。
	    即require.context(文件夹名，flag标记，正则表达式)  
		----require.context(directory, useSubdirectories = false, regExp = /^\.\//);
		
		在building的同时，webpack 解析代码中的require.context。
		例如：
		<pre>
		   <code>
			   require.context("./test", false, /\.test\.js$/);
			   // a context with files from the test directory that can be required with a request endings with `.test.js`.
			   require.context("../", true, /\.stories\.js$/);
			   // a context with all files in the parent folder and descending folders ending with `.stories.js		       
		   </code>
		</pre>
		
		这个context 模块导出一个require函数（这个函数接收一个参数request）.
		这个导出的函数有3个属性： resolve,keys,id
		   1. resolve 是一个函数，返回parsed 请求的module的id值
		   2. keys 是一个函数，  返回一个数组（所有上下文模块可以处理的possible请求）
		   
		例子：
		<pre>
		    <code>
			//例子1：
			function importAll (r) {
			    r.keys().forEach(r);
			}
			
			importAll(require.context('../components/', true, /\.js$/));			
			
            //例子2：
			var cache = {};
			function importAll (r) {
      			r.keys().forEach(key => cache[key] = r(key));
			}
			
			importAll(require.context('../components/', true, /\.js$/));		
			
            </code>				
		</pre>
		
		
		
		
		
	
	
	
	
     
	
	

