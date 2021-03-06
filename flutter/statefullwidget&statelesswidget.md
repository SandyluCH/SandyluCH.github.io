## StatefulWidget 
StatefulWidget是具有可变状态的小部件。
State是在构建窗口小部件时可同步读取的信息，以及在窗口小部件的生命周期内可能会更改的信息。当状态改变时，使用State.setState去同步通知状态的改变。


StatefulWidget实例本身是不可变的，并且将它们的可变状态存储在由createState方法创建的单独State对象中，或者存在在State订阅的对象中， 如Stream 或ChangeNotifier对象，其引用存储在StatefulWidget本身。

无论何时inflates 这个StatefulWidget组件， 框架都会调起createState方法， 这意味着， 如果widget被插入到组件树中的多个位置， 多个state对象将会关联上相同的StatefullWidget。 同理，如果statefulwidget被从组件树中移除， 然后又被插入到组件树中， 框架将会调起createState方法 去创建一个新的state 对象， 简化state对象的生命周期。

如果StatefulWidget的创建这使用GlobalKey作为它的key值，当StatefulWidget 从组件树中的一个位置移到另一个位置时，他仍然拥有相同的state对象。因为使用GlobalKey的组件在组件树中最多只能在一个地方使用，使用GlobalKey的组件也最多只能有一个相关联的元素，所以框架利用这个属性将带有GlobalKey的组件从树中的一个位置移动到另一个位置是通过嫁接与这个组件相关联的唯一子树从一个位置到另一个位置（而不是在新的位置重新创建一个子树）。 与StatefulWidget相关联的State对象也随着剩余子树一起被嫁接，也就意味着state 对象在新的位置被复用而不是recreated。 

## StatelessWidget
一个不需要可变状态的widget。

## State<T扩展StatefulWidget>类
StatefulWidget的逻辑和内部状态。

State是： 
- 1、在构建窗口小部件时可以同步读取的信息
- 2、在窗口小部门的声明周期内可能会更改的信息

State对象是有StatefulWidget.createState方法来创建。

State对象的生命周期:
- 框架通过调用StatefulWidget.createState来创建一个State对象
- 新创建的State对象与BuildContext相关联。此关联是永久性的， State对象永远不会更改其BuildContext。 但是， BuildContext本身可以与子树一起在树中移动。此时，State对象被认为已安装。
- 框架调用initState。 State的子类应覆盖initState以执行依赖于BuildContext或窗口小部件的一次性初始化， 这些初始化分别在调用initState方法时可用作上下文和widget属性。
- 该框架调用didChangeDependencies。State的子类应覆盖didChangeDependencies以执行涉及InheritedWidget的初始化。 如果调用BuildContext.inheritFromWidgetOfExactTtype, 则如果随后的widget更改或widget在树中移动，则将再次调用didChangeDepencies方法
- 此时， State对象已完全初始化，并且框架可能会多次调用其构建方法以获取此子树的用户界面的描述。state对象可以通过调用setState方法自发地请求重建其子树，这表明他们的某些内部状态已经以可能影响此子树中的用户界面的方式发生了更改。
- 在此期间， 父窗口小部件可能会重建并请求树中的此位置更新以显示具有相同的runtimeType和Widget.key的新窗口小部件。发生这种情况时，框架将更新窗口小部件属性以引用新窗口小部件，然后使用前一个窗口小部件作为参数调用 didUpdateWidget方法。状态 对象应覆盖didUpdateWidget以响应其关联窗口小部件中的更改（例如，启动隐式动画）。该框架总是调用建立呼叫后didUpdateWidget，这意味着任何呼叫的setState在didUpdateWidget 是多余的。
- 在开发期间，如果发生热重新加载（无论是flutter通过按下命令行工具r还是从IDE启动）， 都会调用重组方法。这提供了重新初始化在initState方法中准备的任何数据的机会。
- 如果从树中删除包含State对象的子树（例如，因为父级构建了具有不同runtimeType 或Widget.key的小部件），框架将调用deactivate方法。子类应该重写此方法以清除此对象与树中其他元素之间的任何链接（例如，如果您为祖先提供了指向后代的RenderObject的指针）。
- 此时，框架可能会将此子树重新插入树的另一部分。如果发生这种情况，框架将确保它调用build以使State对象有机会适应树中的新位置。如果框架重新插入此子树，它将在动画帧结束之前执行此操作，其中子树已从树中删除。因此，State对象可以推迟释放大多数资源，直到框架调用其dispose 方法。
- 如果框架没有在当前动画帧的末尾重新插入此子树，则框架将调用dispose，这表示此State对象永远不会再次构建。子类应重写此方法以释放此对象保留的任何资源（例如，停止任何活动动画）。
- 在框架调用dispose之后，State对象被认为是已卸载且mount属性为false。此时调用setState是错误的 。生命周期的这个阶段是终端：没有办法重新安装已经处置的State对象。

### 属性Properties
- context 
BuildContext类型
只读属性
widget 在树中创建的位置

- mounted
bool类型
只读属性
表示 state对象是否在树中

- widget
T
只读属性
当前的配置

- hashCode 
int类型
只读、继承 属性
this object的hash code值

- runtimeType
Type类型
只读、继承 属性

### Methods
- build(BuildContext context)——》 Widget
  describles the part of the user interface represented by this widget.
- deactivate()——》void
  Called when this object is removed from the tree
- debugFillProperties(DiagnosticPropertiesBuilder properties)——》 void
  add additional properties associated with the node.
- didChangeDependencies() ——》 void
  Called when a dependency of this State object changes
- didUpdateWidget(covariant T oldWidget)——》 void
  Called whenever the widget configuration changes
- dispose()   ——》 void
  Called when this object is removed from the tree permanently
- initState() ——》 void
  Called when this object is inserted into the tree
- reassemble() ——》 void
  Called whenever the application is reassembled during debugging.
- setState(VoidCallback fn) ——》 void
  Notify the framework that the internal state of this object has changed
- noSuchMethod(Invocation invocation) ——》 dynamic
  invoked when a non-existent method or property is accessed
- toDiagnosticsNode({String name, DiagnosticsTreeStyle style}) ——》 DiagnosticsNode
  Returns a debug representation of the object that is used by debugging tools and by DiagnosticsNode.toStringDeep
- toString({DiagnosticLevel minLevel: DiagnosticLevel.debug }) → String
Returns a string representation of this object.
- toStringShort() → String
A brief description of this object, usually just the runtimeType and the hashCode. 
