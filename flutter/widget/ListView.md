## ListView

线性摆放可滚动的列表组件。

ListView是最通用的滑动组件。在滑动方向，一个接一个展示它的孩子，在cross axis, 它的孩子被要求充满这个ListView。

如果为非null, 则itemExtend强制子项在滚动方向上具有给定范围。指定itemExtend 比 让孩子自己确定自己的范围更有效， 因为滚动机制可以利用孩子保存工作范围的预知。

### 构造方法
构建ListView有4中方式：
- 1、默认构造函数采用子类显式的List<Widget>。 这个方式适合有少量孩子的ListView.
````
ListView({Key key, Axis scrollDirection: Axis.vertical, bool reverse: false, ScrollController controller, bool primary, ScrollPhysics physics, bool shrinkWrap: false, EdgeInsetsGeometry padding, double itemExtent, bool addAutomaticKeepAlives: true, bool addRepaintBoundaries: true, bool addSemanticIndexes: true, double cacheExtent, List<Widget> children: const [], int semanticChildCount, DragStartBehavior dragStartBehavior: DragStartBehavior.start })

````
````
ListView(
  padding: const EdgeInsets.all(8.0),
  children: <Widget>[
    Container(
      height: 50,
      color: Colors.amber[600],
      child: const Center(child: Text('Entry A')),
    ),
    Container(
      height: 50,
      color: Colors.amber[500],
      child: const Center(child: Text('Entry B')),
    ),
    Container(
      height: 50,
      color: Colors.amber[100],
      child: const Center(child: Text('Entry C')),
    ),
  ],
)
````
- 2、ListView.builder 采用IndexedWigetBuilder, 按需build 孩子。 这个方法适合有大量或者infinite 孩子的ListView, 因为builder只在孩子可见时才调用。
````
ListView.builder({Key key, Axis scrollDirection: Axis.vertical, bool reverse: false, ScrollController controller, bool primary, ScrollPhysics physics, bool shrinkWrap: false, EdgeInsetsGeometry padding, double itemExtent, @required IndexedWidgetBuilder itemBuilder, int itemCount, bool addAutomaticKeepAlives: true, bool addRepaintBoundaries: true, bool addSemanticIndexes: true, double cacheExtent, int semanticChildCount, DragStartBehavior dragStartBehavior: DragStartBehavior.start })
````
````
final List<String> entries = <String>['A', 'B', 'C'];
final List<int> colorCodes = <int>[600, 500, 100];

ListView.builder(
  padding: const EdgeInsets.all(8.0),
  itemCount: entries.length,
  itemBuilder: (BuildContext context, int index) {
    return Container(
      height: 50,
      color: Colors.amber[colorCodes[index]],
      child: Center(child: Text('Entry ${entries[index]}')),
    );
  }
);

````
- 3、ListView.separated 采用两个IndexedWidgetBuilders:itemBuilder ，按需build 孩子。separatorBuilder 同样build separtor children (在两个子项目之间)。这个方式使用与有固定数量child的List
````
ListView.separated({Key key, Axis scrollDirection: Axis.vertical, bool reverse: false, ScrollController controller, bool primary, ScrollPhysics physics, bool shrinkWrap: false, EdgeInsetsGeometry padding, @required IndexedWidgetBuilder itemBuilder, @required IndexedWidgetBuilder separatorBuilder, @required int itemCount, bool addAutomaticKeepAlives: true, bool addRepaintBoundaries: true, bool addSemanticIndexes: true, double cacheExtent })

````
````
final List<String> entries = <String>['A', 'B', 'C'];
final List<int> colorCodes = <int>[600, 500, 100];

ListView.separated(
  padding: const EdgeInsets.all(8.0),
  itemCount: entries.length,
  itemBuilder: (BuildContext context, int index) {
    return Container(
      height: 50,
      color: Colors.amber[colorCodes[index]],
      child: Center(child: Text('Entry ${entries[index]}')),
    );
  },
  separatorBuilder: (BuildContext context, int index) => const Divider(),
);

