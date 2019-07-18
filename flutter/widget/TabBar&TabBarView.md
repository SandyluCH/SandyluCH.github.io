## TabBar

小部件展示 a horizontal row of tabs。

通常在AppBar.bottom中创建作为AppBar的一部分，并且和TabBarView结合使用。

如果TabController没有提供， DefaultTabController 祖先必须提供。 tabController的长度必须等于tabs list的长度和 TabBarView.children的长度

## TabBarView
显示与当前所选选项卡对应的窗口小部件的页面视图。
此widget通常与TabBar结合使用。 如果未提供TabController, 则必须有DefaultTabController组件。
选项卡控制器的tabController的长度必须等于字列表的长度和TabBar.tabs列表的长度。
### 构造函数
````
const TabBarView({
Key key,
@required List<Widget> children,
TabController controller,
ScrollPhysics physics,
DragStartBehavior dragStartBehavior: DragStartBehavior.start
})

````

示例：
````
class MyTabbedPage extends StatefulWidget {
  const MyTabbedPage({ Key key }) : super(key: key);
  @override
  _MyTabbedPageState createState() => _MyTabbedPageState();
}

class _MyTabbedPageState extends State<MyTabbedPage> with SingleTickerProviderStateMixin {
  final List<Tab> myTabs = <Tab>[
    Tab(text: 'LEFT'),
    Tab(text: 'RIGHT'),
  ];

  TabController _tabController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(vsync: this, length: myTabs.length);
  }

 @override
 void dispose() {
   _tabController.dispose();
   super.dispose();
 }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        bottom: TabBar(
          controller: _tabController,
          tabs: myTabs,
        ),
      ),
      body: TabBarView(
        controller: _tabController,
        children: myTabs.map((Tab tab) {
          final String label = tab.text.toLowerCase();
          return Center(
            child: Text(
              'This is the $label tab',
              style: const TextStyle(fontSize: 36),
            ),
          );
        }).toList(),
      ),
    );
  }
}

````


## TabController
协调在TabBar和TabBarView之间选择

index属性是指 选中tab的index， animation代表着tab bar和tab bar view的当前滑动位置。 选中的tab的index可以用animateTo改变。

创建TabBar或TabBarView的stateful widget可以创建一个TabController 并且直接共享它。

当TabBar 和TabBarView没有一个方便stateful组件， 一个TabController 可以共享通过提供DefaultTabController继承组件

### 构造函数
````
TabController({
int initialIndex: 0,
@required int length,
@required TickerProvider vsync
})

````

### 属性
- animation → Animation<double>
An animation whose value represents the current position of the TabBar's selected tab indicator as well as the scrollOffsets of the TabBar and TabBarView. [...]
read-only
- index ↔ int
The index of the currently selected tab. [...]
read / write
- indexIsChanging → bool
True while we're animating from previousIndex to index as a consequence of calling animateTo. [...]
read-only
- length → int
The total number of tabs. [...]
final
- offset ↔ double
The difference between the animation's value and index. [...]
read / write
- previousIndex → int
The index of the previously selected tab. [...]
read-only
- hashCode → int
The hash code for this object. [...]
read-only, inherited
- hasListeners → bool
Whether any listeners are currently registered. [...]
@protected, read-only, inherited
- runtimeType → Type
A representation of the runtime type of the object.
read-only, inherited

### 方法
- animateTo(int value, { Duration duration: kTabScrollDuration, Curve curve: Curves.ease }) → void
- dispose() → void
- addListener(VoidCallback listener) → void
- noSuchMethod(Invocation invocation) → dynamic
- notifyListeners() → void
- removeListener(VoidCallback listener) → void
- toString() → String


## SingleTickerProviderStateMixin<T extends StatefulWidget> mixin
提供单个Ticker, 配置为仅在启用当前树时tick, 如TickerMode所定义。

在仅使用单个AnimationController的State中创建AnimationController， mix in  this class，然后传递```vsync:this  ``` 给 animation controller 的构造函数。

这个mixin仅仅只支持出售单个ticker。如果你有多个AnimationController对象在state的生命周期中， 使用一个完整的TickerProviderStateMixin 代替。

SingleTickerProviderStateMixin 实现TickerProvider, 被DrawerControllerState、 ImplicitlyAnimatedWidgetState实现。

例子：
````
import 'package:flutter/material.dart';

void main()=>runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme:ThemeData(
        primarySwatch: Colors.blue,
      ),
      home:KeepAliveDemo()
    );
  }
} 

class KeepAliveDemo extends StatefulWidget {
  _KeepAliveDemoState createState() => _KeepAliveDemoState();
}
/*
with是dart的关键字，意思是混入的意思，
就是说可以将一个或者多个类的功能添加到自己的类无需继承这些类，
避免多重继承导致的问题。
SingleTickerProviderStateMixin 主要是我们初始化TabController时，
需要用到vsync ，垂直属性，然后传递this
*/
class _KeepAliveDemoState extends State<KeepAliveDemo> with SingleTickerProviderStateMixin {
  TabController _controller;

  @override
  void initState(){
    super.initState();
    _controller = TabController(length:3, vsync: this);
  }

  //重写被释放的方法，只释放TabController
  @override
  void dispose(){
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar:AppBar(
        title:Text('Keep Alive Demo'),
        bottom:TabBar(
          controller: _controller,
          tabs:[
            Tab(icon:Icon(Icons.directions_car)),
            Tab(icon:Icon(Icons.directions_transit)),
            Tab(icon:Icon(Icons.directions_bike)),
          ],
        )
      ),
      body:TabBarView(
        controller: _controller,
        children: <Widget>[
         Text('1111'),
         Text('2222'),
         Text('3333')
        ],
      )
    );
  }
}

