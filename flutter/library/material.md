## [material  library](https://api.flutter.dev/flutter/material/material-library.html)
实现了material design的flutter组件。

为了使用它， 需要引入package:flutter/material.dart.

### Classes
- AboutDialog 
一个关于盒子。 这个dialog 盒子有 应用icon、名字、版本号、以及版权，加一个展示应用软件的证书的按钮
- AboutListTile 
一个ListTile展示关于盒子
- AbsorbPointer 
在hit测试期间一个吸引指针的小部件
- Accumulator 
整数的可变包装器， 可以通过传递该包装器来跟踪递归堆栈中的值。
- Action 
actions的基本类
- ActionChip
一个material design action chip

### Functions
#### showSearch<T>({BuildContext context, SearchDelegate<T> delegate, String query: '' }) → Future<T>
展示一个全屏搜索页，当页面关闭时返回用户选择的搜索结果。

#### showTimePicker({BuildContext context, TimeOfDay initialTime, TransitionBuilder builder }) → Future<TimeOfDay>
展示一个对话框包含material design的time picker。