````
- 4、ListView.custom 采用SliverChildDelegate, 提供了自定义子项其他方面的能力。 例如： SliverChildDelegate可以控制评估不可见子项的大小的算法。

为了控制scroll view的initial scroll offset, 提供了带有scroll view 的ScrollController.initialScrollOffset属性集合的控制器。

默认情况下， ListView将自动填充列表的可滚动末端，以避免MediaQuery填充只是的部分障碍。 要避免此行为，请使用零填充属性覆盖。
````
ListView.custom({Key key, Axis scrollDirection: Axis.vertical, bool reverse: false, ScrollController controller, bool primary, ScrollPhysics physics, bool shrinkWrap: false, EdgeInsetsGeometry padding, double itemExtent, @required SliverChildDelegate childrenDelegate, double cacheExtent, int semanticChildCount })

````

### 子项目的生命周期
- Creation 创建
- Destruction  当子项目滚出视图是， 相关元素的子树、state 以及 render objects都会被销毁。当scrolled back时，在列表相同位置的新的子项目将会和新elements、states and render object一起被lazily recreated 。 
- Destruction mitigation  为了保存滚出和滚入的子项目的状态， 以下选择是可能的：
  + 将非平凡的UI状态驱动业务逻辑的所有权移出列表子子树。 例如， 如果列表包含来自缓存网络相应的帖子数量的帖子，则将帖子列表和upvote号码存储在列表外的数据模型中。让列表子UI子树可以从真实源模型对象轻松地重新创建。在子窗口小部件子树中使用StatefulWidget仅存储瞬时UI状态。
  + 让KeepAlive成为需要保留的列表子窗口小部件子树的跟窗口小部件。在KeepAlive部件标志着孩子子树的顶部渲染对象的孩子保持有效。当关联的顶部渲染对象滚动到视图外时，该列表将子渲染对象（以及扩展名，其关联的元素和状态）保留在缓存列表中，而不是销毁套门。当滚动回视图时， 渲染对象按原样重新绘制（如果在过渡期间未标记为dirty）。 这仅在if addAutomaticKeepAlives和addRepaintBoundaries false时有效， 因为这些参数会导致ListView将每个子窗口小部件子树与其他窗口小部件包装在一起。
  + 使用AutomaticKeepAlive小部件（默认情况下插入addAutomaticKeepAlives为true）。 当像KeepAlive一样在屏幕外滚动时， 而不是无条件地缓存子元素子树， AutomaticKeepAlive 可以让是否缓存子树由子树的后台逻辑决定。
  例如， EditableText小部件表示其列表子元素子树保持活动状态，而其文本字段具有输入焦点。如果它没有焦点，并且没有其他后台通过KeepAliveNotification发出保持活动的信号，则滚动离开时列表子元素子树将被销毁。
  AutomaticKeepAlive后台通常使用AutomaticKeepAliveClientMixin发信号通知它，然后实现wantKeepAlive 的getter和调用updateKeepAlive.

### 转换CustomScrollView
一个ListView控件基本上是一个CustomScrollView与单一SliverList在其CustomScrollView.slivers财产。

如果ListView不再足够，例如因为滚动视图既有列表又有网格， 或者因为列表要与SliverAppBar等组合在一起， 所以直接将代码从使用ListView移植到使用CustomScrollView。

ListView上的键、scrollDirection、reverse、controller、 primary、 physics 和shrinkWrap属性直接映射到CustomScrollView上具有相同名称的属性。


CustomScrollView.slivers属性应该是包含任一列表SliverList或SliverFixedExtendList; 前者如果ListView上的itemExtent为null, 后者如果itemExtent不为null。

ListView上的childrenDelegate属性对应于SliverList.delegate(或SliverFixedExtentList.delegate)属性。在新的ListView构造函数的参数对应于childrenDelegate是一个SliverChildListDelegate, 与上述说法相同.该新ListView.builder构造函数和变量对应childrenDelegate是一个SliverChildBuilderDelegate与匹配参数，

