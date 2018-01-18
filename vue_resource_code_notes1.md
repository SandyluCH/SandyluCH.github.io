###render-list.js    : reading notes
所在目录：src\core\instance\render-helpers
####flow-bin  
Flow是一个静态的检查类型检查工具，设计之初的目的就是为了可以发现JavaScript脚本里不容易被发现的错误。
在js开发的过程中，总会遇到一些问题。小的还可以，比如用alert或者console等输出一些信息可以debug，并解决。
但是如果项目比较大的时候，这些手法只能起到一定的辅助作用。更有甚者，有些问题不运行到那段代码，根本不会
发现错误。Facebook的兄弟们就是为了解决这个问题，于是开发了flow。
[flow-bin](https://github.com/flowtype/flow-bin);
[查看官网](https://flow.org/);

1、flow中支持的 primitive types  有  Booleans,String,Numbers,null,void(相当于js中的undefined).
2、flow支持
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