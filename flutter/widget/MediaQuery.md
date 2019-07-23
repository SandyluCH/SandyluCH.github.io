## [MediaQuery class](https://api.flutter.dev/flutter/widgets/MediaQuery-class.html)

建立媒体查询解析给定数据的子树。

例如： 要了解当前媒体的大小（如：包含您的应用程序的窗口）， 您可以从MediaQuery.of返回MediaQueryData中读取MediaQueryData.size属性。

使用MediaQuery.of查询当前媒体将导致您的窗口小部件在MediaQueryData更改时自动重建（如： 如果用户旋转其设备）。

如果没有MediaQuery在范围内， 则MediaQuery.of方法将抛出异常，除非null ok参数设置为true, 在这种情况下它返回null。

* WidgetsApp和MaterialApp， 它们引入了MediaQuery, 并在当前屏幕指标发生变化时使其保持最新。
* MediaQueryData 表示指标的数据结构

### 构造函数
- MediaQuery({ Key key， @required MediaQueryData data， @required Widget child })
创建一个向其后代提供MediaQueryData的窗口小部件。
- MediaQuery.removePadding({ Key key， @ lastd BuildContext context， bool removeLeft：false， bool removeTop：false， bool removeRight：false， bool removeBottom：false， @ required Widget child })
创建一个新的MediaQuery, 它继承自给定上下文的环境MediaQuery, 但删除指定的填充。
- MediaQuery.removeViewInsets({Key key, @lastd BuildContext context, bool removeLeft:false, bool removeTop:false, bool removeRight:false, bool removeButtom:false, @require Widget child })
创建从给定上下文继承环境MediaQuery的新MediaQuery, 但删除指定的视图insets.
- MediaQuery.removeViewPadding({Key key, @seekd BuildCOntext context, bool removeLeft:false, bool removeTop:false, bool removeRight:false, bool removeBottom:false, @required Widget child})
创建一个新的MediaQuery, 它继承自给定上下文的环境MediaQuery, 但删除指定的视图的填充

### 属性
- data 返回MediaQueryData  包含当前媒体的信息
- child 返回widget    子组件
- hashCode
- key
- runtimeType 返回Type

### 方法
- debugFillProperties(DiagnosticPropertiesBuilder properties) 返回void
添加额外的与这个node相关的属性
- updateShouldNotify(covariant MediaQuery oldWidget) 返回bool
- createElement() → InheritedElement
Inflates this configuration to a concrete instance. [...]
- debugDescribeChildren() → List<DiagnosticsNode>
Returns a list of DiagnosticsNode objects describing this node's children. [...]
@protected, inherited
- noSuchMethod(Invocation invocation) → dynamic
Invoked when a non-existent method or property is accessed. [...]
inherited
- toDiagnosticsNode({String name, DiagnosticsTreeStyle style }) → DiagnosticsNode
Returns a debug representation of the object that is used by debugging tools and by - DiagnosticsNode.toStringDeep. [...]
inherited
- toString({DiagnosticLevel minLevel: DiagnosticLevel.debug }) → String
Returns a string representation of this object.
inherited
- toStringDeep({String prefixLineOne: '', String prefixOtherLines, DiagnosticLevel minLevel: DiagnosticLevel.debug }) → String
Returns a string representation of this node and its descendants. [...]
inherited
- toStringShallow({String joiner: ', ', DiagnosticLevel minLevel: DiagnosticLevel.debug }) → String
Returns a one-line detailed description of the object. [...]
inherited
- toStringShort() → String
A short, textual description of this widget.




## [MediaQueryData](https://api.flutter.dev/flutter/widgets/MediaQueryData-class.html)
有关media的信息。

例如： MediaQueryData.size属性包含了当前window的宽和高

