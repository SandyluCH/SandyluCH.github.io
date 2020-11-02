## React.component

### 关于component的生命周期

#### 挂在Mounting
常用方法有：
- constructor()
    主要用途： 初始化本地state, 在组件实例上面绑定event handler方法
    * （1）在该方法中不应该调用setState方法， 应该直接给state赋值 ```this.state = { counter:0 } ```
    * （2）在该方法中避免引入有任何副作用或者订阅
    * （3）避免直接将props 复制到state中， 如````
        constructor(props) {
            super(props);
            // Don't do this!
            this.state = { color: props.color };
            // 正确的做法是
            this.state = { color: this.props.color }
        }
    ````
- static getDerivedStateFromProps()  *该方法不常用*
   在初始mount和后来的updating 的render方法之前， getDerivedStateFromProps方法被触发。该方法应该返回一个对象去更新state， 或者返回null 不更新。
   在该方法中不能获取组件实例。 如果你想要的话， 可以在getDerivedStateFromProps()和其他类方法之间重用一些代码， 方法是在类定义之外提取组件props和state的纯函数。
   * 请注意， 无论原因是什么， 此方法都会在每个渲染器上激发。这与UNSAFE_componentWillReceiveProps形成对比， 后者只在父级导致重新渲染时触发， 而不是由本地setState而触发的。

- render()
    render方法是类组件中必需的方法。
    当该方法被调用的时候， 它将会检查 this.props和this.state, 然后返回以下类型中的一个：
    + React elements
    + Arrays and fragments
    + Portals
    + String and numbers
    + Booleans or null

    在该方法中应该是纯粹的， 意思是在这个方法中不能改变组件的state, 不能直接与浏览器交互

    如果你相与浏览器交互， 你应该在componentDidMount方法中或者其他生命周期中。

    * 注意： 如果shouldComponentUpdate方法返回false, render方法不会触发。
    
- componentDidmount()
  在组件被插入到树中之后 componentDidMount方法将会被立即触发。需要用到dom节点的初始化操作应该放在这里。 如果你需要从远端拉取数据， 那么在这里初始化网络请求数据是一个好的选择。
  
  在该方法中， 可以设置订阅， 不过别忘记在componentWillUnmount方法中取消订阅

  你可能在componentDidMount方法中直接调用setState方法，它将会触发额外的rendering， 但是它将会发生在浏览器更新screen之前。这保证了，即使render方法在这种情况下被调用的两次， 用户也不会看到中间态。请谨慎使用此模式， 因为它通常会导致性能问题。在大多数情况下， 你应该在constructor方法中初始化state。但是，对于modals和工具提示之类的情况，当您需要在呈现依赖于其大小或位置的DOM节点之前测量它时，它可能是必需的。
不常用方法有：

* 需要避免使用的方法： UNSAFE_componentWillMount()


#### 更新Updating
更新是由props或state的改变引起的， 当组件即将被re-rendered的时候， 这些方法将按照以下顺序调用：
- static getDerivedStateFromProps() **(不常用)**
  同上
- shouldComponentUpdate()  **(不常用)**
  使用shouldComponentUpdate() 让React知道组件的输出是否不受state或props的当前变化的影响。 默认的行为是每次state变化都要重新渲染。在绝大多数的情况下， 你应该依赖默认行为。
  当收到新的props或者state时，shouldComponentUpdate() 会在render之前被触发调用。默认为true. 对于初始render或者当使用forceUpdate()方法时， 该方法不会被触发。
  该方法仅作为性能优化而存在， 不要依赖它来阻止渲染， 因为这会导致错误。 考虑使用内置的PureComponent, 而不是手动编写shouldComponentUpdate(). PureComponent对props和state 进行简单比较， 减少了跳过必要更新的可能性。 

  如果你确信你想手写， 你可以比较一下this.props和nextProps, 以及this.state和nextState, 并返回false ， 去告诉React 这个更新可以跳过了。
  * 注意返回false并不能阻止子组件们因为他们的state改变而触发的重新渲染。
  我们建议在shouldComponentUpdate()方法中不要使用深度检查或者使用JSON.stringify(), 它效率很低， 而且会影响性能。

  当然， 如果shouldComponentUpdate方法返回false, 则不会调用UNSAFE_componentWillUpdate()、render() 和 ComponentDidUpdate方法。将来React可能会将shouldComponentUpdate()视为一个提示而不是一个严格的指令， 返回false仍可能导致组件的重新渲染。
  
- render()
  同上
- getSnapshotBeforeUpdate() **(不常用)**
  在最近渲染输出被提交到例如DOM之前， getSnapshotBeforeUpdate()方法被触发。这可以让你的组件从DOM(例如scroll position)上在可能变化之前捕捉信息。这个生命周期的任何返回值都会作为参数传给componentDidUpdate()。
  例如像在聊天线程中需要去特殊处理scroll position, 代码如下：
  ````
  class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // Are we adding new items to the list?
    // Capture the scroll position so we can adjust scroll later.
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // If we have a snapshot value, we've just added new items.
    // Adjust scroll so these new items don't push the old ones out of view.
    // (snapshot here is the value returned from getSnapshotBeforeUpdate)
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>{/* ...contents... */}</div>
    );
  }
}

  ````
  
