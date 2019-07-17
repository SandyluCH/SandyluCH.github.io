## Navigator
页面路径通常由Navigator小部件管理。Navigator管理一堆Route对象，并提供管理堆栈的方法。
到用户界面符合堆栈时， 用户应该能够navigate 回堆栈中的早期元素， 使用路径和导航器是合适的。在某些平台上，系统UI将提供一个后退按钮， 允许用户导航回应用程序堆栈中的早期路径。在没有此内置导航机制的平台上，使用AppBar(通常在Scaffold.appBar属性中使用)可以自动为用户导航添加后退按钮。

可以直接创建导航器， 单通常是使用有WidgetsApp或MaterialApp小部件创建的导航器， 可以使用Navigator.of引用该导航器。
MaterialApp的Home成为在底部的路线导航的堆栈。
要在堆栈上推送新路径，您可以使用构建器函数创建MaterialPageRoute实例，例如：
````
Navigator.push(context, MaterialPageRoute<void>{
  builder:(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('My Page')),
      body: Center(
        child: FlatButton(
          child: Text('POP'),
          onPressed: (){
            Navigator.pop(context);
          }
        )
      )

    );
  }
});


````
通常没必要提供一个窗口小部件，在这个小部件中弹出导航器， 因为脚手架会自动向其AppBar添加"后退"按钮。按后退按钮可以用调用Navigator.pop。 在Android中，按下系统的后退按钮也可以达到同样的效果。

### 使用命名的导航器路由
MaterialApp可以用创建Map<String, WidgetBuilder>从一个路径名映射到一个生成器功能，从而创建导航器路由。该导航器路由值为 onGenerateRoute回调。
````
void main() {
  runApp(MaterialApp(
    home: MyAppHome(), // becomes the route named '/'
    routes: <String, WidgetBuilder> {
      '/a': (BuildContext context) => MyPage(title: 'page A'),
      '/b': (BuildContext context) => MyPage(title: 'page B'),
      '/c': (BuildContext context) => MyPage(title: 'page C'),
    },
  ));
}

````
要按名称显示路线：
````
Navigator.pushNamed(context, '/b');

````

### 路由可以返回一个值
当推送路由以询问用户值时，可以通过pop方法的result参数返回该值。
例如：
````
bool value = await Navigator.push(context, MaterialPageRoute<bool>(
  builder: (BuildContext context) {
    return Center(
      child: GestureDetector(
        child: Text('OK'),
        onTap: () { Navigator.pop(context, true); }
      ),
    );
  }
));

````
如果用户按下'ok', 则value为true。如果用户退出路线，例如按下脚手架的后退按钮，则该值将为空。
当路由用于返回值时，路由的类型参数必须与pop的结果类型匹配。 即上述例子中使用 MaterialPageRoute<bool> 而不是 MaterialPageRoute<void>, 或者只使用MaterialPageRoute也可以。

### Popup routes
路线不必整个屏幕。 PopupRoute用ModalRoute,barrierColor覆盖屏幕，ModalRoute.barrierColor只能部分不透明以允许当前屏幕显示。 弹出路由是“Modal” 。

这有一些创建和展示popup routes的方法。例如： showDialog, showMenu 和 showModalBottomSheet.

这也有一些创建和展示popup routes的widget, 例如：PopupMenuButton 和 DropdownButton.

## Custom routes
你可以创建自己的一个窗口小部件库路由类（如PopupRoute, ModalRoute 或 PageRoute）的子类，以控制用于显示路径的动画过渡， 路径模态障碍的颜色和行为以及路径的其他方面。

该PageRouteBuilder类能够在回调中来定义一个定制的路线。这个一个在路线出现或消失时旋转并淡化其子节点的实例。

例如：
````
Navigator.push(context, PageRouteBuilder(
  opaque: false,
  pageBuilder: (BuildContext context, _, __) {
    return Center(child: Text('My PageRoute'));
  },
  transitionsBuilder: (___, Animation<double> animation, ____, Widget child) {
    return FadeTransition(
      opacity: animation,
      child: RotationTransition(
        turns: Tween<double>(begin: 0.5, end: 1.0).animate(animation),
        child: child,
      ),
    );
  }
));

````
该PageRouteBuilder类能够在回调中来定义一个定制的路线。这是一个在路线出现或消失时旋转并淡化其子节点的示例。 此路由不会遮挡整个屏幕，因为他指定了opaque:false, 就像弹出路由一样。

页面路径由两部分构成，“页面”和“过渡”。该页面成为传递给该transitionsBuilder函数的子代的后代。通常情况下，页面仅内置一次，因为它不依赖于它的动画参数（省略的_ ，并__在这个例子中）。过渡是建立在每个帧的持续时间。

