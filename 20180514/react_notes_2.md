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
- (1). 创建一个相同名的ES6类， 继承与React.Component
- (2). 添加render()方法
- (3). 改变render()方法的body
- (4). 在render()方法中 用this.props替换 props
- (5). 删掉遗留的空函数声明

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
-- React events 通常采用驼峰式命名，而不是lowercase
-- 使用JSX语法，你传入的一个函数作为event handler，而不是一个字符串。

例如：在Html中代码如下：
````
<button onclick="activateLasers()">
  Activate Lasers
</button>

````

在React中代码如下：
````
<button onClick={activateLasers}>
  Activate Lasers
</button>

````


在html中阻止默认事件，代码如下：
````
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>

````

而在React中阻止默认事件，代码如下：
````
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}

````


当我们使用ES6 class定义一个组件,示例代码：
````
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);

````

* 注意：必须小心JSX 回调中this的意义， class methods 并不是默认绑定在this上的。如果
你忘记绑定this.handlerClick （即未使用代码```this.handleClick = this.handleClick.bind(this);```）并将其传入onClick,  ‘this’对象将会是undefined当这个方法真正
被调用的时候。这不是React的特殊性，这个是“how functions work in JavaScript”中的一部分。通常，如果你
引用一个方法并且在方法后面没有带有'()',例如：```onClick={this.handlerClick}```,你应该绑定这个方法
（使用代码```this.handleClick = this.handleClick.bind(this);```进行绑定）


如果对于调用bind困到到你，这有两个方式可以绕过这个‘this’。

方式一、代码示例：

````
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}

````

方式二代码示例：
````
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}

````

+ Passing Arguments to Event Handlers
示例代码：
````
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>

````


### Conditional Rendering

在React, 你可能创建不同的组件来封装你需要的行为。然后，你可以根据应用的状态，只渲染其中一部分。

React中的条件渲染与JavaScript中的条件渲染是一致的。使用Javascript操作像if 或者 条件操作符to创建
elements代表当前状态，然后让React 更新UI去跟他们相配。

条件渲染示例如下：
````

function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}

function Greeting(props){
  const isLoggedIn = props.isLoggedIn;
  if(isLoggedIn){
     return <UserGreeting />;
  }
  return <GuestGreeting />;
}

  ReactDOM.render(
   <Greetting isLoggedIn = {false} />,
   document.getElementById('root')
  );

````

+ Element Variables
可以使用变量来存放elements。这可以帮助你有条件地呈现组件的一部分，而其余的输出不会改变。


代码示例：
````
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}

function Greeting(props){
  const isLoggedIn = props.isLoggedIn;
  if(isLoggedIn){
     return <UserGreeting />;
  }
  return <GuestGreeting />;
}

function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}

class LoginControl extends React.Component{
  constructor(props){
     super(props);
     this.handleLoginClick = this.handleLoginClick.bind(this);
     this.handleLogoutClick = this.handleLogoutClick.bind(this);
     this.state = { isLoggedIn:false };
  }

  handleLoginClick(){
    this.setState({isLoggedIn:true});
  }

  handleLogoutClick(){
    this.setState({isLoggedIn:false});
  }

  render(){
    const isLoggedIn = this.state.isLoggedIn;

    const button = isLoggedIn?(
<logoutButton  onClick = {this.handleLogoutClick} />
      ):(
<loginButton onClick = {this.handleLoginClick} />
        );

        return (
            <div>
                <Greeting isLoggedIn = {isLoggedIn} />
                {button}
            </div>
        );

  }

}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);


````

+ Inline If with Logical && Operator

您可以将任何表达式嵌入到JSX中，将他们用大括号括起来。这包括JavaScript的逻辑和运算符。
示例代码:
````
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);

````

+ Inline If-Else with Conditional Operator
JavaScript条件操作符： condition ? true: false
代码示例1：
````
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}

````

代码示例2：
````
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}

````

+ Preventing Component from Rendering

极少数情况下，你可能想要一个隐藏组件，尽管它被其他组件渲染，办法是返回null而不是将这个组件输出。

代码示例：

````
function WarningBanner(props){
  if(!props.warn){
    return null;
  }
  return (
     <div className = "warning">
       Warning!
     </div>
    );
}

class Page extends React.Component{
  constructor(props){
    super(props);
    this.state = {showWarning:true};
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick(){
    this.setState(preState => ({
       showWarning:!prevState.showWarning
      });//箭头函数返回值为对象{}用括号（）包起来以示跟函数{}的区别
  }

  render(){
    return (
      <div>
          <WarningBanner warn={this.state.showWarning} />
          <button onClick={this.handleToggleClick}>
             {this.state.showWarning ? 'Hide':'Show'}
          </button>
      </div>
      );
  }

}


````

上述代码中组件<WarningBanner />的渲染取决于warn的值，如果值为false, 那么这个组件将不会渲染



