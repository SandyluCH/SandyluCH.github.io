## [MaterialApp](https://api.flutter.dev/flutter/material/MaterialApp-class.html)
应用使用material设计。

MaterialApp包含了一些组件（material design应用中所需要的）。他是在WidgetApp的基础上，通过添加一些material-design 特定的功能， 例如AnimatedTheme and GridPaper。

MaterialApp配置了顶级的导航栏按照下面的顺序去搜索路由：
- 1、对于/路由，如果不为空，则使用home 属性
- 2、否则， 如果有route入口，则使用routes
- 3、否则，如果onGenerateRoute被提供， 那么它将被调用。对于没有被home和routes处理的任何有效路由，它应该返回非空值。
- 4、最后，如果所有其他的都是失败了， onUnknownRoute将被调用

如果创建了导航器，则这些选项中的至少一个必须处理该/路由， 因为在启动时指定了无效的initialRoute时使用该路径。

MaterialApp还配置了顶级导航栏的观察者，以便于运行 Hero动画。

如果home, routes, onGenerateRoute 和 onUnknownRoute都为null, builder不为空， 那么将没有导航栏创建。


MaterialApp构造函数：
````
const MaterialApp({
Key key,
GlobalKey<NavigatorState> navigatorKey,
Widget home,
Map<String, WidgetBuilder> routes: const {},
String initialRoute,
RouteFactory onGenerateRoute,
RouteFactory onUnknownRoute,
List<NavigatorObserver> navigatorObservers: const [],
TransitionBuilder builder,
String title: '',
GenerateAppTitle onGenerateTitle,
Color color,
ThemeData theme,
ThemeData darkTheme,
Locale locale,
Iterable<LocalizationsDelegate> localizationsDelegates,
LocaleListResolutionCallback localeListResolutionCallback,
LocaleResolutionCallback localeResolutionCallback,
Iterable<Locale> supportedLocales: const [Locale('en', 'US')],
bool debugShowMaterialGrid: false,
bool showPerformanceOverlay: false,
bool checkerboardRasterCacheImages: false,
bool checkerboardOffscreenLayers: false,
bool showSemanticsDebugger: false,
bool debugShowCheckedModeBanner: true
})

````