- componentDidUpdate()
  componentDidUpdate方法在updating发生之后会被触发。该方法对于初始render不会被触发。

  在这个方法里面有机会去操作已经更新的组件中的DOM。 只要你比较当前的props与之前的props(如果props没有改变， 则可能不要网络请求)， 这也是进行网络请求的好地方。如：
  ````
componentDidUpdate(prevProps) {
  // Typical usage (don't forget to compare props):
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
  ````
  你可以在componentDidUpdate中立即调用setState(), 但请注意， 它必须包装在如上面示例中所示的条件中， 否则将导致无限循环， 这也会导致额外的重新渲染， 虽然用户看不到， 但会影响组件的性能。如果你试图mirror一些state 到来自上层的props, 考虑直接使用props.

  如果你的组件实现了getSnapshowBeforeUpdate()方法， 这个返回的值会作为第三个参数snapshot传递给componentDidUpdate(), 否则这个参数将会是undefined。



* 在代码中要避免使用的方法：UNSAFE_componentWillUpdate()和 UNSAFE_componentWillReceiveProps()


#### 卸载Unmounting
当组件将被从dom中移除的时候， 以下方法会被调用：
- componentWillUnmount()
该方法在组件被卸载和销毁前会马上触发， 在这个方法中执行任何有必要的清理。例如， 例如无效的定时器， 取消网络请求， 或者清理任何订阅。

你不应该在componentWillUnmount方法中调用setState(), 因为组件将永远不会被重新渲染， 一旦组件实例被卸载， 他将不会被再次挂载。


#### 错误处理Error Handling
渲染期间， 在生命周期方法中或者 任何子组件的constructor中 发生错误的时候， 以下方法将会被调用：
- static getDerivedStateFromError() **(不常用)**
- componentDidCatch() **(不常用)**


### 其他

#### 其他的api
每个组件也提供一些其他的api:
- setState()
setState()排队改变组件的state,并且告诉React， 这个组件和它的孩子需要用更新的state 重新渲染。这是用于更新用户界面以响应事件处理程序和服务器响应的主要方法。

将setState()看做是更新组件的请求， 而不是立即命令。为了更好地感知性能，React可能会延迟它， 然后在一次传递中更新多个组件。 React不保证立即应用状态更改。

setState()并不总是立即更新组件。它可以批处理或推迟更新。在调用setState()方法之后立即读取这个state, 这是一个潜在的隐患。相反， 使用componentDidUpdate或者使用setState回调函数（如setState(updater, callback)）, 它们中的任何一个都保证在应用更新后激发。

setState()将总是导致重新渲染 如果shouldComponentUpdate方法不返回false。 如果正在使用可变对象， 并且无法在shouldComponentUpdate()中实现呈现逻辑， 则只有在新状态与以前的状态下不同时才调用setSate()将避免不必要的重新渲染。

```` (state, props) => stateChange   ````

state是应用更改时组件状态的引用。 它不应该直接突变。相反， 更改应该通过基于state和props的输入构建一个新的对象来表示， 例如：
````
this.setState((state, props) => {
  return {counter: state.counter + props.step};
});
````
更换程序函数接收的state 和props都保证是最新的。更新程序的输出是简单地合并在一起。

setState()方法的第二个参数是一个可选的回调函数， 一旦setState完成并重新呈现组件， 该函数将执行。通常， 我们建议对此类逻辑使用componentDidUpdate().

你可以选择将对象作为第一个参数传递给setState(), 而不是函数, 如：
````
this.setState({quantity: 2})
````

这种形式的setState()也是异步的， 同一周期内的多个调用可以一起批处理，例如， 你试图在一个周期中多次增加一个item的数量， 这将导致等效于：
````
Object.assign(
  previousState,
  {quantity: state.quantity + 1},
  {quantity: state.quantity + 1},
  ...
)
````

后续调用将覆盖同一个周期中以前调用的值， 因此数量只增加一次。如果下一个状态取决于当前状态， 建议使用更新程序函数形式， 如：
````
this.setState((state) => {
  return {quantity: state.quantity + 1};
});
````









- forceUpdate()
  默认情况下， 当你组件的state或者props改变， 你组件将会重新渲染。如果你的render方法依赖于一些其他的数据， 你可以通过调用forceUpdate方法告诉React， 组件需要重新渲染。

  调用forceUpdate将会导致组件render（）被调用， 跳过shouldComponentUpdate()。 这将会触发子组件的正常的生命周期方法包括每个子组件的shouldComponentUpdate 。如果标记发生改变， React仍然只更新DOM。

  正常情况下， 你应该避免使用forceUpdate()方法， 仅仅从render方法中读取this.props 和this.state


#### Class Properties
- defaultProps
- displayName

#### Instance Properties
- props
- state