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
- shouldComponentUpdate()  **(不常用)**
- render()
- getSnapshotBeforeUpdate() **(不常用)**
- componentDidUpdate()

* 在代码中要避免使用的方法：UNSAFE_componentWillUpdate()和 UNSAFE_componentWillReceiveProps()


#### 卸载Unmounting
当组件将被从dom中移除的时候， 以下方法会被调用：
- componentWillUnmount()

#### 错误处理Error Handling
渲染期间， 在生命周期方法中或者 任何子组件的constructor中 发生错误的时候， 以下方法将会被调用：
- static getDerivedStateFromError() **(不常用)**
- componentDidCatch() **(不常用)**


### 其他

#### 其他的api
每个组件也提供一些其他的api:
- setState()
- forceUpdate()

#### Class Properties
- defaultProps
- displayName

#### Instance Properties
- props
- state