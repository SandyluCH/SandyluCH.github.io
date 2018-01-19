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

