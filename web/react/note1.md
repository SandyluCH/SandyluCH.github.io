## React.component

### 关于component的生命周期

#### 挂在Mounting
常用方法有：
- constructor()
- render()
- componentDidmount()
不常用方法有：
- static getDerivedStateFromProps()
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