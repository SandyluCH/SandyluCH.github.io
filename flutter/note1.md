## Widgets目录
### 基础组件
#### 1、Container
container: 一个拥有painting, positioning, sizing的widget。

#### 2、Row
在水平方向上排列子widget的列表。

#### 3、Column
在垂直方向上排列子widget的列表。

#### 4、 Image
一个显示图片的widget。

#### 5、Text
单一格式的文本

#### 6、ICON

#### 7、RaisedButton
Material Design中的button， 一个凸起的材质矩形按钮

#### 8、Scaffold
Material Design布局结构的基本实现。此类提供了用于显示drawer、snackbar和底部sheet的API。

#### 9、 Appbar
一个Material Design应用程序栏，由工具栏和其他可能的widget（如TabBar和FlexibleSpaceBar）组成。

#### 10、 FlutterLogo
Flutter logo, 以widget形式. 这个widget遵从IconTheme。

#### 11、 Placeholder
一个绘制了一个盒子的的widget，代表日后有widget将会被添加到该盒子中

### Material Components

实现了Material Design指南的视觉、效果、motion-rich的widget。

App结构和导航：
- Scaffold Material Design布局结构的基本实现。此类提供了用于显示drawer、snackbar和底部sheet的API
- Appbar 
一个Material Design应用程序栏，由工具栏和其他可能的widget(如TabBar和FlexibleSpaceBar)组成。
- BottomNavigationBar 
底部导航条， 可以很容易的在tap之间切换和浏览顶级视图。
- TabBar 
一个显示水平选项卡的Material Design widget.
- TabBarView 
显示与当前选中的选项卡相对应的页面视图。通常和TabBar一起使用。
- MaterialApp 
一个方便的widget, 它封装了应用程序实现Material Design 所需要的一些widget.
- WidgetsApp
一个方便的类， 它封装了应用程序通常需要的一些widget.
- Drawer
从scaffold边缘水平滑动以显示应用程序中导航链接的Material Design面板

按钮
- RaisedButton 
MaterialDesign 中的button, 一个凸起的材质矩形按钮
- FloatingActionButton 
一个圆形图标按钮，它悬停在内容之上，以展示应用程序中的主要动作。FloatingActionButton通常用于Scaffload.floatingActionButton字段。
- FlatButton 
一个扁平的material按钮
- IconButton 
一个material图标按钮，点击时会有水波动画
- PopupMenuButton 
当菜单隐藏式， 点击或调用onSelected时显示一个弹出式菜单列表
- ButtonBar
水平排列的按钮组

输入框和选择框
- TextField
- Checkbox
- Radio
- Switch On/Off用于切换一个单一状态
- Slider 滑块，允许用户通过滑动滑块来从一系列值中选择
- Date & Time Pickers 日期&时间选择器

对话框、Alert、 Panel
- SimpleDialog 简单对话框可以显示附加的提示或操作
- AlertDialog 一个会中断用户操作的对话款，需要用户确认
- BottomSheet BottomSheet是一个从屏幕底部滑起的列表（以显示更多的内容）。 你可以调用showBottomSheet或showModalBottomSheet弹出
- ExpansionPanel
- SnackBar 具有可选操作的轻量级消息弹框，在屏幕底部显示。

信息展示
- Image
- Icon
- Chip 标签，一个Material widget。 它可以将一个复杂内容实体展现在一个小块中，如联系人。
- ToolTIP
- DataTable
- Card
- LinearProressIndicator 一个线性进度条，另外还有一个圆形进度条CircularProgressIndicator


布局
- ListTile 一个固定高度的行，通常包含一些文本，以及一个行前或行尾图标。
- Stepper 一个Material Design步骤指示器，显示一系列步骤的过程。
- Divider 一个逻辑1像素厚的水平分割线，两边都有填充



### Cupertino(iOS风格的widget)
用于当前iOS设计语言的美丽的高保真的widget.

- CupertinoActivityIndicator 一个iOS风格的loading指示器。显示一个圆形的转圈菊花。
- CupertinoAlertDialog  iOS风格的alert dialog.
- CupertinoButton iOS风格的button
- CupertinoDialog iOS风格的对话框
- CupertinoDialogAction 通常用于CupertinoAlertDialog的一个button
- CupertinoSlider 从一个范围中选一个值
- CupertinoSwitch iOS风格的开关，用于单一状态的开/关
- CupertinoPageTransition 提供iOS风格的页面过度动画
- CupertinoFullscreenDialogTransition 一个ios风格的过渡， 用于调用全屏对话框。
- CupertinoNavigationBar iOS风格的导航栏， 通常和CupertinoPageScaffold一起使用。
- CupertinoTabBar iOS风格的底部选项卡，通常和CupertinoTabScaffold一起使用。
- CupertinoPageScaffold 一个iOS风格的页面基本布局结构。包含内容和导航栏。
- CupertinoTabScaffold 标签式ios应用程序的结构。将选项卡栏放在内容选项卡之上
- CupertinoTabView 支持选项卡间并行导航项卡的根内容， 通常和CupertinoTabScaffold一起使用


