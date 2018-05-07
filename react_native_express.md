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
