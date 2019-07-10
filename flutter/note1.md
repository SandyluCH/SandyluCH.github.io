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

### Input

### 动画和Motion

### 交互模型

### 样式

### 绘制和效果

### Async

### 滚动

### 辅助功能

