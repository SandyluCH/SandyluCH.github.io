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