该padding属性对应于具有SliverPadding在CustomScrollView.slivers属性，而不是列表本身，有SliverList, 而不是成为SliverPadding的孩子。

CustomScrollView不会像ListView那样自动避免来自MediaQuery的障碍。要重现该行为，用SliverSafeAreas包裹slivers。

一旦代码被移植到CustomScrollView中， 其他slivers，例如SliverGrid或SliverAppBar, 可以放在CustomScrollView.slivers 的列表中.
例子：
移植前
````
ListView(
  shrinkWrap: true,
  padding: const EdgeInsets.all(20.0),
  children: <Widget>[
    const Text('I\'m dedicating every day to you'),
    const Text('Domestic life was never quite my style'),
    const Text('When you smile, you knock me out, I fall apart'),
    const Text('And I thought I was so smart'),
  ],
)

````
移植后：
````
CustomScrollView(
  shrinkWrap: true,
  slivers: <Widget>[
    SliverPadding(
      padding: const EdgeInsets.all(20.0),
      sliver: SliverList(
        delegate: SliverChildListDelegate(
          <Widget>[
            const Text('I\'m dedicating every day to you'),
            const Text('Domestic life was never quite my style'),
            const Text('When you smile, you knock me out, I fall apart'),
            const Text('And I thought I was so smart'),
          ],
        ),
      ),
    ),
  ],
)

````


### 属性
- childrenDelegate  返回sliverChildDelegate    为ListView提供孩子的代理
- itemExtent   如果不为空， 强制子项目在滑动的方向有给定的extent.
- anchor → double
The relative position of the zero scroll offset. [...]
final, inherited
- cacheExtent → double
The viewport has an area before and after the visible area to cache items that are about to become visible when the user scrolls. [...]
final, inherited
- center → Key
The first child in the GrowthDirection.forward growth direction. [...]
final, inherited
- controller → ScrollController
An object that can be used to control the position to which this scroll view is scrolled. [...]
final, inherited
- dragStartBehavior → DragStartBehavior
Determines the way that drag start behavior is handled. [...]
final, inherited
- hashCode → int
The hash code for this object. [...]
read-only, inherited
- key → Key
Controls how one widget replaces another widget in the tree. [...]
final, inherited
- padding → EdgeInsetsGeometry
The amount of space by which to inset the children.
final, inherited
- physics → ScrollPhysics
How the scroll view should respond to user input. [...]
final, inherited
- primary → bool
Whether this is the primary scroll view associated with the parent PrimaryScrollController. [...]
final, inherited
- reverse → bool
Whether the scroll view scrolls in the reading direction. [...]
final, inherited
- runtimeType → Type
A representation of the runtime type of the object.
read-only, inherited
- scrollDirection → Axis
The axis along which the scroll view scrolls. [...]
final, inherited
- semanticChildCount → int
The number of children that will contribute semantic information. [...]
final, inherited
- shrinkWrap → bool
Whether the extent of the scroll view in the scrollDirection should be determined by the contents being viewed. [...]
final, inherited

### 方法
- buildChildLayout(BuildContext context)  返回widget  子类应该覆盖这个方法去build the layout model
- debugFillProperties(DiagnosticPropertiesBuilder properties) 返回void  添加与节点相关的额外属性
- build(BuildContext context)  返回widget  描述此窗口小部件表示的用户界面部分。
- buildSlivers(BuildContext context) 返回List<Widget> 构建要放置在是口内的窗口小部件列表。
- buildViewport(BuildContext context, ViewportOffset offset, AxisDirection axisDirection, List<Widget> slivers) → Widget
 构建视图
@protected, inherited
- createElement() → StatelessElement
创建StatelessElement以管理此窗口小部件在树中的位置
inherited
- debugDescribeChildren() → List<DiagnosticsNode>
返回描述此节点的子节点的DiagnosticsNode对象列表
@protected, inherited
- getDirection(BuildContext context) → AxisDirection
返回滚动视图滚动的AxisDirection
@protected, inherited
- noSuchMethod(Invocation invocation) → dynamic
访问不存在的方法或属性时调用
inherited
- toDiagnosticsNode({String name, DiagnosticsTreeStyle style }) → DiagnosticsNode
返回调试工具和DiagnosticsNode.toStringDeep使用的对象调试表示形式。
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
inherited