### Layout
排列其他widget的columns、rows、grids和其他的layouts.

拥有单个子元素的布局widget:
- 1、Container
一个拥有绘制、定位、调整大小的widget
- 2、Padding
一个widget, 会给其子widget添加指定的填充。
- 3、Center
将其子widget居中显示在自身内部的widget
- 4、Align
一个widget, 它可以将其子widget对齐、并可以根据子widget的大小自动调整大小
- 5、FittedBox
按照自己的大小调整其子widget的大小和位置
- 6、AspectRatio
一个widget， 视图将子widget的大小指定为某个特定的长宽比。
- 7、ConstrainedBox
对其子项施加附加约束的widget
- 8、Baseline
根据子项的基线对他们的位置进行定位的widget
- 9、FractionallySizedBox
一个widget, 它把它的子项放在可用空间的一小部分。
- 10、IntrinsicHeight
一个widget, 它将它的子widget的高度调整其本身实际的高度
- 11、IntrinsicWidth
一个widget, 它将它的子widget的宽度调整其本身实际的宽度
- 12、LimitedBox
一个当其自身不受约束时才限制其大小的盒子。
- 13、Offstage
一个布局widget, 可以控制其子widget的显示和隐藏。
- 14、OverflowBox
对其子项施加不同约束的widget, 它可能允许子项溢出父级。
- 15、SizedBox
一个特定大小的盒子。这个widget强制它的孩子有一个特定的宽度和高度。如果宽度和高度都为NULL,则此widget将调整自身大小以匹配该维度中的孩子的大小。
- 16、SizedOverflowBox
一个特定大小的widget, 但是会将它的原始约束传递给他的孩子，他可能会溢出。
- 17、Transform
在绘制子widget之前应用转换的widget
- 18、CustomSingleChildLayout
一个自定义的拥有单个子widget的布局widget

拥有多个子元素的布局的widget
- Row 在水平方向上排列子widget的列表
- Column 在垂直方向上排列子widget的列表
- Stack  可以允许其子widget简单的堆叠在一起
- IndexedStack 从一个子widget列表中显示单个孩子的Stack
- Flow 一个实现流式布局算法的widget
- Table 为其子widget使用表格布局算法的widget
- Wrap 可以在水平或垂直方向多行显示其子widget
- ListBody 一个widget, 它沿着一个给定的轴，顺序排列它的子元素
- ListView 可滚动的列表控件。ListView是最常用的滚动widget, 它在滚动方向上一个接一个地显示他的孩子。在纵轴上，孩子们被要求填充ListView.
- CustomMultiChildLayout 
使用一个委托来对多个孩子进行设置大小和定位的小部件。

Layout helpers
- LayoutBuilder 构建一个可以依赖父窗口大小的widget树。



### Text
- Text
- RichText 一个富文本Text, 可以显示多种样式的Text
- DefaultTextStyle 文字样式，用于指定Text widget的文字样式


### Assets、图片、ICONS
- Image 一个显示图片的widget
- Icon  A Material Design icon
- RawImage 一个直接显示dart:ui.Image的widget
- AssetBundle 包含应用程序可以使用的资源，如图像和字符串。对这些资源的访问是异步，所以他们可以来自网络（例如，从NetworkAssetBundle）或从本地文件系统，这并不会挂起用户界面。

### 表单组件
- Form   一个可选的、用于给多个TextField分组的widget
- FormFiled 一个单独的表单字段。此widget维护表单字段的当前状态，以便在UI中直观地反映更新和验证错误。
- RawKeyboardListener 每当用户按下或释放键盘上的调用回调的widget