### 嵌套导航器
一个应用程序可以使用多个导航器。将一个导航器嵌套在另一个导航器下方可用于创建"inner journey"， 例如选项卡式导航，用户注册，商店结账或代表整个应用程序子部分的其他独立journey.



### 构造函数
````
const Navigator({
Key key,
String initialRoute,
@required RouteFactory onGenerateRoute,
RouteFactory onUnknownRoute,
List<NavigatorObserver> observers: const []
})

````

### 方法
- createState() → NavigatorState
Creates the mutable state for this widget at a given location in the tree. [...]
override
- createElement() → StatefulElement
Creates a StatefulElement to manage this widget's location in the tree. [...]
inherited
- debugDescribeChildren() → List<DiagnosticsNode>
Returns a list of DiagnosticsNode objects describing this node's children. [...]
@protected, inherited
- debugFillProperties(DiagnosticPropertiesBuilder properties) → void
Add additional properties associated with the node. [...]
inherited
- noSuchMethod(Invocation invocation) → dynamic
Invoked when a non-existent method or property is accessed. [...]
inherited
- toDiagnosticsNode({String name, DiagnosticsTreeStyle style }) → DiagnosticsNode
Returns a debug representation of the object that is used by debugging tools and by DiagnosticsNode.toStringDeep. [...]
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

### 静态方法
- canPop(BuildContext context) → bool
Whether the navigator that most tightly encloses the given context can be popped. [...]
- maybePop<T extends Object>(BuildContext context, [ T result ]) → Future<bool>
Returns the value of the current route's Route.willPop method for the navigator that most tightly encloses the given context. [...]
@optionalTypeArgs
- of(BuildContext context, { bool rootNavigator: false, bool nullOk: false }) → NavigatorState
The state from the closest instance of this class that encloses the given context. [...]
- pop<T extends Object>(BuildContext context, [ T result ]) → bool
Pop the top-most route off the navigator that most tightly encloses the given context. [...]
@optionalTypeArgs
- popAndPushNamed<T extends Object, TO extends Object>(BuildContext context, String routeName, { TO result, Object arguments }) → Future<T>
Pop the current route off the navigator that most tightly encloses the given context and push a named route in its place. [...]
@optionalTypeArgs
- popUntil(BuildContext context, RoutePredicate predicate) → void
Calls pop repeatedly on the navigator that most tightly encloses the given context until the predicate returns true. [...]
- push<T extends Object>(BuildContext context, Route<T> route) → Future<T>
Push the given route onto the navigator that most tightly encloses the given context. [...]
@optionalTypeArgs
- pushAndRemoveUntil<T extends Object>(BuildContext context, Route<T> newRoute, RoutePredicate predicate) → Future<T>
Push the given route onto the navigator that most tightly encloses the given context, and then remove all the previous routes until the predicate returns true. [...]
@optionalTypeArgs
- pushNamed<T extends Object>(BuildContext context, String routeName, { Object arguments }) → Future<T>
Push a named route onto the navigator that most tightly encloses the given context. [...]
@optionalTypeArgs
- pushNamedAndRemoveUntil<T extends Object>(BuildContext context, String newRouteName, RoutePredicate predicate, { Object arguments }) → Future<T>
Push the route with the given name onto the navigator that most tightly encloses the given context, and then remove all the previous routes until the predicate returns true. [...]
@optionalTypeArgs
- pushReplacement<T extends Object, TO extends Object>(BuildContext context, Route<T> newRoute, { TO result }) → Future<T>
Replace the current route of the navigator that most tightly encloses the given context by pushing the given route and then disposing the previous route once the new route has finished animating in. [...]
@optionalTypeArgs
- pushReplacementNamed<T extends Object, TO extends Object>(BuildContext context, String routeName, { TO result, Object arguments }) → Future<T>
Replace the current route of the navigator that most tightly encloses the given context by pushing the route named routeName and then disposing the previous route once the new route has finished animating in. [...]
@optionalTypeArgs
- removeRoute(BuildContext context, Route route) → void
Immediately remove route from the navigator that most tightly encloses the given context, and Route.dispose it. [...]
- removeRouteBelow(BuildContext context, Route anchorRoute) → void
Immediately remove a route from the navigator that most tightly encloses the given context, and Route.dispose it. The route to be replaced is the one below the given anchorRoute. [...]
- replace<T extends Object>(BuildContext context, { Route oldRoute, Route<T> newRoute }) → void
Replaces a route on the navigator that most tightly encloses the given context with a new route. [...]
@optionalTypeArgs
- replaceRouteBelow<T extends Object>(BuildContext context, { Route anchorRoute, Route<T> newRoute }) → void
Replaces a route on the navigator that most tightly encloses the given context with a new route. The route to be replaced is the one below the given anchorRoute. [...]
@optionalTypeArgs