## CustomScrollView
使用slivers创建自定义滚动效果的scrollview。

一个CustomScrollview允许您提供sliver直接创建不同的滚动效果， 如列表、网格、扩大头。例如， 要创建包含展开的应用栏，后跟列表和网络的滚动视图， 请使用三个sliver的列表：SliverAppBar, SliverList和SliverGrid。 这些sliver中的小部件必须生成RenderSliver对象。

若要控制滚动视图的初始滚动偏移，请为控制器提供器ScrollController.initialScrollOffset属性集。
例子：
````
CustomScrollView(
  slivers: <Widget>[
    const SliverAppBar(
      pinned: true,
      expandedHeight: 250.0,
      flexibleSpace: FlexibleSpaceBar(
        title: Text('Demo'),
      ),
    ),
    SliverGrid(
      gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
        maxCrossAxisExtent: 200.0,
        mainAxisSpacing: 10.0,
        crossAxisSpacing: 10.0,
        childAspectRatio: 4.0,
      ),
      delegate: SliverChildBuilderDelegate(
        (BuildContext context, int index) {
          return Container(
            alignment: Alignment.center,
            color: Colors.teal[100 * (index % 9)],
            child: Text('grid item $index'),
          );
        },
        childCount: 20,
      ),
    ),
    SliverFixedExtentList(
      itemExtent: 50.0,
      delegate: SliverChildBuilderDelegate(
        (BuildContext context, int index) {
          return Container(
            alignment: Alignment.center,
            color: Colors.lightBlue[100 * (index % 9)],
            child: Text('list item $index'),
          );
        },
      ),
    ),
  ],
)

````

### Accessibility
一个CustomScrollView 可以允许Talk/VoiceOver 去发布通告用户在滚动状态的变化。例如： 在Android上，公告可能被解读为“显示23的项目1到10”。 要生成此公告，滚动视图需要三条信息：
- 第一个可见的子索引；
- child总数；
- 可见child的总数；
最后一个值可以由框架精确计算，但必须提供前两个。大多数更高级别的可滚动窗口小部件自动提供此信息。例如， ListView自动为每个子窗口小部件提供语义索引，并将语义子基数设置为列表的长度。

为了确定可见索引，滚动视图需要一种方法来将每个可滚动项的生成语义和语义索引相关联。这可以通过将子窗口小部件包装在IndexedSemantics中来完成。

该语义索引不一定与可滚动窗口中的窗口小部件的索引相同，因为一些窗口小部件可能不提供语义信息。考虑一个新的ListView.separated（）：每个其他小部件都是一个没有语义信息的分隔符。在这种情况下，只有奇数编号的小部件具有语义索引（等于索引〜/ 2）。此外，此示例中的子节点总数将是小部件数量的一半。（新的ListView.separated（）构造函数自动处理;这仅作为示例使用。）

可见子元素的总数可以由构造函数参数semanticChildCount提供，这始终与IndexedSemantics中包含的小部件数相同。

### 构造方法
````
CustomScrollView({Key key, Axis scrollDirection: Axis.vertical, bool reverse: false, ScrollController controller, bool primary, ScrollPhysics physics, bool shrinkWrap: false, Key center, double anchor: 0.0, double cacheExtent, List<Widget> slivers: const [], int semanticChildCount, DragStartBehavior dragStartBehavior: DragStartBehavior.start })

````

### 属性
- slivers -> List<Widget>  

- anchor 返回double

- cacheExtent 返回double

- center 返回key

- controller 返回ScrollController

- dragStartBehavior 返回DragStartBehavior

- hashCode 返回int

- key 返回Key

- physics 返回ScrollPhysics

- primary 返回 bool

- reverse 返回bool

- runtimeType  返回Type

- scrollDirection 返回Axis

- semanticChildCount 返回int

- shrinkWrap 返回 bool