### 动画和Motion
- AnimatedContainer 在一段时间内逐渐改变其值的容器
- AnimatedCrossFade 一个widget， 在两个child之间交叉淡入，并同时调整他们的尺寸
- Hero 将其子项标记为hero动画候选的widget。
- AnimatedBuilder 用于构建动画的通用小部件。AnimatedBuilder在有多个widget希望有一个动画作为一个较大的建造函数部分时会非常有用。要使用AnimatedBuilder，只需构建widget并将其传给builder函数即可。
- DecoratedBoxTransition  DecoratedBox的动画版本， 可以给他的Decoration不同属性使用动画
- FadeTransition 对透明度使用动画的widget
- PositionedTransition Positioned的动画版本，它需要一个特定的动画来将孩子的位置从动画的生命周期的起始位置移到结束位置。
- RotationTransition 对widget使用旋转动画
- ScaleTransition  对widget使用缩放动画
- SizeTransition  Animated its own size and clips and aligns the child
- SlideTransition 对相对于其正常位置的某个位置之间使用动画
- AnimatedDefaultTextStyle 在文本样式切换时使用动画
- AnimatedListState 动画列表的state
- AnimatedModalBarrier 一个阻止用户与widget交互的widget
- AnimatedOpacity  在给定的透明度变化时，自动地在给定的一段时间内改变孩子的Opacity
- AnimatedPhysicalModel 
- AnimatedPositioned 每当给定位置变化，自动在给定的时间内转换孩子的位置
- AnimatedSize 当给定的孩子的大小变化时，它自动的在给定时间内转换它的大小
- AnimatedWidget 当给定的listenable改变值时，会重新构建该widget
- AnimatedWidgetBaseState 具有隐式动画的widget的

### 交互模型widget
- LongPressDraggable  长按时可拖动
- GestureDetector 检测手势
- DragTarget 一个拖动的目标widget, 在完成拖动时它可以接收数据
- Dismissible 可以在拖动时隐藏的widget
- IgnorePointer 在hit test中不可见的widget。当ignoring为true时，此widget及其子树不响应事件。但它在布局过程中仍然消耗空间，并像往常一样绘制它的孩子。它是无法捕获事件对象、因为它在RenderBox.hitTest中返回false
- AbsorbPointer  在hit test期间吸收(拦截)事件。当absorbing为true时，此小部件阻止其子树通过终止命中测试来接收指针事件。它在布局过程中仍然消耗空间，并像往常一样绘制它的孩子。它只是防止其孩子成为事件命中目标，因为它从RenderBox.hitTest返回true。
- Navigator 导航器， 可以在多个页面（路由）栈之间跳转
- Scrollable 实现了可滚动widget的交互模型， 但不包含UI显示相关的逻辑


### 样式widget
- Padding   一个widget, 会给其子widget添加指定的填充
- Theme  将主题应用于子widget。 主题描述了应用选择的颜色和字体。
- MediaQuery 建立一个子树， 在树中媒体查询解析不同的给定数据

### 绘制和效果
- Opacity 使其子widget透明的widget
- Transform 在绘制子widget之前应用转换的widget
- DecoratedBox 在孩子绘制之前或之后绘制装饰的widget
- FractionalTranslation 绘制盒子之前给其添加一个偏移转换
- RotatedBox  可以沿顺时针以90度的倍数旋转其子widget
- ClipOval 用椭圆剪辑其孩子的widget
- ClipPath 用path剪辑其孩子的widget
- ClipRect 用矩形剪辑其孩子的widget
- CustomPaint 提供一个画布的widget, 在绘制阶段可以在画布上绘制自定义的图形
- BackdropFilter 一个widget, 它将过滤应用到现有的绘图内容，然后绘制child。 这种效果是比较昂贵的， 尤其是如果过滤器是non-local

### Async widget
- FutureBuilder 基于与Future交互的最新快照来构建自身的widget
- StreamBuilder 基于流交互的最新快照构建自身widget

### 可滚动的widget
- ListView
- NestedScrollView  可以嵌套其他可滚动的widget的widget
- GridView 一个可滚动的二维空间数组
- SingleChildScrollView 有一个子widget的可滚动的widget, 子内容超过父容器时可以滚动
- Scrollable 实现了可滚动widget的交互模型， 但不包含UI显示相关的逻辑
- Scrollbar  一个Material Design滚动条， 表示当前滚动到了什么位置
- CustomScrollView 一个使用slivers创建自定义的滚动效果的ScrollView
- NotificationListener 一个用来监听树上冒泡通知的widget
- ScrollConfiguration 控制可滚动组件在子树中的表现行为
- RefreshIndicator  Material Design下拉刷新指示器， 包装一个可滚动的widget



### 辅助功能
- Semantics 
一个widget，用以描述widget树的具体语义。使用辅助工具、搜索引擎和其他语义分析软件来确定应用程序的含义。
- MergeSemantics 
合并其后代语义的widget
- ExcludeSemantics 
删除其后代所有语义的widget

