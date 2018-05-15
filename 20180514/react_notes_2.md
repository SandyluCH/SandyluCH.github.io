[react 官方文档地址](https://reactjs.org/docs/state-and-lifecycle.html)

### State and Lifecycle

+ Converting a Function to a Class

clock组件代码：
````
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

````

将一个functional组件转为 a class 只需要5步，以Clock组件为例：
(1). 创建一个相同名的ES6类， 继承与React.Component
(2). 添加render()方法
(3). 改变render()方法的body
(4). 在render()方法中 用this.props替换 props
(5). 删掉遗留的空函数声明

转换之后class的代码是：

````
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

````

+ Adding Local State to a Class

Clock组件的代码变为：
````

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

````

使用Clock组件：
````
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);

````

+ Adding Lifecycle Methods to a Class

组件的生命周期：
constructor(object props) ————> componentWillMount()————>render()——————> componentDidMount()
加入生命周期之后的示例代码如下：
````
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);


````

+ Using State Correctly
这里有三条关于setState()的规则：
(1). 不能直接更改state的值

```this.state.comment = 'hello'```这种写法是错误的，
正确的写法是：```this.setState({comment:'hello'})```

(2). State Updates May Be Asynchronous(state更新可能是异步的)

由于this.props和this.state可能异步更新，你不应该依赖于他们的值来计算下一个state

错误代码示例：
````
this.setState({
	counter:this.state.counter + this.props.increment
	});

````

纠正方式是 传入一个函数而不是一个object 给setState()作为参数。这个函数将会接收这个previous state作为第一个
参数，这个 props 作为第二个参数。

正确代码示例：
````
this.setState(function(prevState, props){
	return {
      counter: prevState.counter + props.increment
	};
 });

````

(3). State Updates are Merged

当你调用setState方法时， React会将所提供的对象合并为当前sate

例如：您的state 可能包含了几个独立的变量：
````
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }

````

然后你可以分别调用setState()方法，独自更新他们，代码示例如下：
````
componentDidMount(){
    fetchPosts().then(response =>{
      this.setState({
      	 posts:response.posts
      	});  	
  	})

  	fetchComments().then(response =>{
  		this.setState({
  		 comments:response.comments
  		});
  	});
}

````

+ The Data Flows Down

无论是父组件还是子组件都不知道当前组件是否是有状态的还是无状态的，并且他们不应该关心它是被定义为function
还是class。

这就是为什么state通常被称为局部的或者封装的。任何组件无法访问到它，除非拥有并设置它的组件。

### Handling Events

React 元素上的Handling events与Dom元素上的handling events 类似，但是也有一些区别：

  -  React events 通常采用驼峰式命名，而不是lowercase
	
  -  使用JSX语法，你传入的一个函数作为event handler，而不是一个字符串。
