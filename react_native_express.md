[React Native Express](http://www.reactnativeexpress.com/environment)

有两种通用的方式来设置React Native 的开发环境：create-react-native-app和 react-native-cli

当我们需要在app中自定义native modules时，通常用react-native-cli来创建我们的app.


### Component API
1. this.props
2. this.state
3. this.setState(object,newState)

### Lifecycle API
#### Mounting Cycle

(1). 构造方法：constructor(object props)

组件类被实例化。构造函数的参数是元素的初始化props, 由父元素指定。你可以通过将对象赋值给this.state 来指定元素的初始状态。 此时，没有native ui 被rendered for this element。

(2). 组件将挂载：componentWillMount()

在第一次rendering之前此方法只触发一次。此时，仍然没有native ui 被rendered fro this element.

(3). 渲染方法： render() -> React Element

该方法要么返回一个React Element 去渲染，要么返回null,要么不做渲染。

(4). 组件挂载：componentDidMount()

在第一次rendering之后此方法只触发一次。此时跟元素相关的native UI 也已经完成渲染，并且可以通过this.refs直接操作。如果你需要调用异步API或者执行延迟代码使用setTimeout,通常应该在该方法中进行实现操作。



#### Updating Cycle

(1). componentWillReceiveProps(object nextProps)

父组件已经传入了新的props。当前组件将会被重新渲染。你可能选择的调用this.setState()去更新组件的内部状态在render方法被调用之前。	

(2). shouldComponentUpdate(object nextProps, object nextState) -> boolean

基于props和state的next values,  
组件也许会去重新渲染或者不去重新渲染。这个方法的基类实现总是返回true(组件应该重新渲染)。为了优化， 覆盖这个方法并且检查是props还是state已经被改变，例如对这些对象中的键/值进行相等性测试，如果返回false将会阻止render方法会被调用

(3). componentWillUpdate(object nextProps, object nextState)

在确定组件将重新渲染之后，这个方法将被触发。你可能不会在这个方法中调用this.setState(),因为已经有一个update操作处于进程中了。	

(4). render() -> React Element

假设shouldComponentUpdate方法返回true,  那么这个方法将会被调用。这个方法会返回一个要渲染的react element或者null或者不做任何渲染。

(5). componentDidUpdate(object prevProps, object prevState)

在re-rendering发生之后这个方法会被触发。此时，当前组件的原生UI已经被更新了，反映在通过render()方法所返回的react element上面。
	