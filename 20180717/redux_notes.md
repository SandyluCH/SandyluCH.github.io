## [redux](http://www.redux.org.cn/)

### API

#### createStore(reducer, [preloadedState], enhancer)
创建一个Redux store来以存放应用中的所有的state.
应用中应有且仅有一个store

参数
- reducer (Function): 接收两个参数，分别是当前的 state 树和要处理的 action，返回新的 state 树。

- [preloadedState] (any): 初始时的 state。 在同构应用中，你可以决定是否把服务端传来的 state 水合（hydrate）后传给它，或者从之前保存的用户会话中恢复一个传给它。如果你使用 combineReducers 创建 reducer，它必须是一个普通对象，与传入的 keys 保持同样的结构。否则，你可以自由传入任何 reducer 可理解的内容。

- enhancer (Function): Store enhancer 是一个组合 store creator 的高阶函数，返回一个新的强化过的 store creator。这与 middleware 相似，它也允许你通过复合函数改变 store 接口。

返回值
(Store): 保存了应用所有 state 的对象。改变 state 的惟一方法是 dispatch action。你也可以 subscribe 监听 state 的变化，然后更新 UI。

示例：
````

import { createStore } from 'redux'

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return state.concat([action.text])
    default:
      return state
  }
}

let store = createStore(todos, ['Use Redux'])

store.dispatch({
  type: 'ADD_TODO',
  text: 'Read the docs'
})

console.log(store.getState())
// [ 'Use Redux', 'Read the docs' ]


````

小贴士
- 应用中不要创建多个 store！相反，使用 combineReducers 来把多个 reducer 创建成一个根 reducer。

- 你可以决定 state 的格式。你可以使用普通对象或者 Immutable 这类的实现。如果你不知道如何做，刚开始可以使用普通对象。

- 如果 state 是普通对象，永远不要修改它！比如，reducer 里不要使用 Object.assign(state, newData)，应该使用 Object.assign({}, state, newData)。这样才不会覆盖旧的 state。如果可以的话，也可以使用 对象拓展操作符（object spread spread operator 特性中的 return { ...state, ...newData }。

- 对于服务端运行的同构应用，为每一个请求创建一个 store 实例，以此让 store 相隔离。dispatch 一系列请求数据的 action 到 store 实例上，等待请求完成后再在服务端渲染应用。

- 当 store 创建后，Redux 会 dispatch 一个 action 到 reducer 上，来用初始的 state 来填充 store。你不需要处理这个 action。但要记住，如果第一个参数也就是传入的 state 是 undefined 的话，reducer 应该返回初始的 state 值。

- 要使用多个 store 增强器的时候，你可能需要使用 compose



## [react-redux](http://www.redux.org.cn/docs/react-redux/api.html)

### <Provider store>
使组件层级中的 connect() 方法都能够获得 Redux store。正常情况下，你的根组件应该嵌套在 <Provider> 中才能使用 connect() 方法。

如果你真的不想把根组件嵌套在 <Provider> 中，你可以把 store 作为 props 传递到每一个被 connect() 包装的组件，但是我们只推荐您在单元测试中对 store 进行伪造 (stub) 或者在非完全基于 React 的代码中才这样做。正常情况下，你应该使用 <Provider>。

属性
- store (Redux Store): 应用程序中唯一的 Redux store 对象
- children (ReactElement) 组件层级的根组件。

例子1：
````

ReactDOM.render(
  <Provider store={store}>
    <MyRootComponent />
  </Provider>,
  rootEl
)

````


例子2：
````

ReactDOM.render(
  <Provider store={store}>
    <Router history={history}>
      <Route path="/" component={App}>
        <Route path="foo" component={Foo}/>
        <Route path="bar" component={Bar}/>
      </Route>
    </Router>
  </Provider>,
  document.getElementById('root')
)

````


### connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])
连接 React 组件与 Redux store。

连接操作不会改变原来的组件类。
反而返回一个新的已与 Redux store 连接的组件类。
参数
- [mapStateToProps(state, [ownProps]): stateProps] (Function): 如果定义该参数，组件将会监听 Redux store 的变化。任何时候，只要 Redux store 发生改变，mapStateToProps 函数就会被调用。该回调函数必须返回一个纯对象，这个对象会与组件的 props 合并。如果你省略了这个参数，你的组件将不会监听 Redux store。如果指定了该回调函数中的第二个参数 ownProps，则该参数的值为传递到组件的 props，而且只要组件接收到新的 props，mapStateToProps 也会被调用（例如，当 props 接收到来自父组件一个小小的改动，那么你所使用的 ownProps 参数，mapStateToProps 都会被重新计算）。

注意：在高级章节中，你需要更好地去控制渲染的性能，所用到的 mapStateToProps() 会返回一个函数。在这种情况下，那个函数将被作为 mapStateToProps() 在独有的组件实例中调用。这样就允许你在每一个实例中去记录。你可以参考 #279 去测试和了解其中的详细内容。但在绝大多数的应用中不会用到。

- [mapDispatchToProps(dispatch, [ownProps]): dispatchProps] (Object or Function): 如果传递的是一个对象，那么每个定义在该对象的函数都将被当作 Redux action creator，对象所定义的方法名将作为属性名；每个方法将返回一个新的函数，函数中dispatch方法会将action creator的返回值作为参数执行。这些属性会被合并到组件的 props 中。

如果传递的是一个函数，该函数将接收一个 dispatch 函数，然后由你来决定如何返回一个对象，这个对象通过 dispatch 函数与 action creator 以某种方式绑定在一起（提示：你也许会用到 Redux 的辅助函数 bindActionCreators()）。如果你省略这个 mapDispatchToProps 参数，默认情况下，dispatch 会注入到你的组件 props 中。如果指定了该回调函数中第二个参数 ownProps，该参数的值为传递到组件的 props，而且只要组件接收到新 props，mapDispatchToProps 也会被调用。

注意：在高级章节中，你需要更好地去控制渲染的性能，所用到的 mapStateToProps() 会返回一个函数。但在这个例子中，这个函数将被 mapStateToProps() 在独有的组件实例中调用。这样就允许你在每一个实例中去记录。你可以参考 #279 去测试和了解其中的详细内容。但在绝大多数的应用中不会用到。

- [mergeProps(stateProps, dispatchProps, ownProps): props] (Function): 如果指定了这个参数，mapStateToProps() 与 mapDispatchToProps() 的执行结果和组件自身的 props 将传入到这个回调函数中。该回调函数返回的对象将作为 props 传递到被包装的组件中。你也许可以用这个回调函数，根据组件的 props 来筛选部分的 state 数据，或者把 props 中的某个特定变量与 action creator 绑定在一起。如果你省略这个参数，默认情况下返回 Object.assign({}, ownProps, stateProps, dispatchProps) 的结果。

- [options] (Object) 如果指定这个参数，可以定制 connector 的行为。

[pure = true] (Boolean): 如果为 true，connector 将执行 shouldComponentUpdate 并且浅对比 mergeProps 的结果，避免不必要的更新，前提是当前组件是一个“纯”组件，它不依赖于任何的输入或 state 而只依赖于 props 和 Redux store 的 state。默认值为 true。
[withRef = false] (Boolean): 如果为 true，connector 会保存一个对被包装组件实例的引用，该引用通过 getWrappedInstance() 方法获得。默认值为 false。


待续......