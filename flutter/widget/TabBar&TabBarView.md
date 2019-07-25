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