````


## AutomaticKeepAliveClientMixin<T extends StatefulWidget> mixin
为AutomaticKeepAlive的客户提供方便的方法，与state子类一起使用， 子类必须实现wantKeepAlive， 并且他们的构建方法必须调用super.build(返回值将始终返回null, 并且应该被忽略)。

然后， 每当wantKeepAlive的值改变（或可能改变时）， 子类应该调用updateKeepAlive.

type参数T是混合此类的State的StatefulWidget子类的类型

also see:
- AutomaticKeepAlive 用于侦听来自此mixin的消息
- KeepAliveNotification 这个mixin发送的通知

### 构造函数
AutomaticKeepAliveClientMixin()

### 属性
- wantKeepAlive  返回bool 表示当前实例是否应保持活动状态
- context 返回BuildContext  构建这个小部件在树中的位置
- hashCode  返回int 此对象的哈希值
- mounted → bool  表示这个state object是否在树中  只读
- runtimeType → Type A representation of the runtime type of the object.
- widget → T The current configuration. [...]

## AutomaticKeepAlive

允许子树请求在惰性列表中保持活动状态。

此小部件类似于KeepAlive, 但它不是显式配置，而是侦听来自子级和其他后台的KeepAliveNotification消息。
只要有一个或多个已发送KeepAliveNotification并且尚未触发其KeepAliveNotification.handle的后代，子树就会保持活动状态。

要发送这些通知，请考虑使用AutomaticKeepAliveClientMixin.

### 构造函数
AutomaticKeepAlive({Key key, Widget child})
创建一个侦听KeepAliveNotification的小部件， 并适当维护KeepAlive小部件。


## KeepAliveNotification
表示通知气泡必须保持活动的子树，即使它通常会作为优化被丢弃。

每个KeepAliveNotification都配置了一个句柄，该句柄由一个Hideable组成， 当一个子树不再需要保持活动状态时，该句柄会被触发。

在发送组件从树中被移除（在State.deactive）的任何时候，该手柄应该会被触发。 然后如果重建窗口小部件并且仍然需要保持活动状态， 它应该在构建期间立即发送新通知（可能具有相同的监听）。

AutomativKeepAlive小部件会监听此通知， 该小部件有SliverList(和ListView) 和SliverGrid 和GridView小部件自动添加到树中。

无法以上述方式触发句柄可能会导致AutomaticKeepAlive无法跟踪窗口小部件是否应保持活动状态， 从而导致内存泄漏或数据丢失。 例如，如果请求keep-alive的窗口小部件从子树中删除但在出来时不出发其Listenable, 则子树将继续保持活动状态， 直到列表本身被释放。类似的， 如果在窗口小部件需要保持活动状态时触发了Listenable, 但是没有立即发送新的KeepAliveNotification, 则窗口小部件可能会在希望保持活动状态时进行垃圾回收。






## 保持状态页面的例子
````
import 'package:flutter/material.dart';
class MyHomePage extends StatefulWidget {

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

//混入AutomaticKeepAliveClientMixin，这是保持状态的关键
//然后重写wantKeppAlive 的值为true。
class _MyHomePageState extends State<MyHomePage> with AutomaticKeepAliveClientMixin {
  int _counter = 0;
  //重写keepAlive 为true ，就是可以有记忆功能了。
  @override
  bool get wantKeepAlive => true;
  //声明一个内部方法，用来点击按钮后增加数量
  void _incrementCounter(){
    setState((){ _counter++;});
  }

  @override
  void initState(){
    print('**************initState');
    super.initState();
  }

  @override
  void deactivate() {
    // TODO: implement deactivate
    print('**************deactivate');
    super.deactivate();
  }

  @override
  void updateKeepAlive() {
    // TODO: implement updateKeepAlive
    print('**************updateKeepAlive');
    super.updateKeepAlive();
  }


  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body:Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text('点一下加1，点一下加1:'),
            Text(
              '$_counter',
              style:Theme.of(context).textTheme.display1,
            )
          ],
        ),
      ),
      //增加一个悬浮按钮，点击时触犯_incrementCounter方法
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }
}

/**
 * 保存页面状态 demo
 * 
 */
class KeepAliveDemo extends StatefulWidget {
  _KeepAliveDemoState createState() => _KeepAliveDemoState();
}
/*
with是dart的关键字，意思是混入的意思，
就是说可以将一个或者多个类的功能添加到自己的类无需继承这些类，
避免多重继承导致的问题。
SingleTickerProviderStateMixin 主要是我们初始化TabController时，
需要用到vsync ，垂直属性，然后传递this
*/
class _KeepAliveDemoState extends State<KeepAliveDemo> with SingleTickerProviderStateMixin {
  TabController _controller;

  @override
  void initState(){
    super.initState();
    _controller = TabController(length:3, vsync: this);
  }

  //重写被释放的方法，只释放TabController
  @override
  void dispose(){
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar:AppBar(
        title:Text('Keep Alive Demo'),
        bottom:TabBar(
          controller: _controller,
          tabs:[
            Tab(icon:Icon(Icons.directions_car)),
            Tab(icon:Icon(Icons.directions_transit)),
            Tab(icon:Icon(Icons.directions_bike)),
          ],
        )
      ),
      body:TabBarView(
        controller: _controller,
        children: <Widget>[
         MyHomePage(),
         MyHomePage(),
         MyHomePage()
        ],
      )
    );
  }
}

void main() => runApp(new MyApp());
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter bottomNavigationBar',
      theme: ThemeData(
        primarySwatch: Colors.lightBlue,
      ),
      home: KeepAliveDemo(),     
    );
  }
}

````