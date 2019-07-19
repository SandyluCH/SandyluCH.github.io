## SearchDelegate<T>类
委托showSearch定义搜索页面的内容。
搜索页面始终在顶部AppBar显示， 用户可以在其中输入搜索查询。搜索查询文本字段之前和之后显示的按钮可以通过SearchDelegate.leading和自定义Searchdelegate.actions.

AppBar下方的主体可以显示建议的查询（由SearchDelegate.buildSuggestions返回）， 或者一旦用户提交搜索结果，由SearchDelegate.buildResults返回。

SearchDelegate.query始终包含用户输入的当前查询，应该用于构建建议和结果。 结果可以在屏幕上通过调用带来SearchDelegate.showResults, 您可以通过拨打回去显示建议SearchDeletegate.showSuggestions。

一旦用户选择了搜索结果， 就应该调用SearchDelegate.close从导航堆栈的顶部删除搜索页面，并通知showSearch的调用者有关所选搜索结果的信息。给定的SearchDelegate只能与一个活动的showSearch调用相关联。在为另一个showSearch调用重新使用相同的委托实例之前调用SearchDelegate.close。

### 构造函数
SearchDelegate()

### 属性
- query   在AppBar中显示的当前查询的字符串   可读写
- transitionAnimation   当搜索页面出现或消失时的动画  可读写
- hashCode
- runtimeType   A representation of the runtime typeof the object.

### 方法
- appBarTheme(BuildContext context) → ThemeData   appBar样式的主题
- buildActions(BuildContext context) → List<Widget>
在AppBar中， 搜索查询之后展示的小部件
- buildLeading(BuildContext context) → Widget
在AppBar中， 当前查询之前展示的小部件
- buildResults(BuildContext context) → Widget
用户提交从搜索页面的search之后展示的结果
- buildSuggestions(BuildContext context) → Widget
用户在搜索区域输入查询的同时，在搜索页面的body中展示的搜索建议
- close(BuildContext context, T result) → void
管理搜索页面返回潜在的路由
- showResults(BuildContext context) → void
展示结果
Transition from the suggestions returned by buildSuggestions to the query results returned by buildResults. [...]
- showSuggestions(BuildContext context) → void
展示建议
Transition from showing the results returned by buildResults to showing the suggestions returned by buildSuggestions. [...]
- noSuchMethod(Invocation invocation) → dynamic
Invoked when a non-existent method or property is accessed. [...]
inherited
- toString() → String
Returns a string representation of this object.
inherited



