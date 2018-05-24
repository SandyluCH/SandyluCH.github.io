## [react官方文档](https://reactjs.org/)

 react相关基础： JSX, components, state 和 props。

### JSX使用

JSX[相关文档地址](http://www.css88.com/react/docs/jsx-in-depth.html)

React使用JSX来替换常规的JavaScript。JSX的优点：
执行更快，因为它在编译为JavaScript代码后进行了优化；类型安全，在编译
过程中就能发现错误，使用JSX编写模板更加简单快捷。

#### 作用
1. 变量声明

```const element= = <h1>Hello,world!</h1>```

2. 嵌入式表达
````
	const name = 'Josh Perez';
	const element = <h1>Hello, {name}</h1>;
	ReactDOM.render(
	  element,
	  document.getElementById('root')
	);
````
	

或者如下
````
	function formatName(user) {
	  return user.firstName + ' ' + user.lastName;
	}
	const user = {
	  firstName: 'Harper',
	  lastName: 'Perez'
	};
	const element = (
	  <h1>
	    Hello, {formatName(user)}!
	  </h1>
	);
	ReactDOM.render(
	  element,
	  document.getElementById('root')
	);
````

3. 也作为一种expression
即我们可以在if条件和for循环中，将jsx表达式作为一种变量，或者参数 ，或者作为函数的返回值
````
	function getGreeting(user) {
	  if (user) {
	    return <h1>Hello, {formatName(user)}!</h1>;
	  }
	  return <h1>Hello, Stranger.</h1>;
	}
````

4. 指定Attributes值

如： ```const element = <div tabIndex="0"></div>;```

或者 ```const element = <img src={user.avatarUrl}></img>;```

5. 指定Children

如果一个tag是空的，你可以用“/>”关闭它，如：```const element = <img src={user.avatarUrl} />;```
JSX的tags可以包含Children ，如：
````
		const element = (
			  <div>
			    <h1>Hello!</h1>
			    <h2>Good to see you here.</h2>
			  </div>
			);		
````


6. JSX阻止注入攻击

在JSX中嵌入user input 是安全的。
如：
````
		const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
````

默认情况下， React DOM 在渲染jsx之前会释放掉任何嵌入在JSX中的值。因此，它确保你
不会注入未在应用程序中显示写入的任何内容。在渲染之前，所有的东西都被转为字符串了，
这有助于阻止XSS（跨站点脚本攻击）。

7. 表示对象

方式一：
````
		const element = (
		  <h1 className="greeting">
		    Hello, world!
		  </h1>
		);
````


方式二：
````
	const element = React.createElement(
	  'h1',
	  {className: 'greeting'},
	  'Hello, world!'
	);	
````


方式一和方式二是相同的。
方式二的本质等同于：
````
		const element = {
		  type: 'h1',
		  props: {
		    className: 'greeting',
		    children: 'Hello, world!'
		  }
		}; 
````

#### Rendering Elements

+ Rendering an Element into the DOM
在html文件中有代码```<div id="root"></div>```
我们把这个id=root的节点叫做root节点，因为在这个节点下的everything 都通过React DOM节点来管理。

用React创建的applications的应用通常只有一个单root dom节点。如果您正在集成React到现有的app中，那么
你可能有尽可能多的隔离根DOM节点。

示例代码：

````
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));

````

+ Updating the Rendered Element

React 元素是不变的，稳定的。一旦你创建了一个element,你不能改变它的children和属性。
一个元素就像在电影中的一帧，它代表了在当前时间和节点的的UI。

目前，更新UI的方法是创建一个new element,并把它传给ReactDOM.render()。

示例代码如下：

````

function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));
}

setInterval(tick, 1000);

````

#### Components and Props
组件从概念上看就像是函数，它可以接受任意的输入值(称之为“props”),并返回一个需要在页面上展示的React元素。


+ Functional and Class Components
1. 定义一个组件最简单的方式是写一个Javascript函数：
````
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

````

这个函数是一个有效的React 组件，因为它接收了一个props(这代表了properties) 对象参数带有data,
并且返回一个React 元素。  我们把这种组件叫做 “ functional ”,因为它是合法的javascript 函数。

2. 您也可以使用ES6 class来定义一个组件
代码示例：
````

class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

````

+ Rendering a Component

示例代码：
````
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);

````

+ Composing Components
组件可以引用输出中的其他组件。这让我们可以在任何级别的细节中使用相同的组件抽象。一个按钮、
一个表单、一个对话框、一个屏幕，在反应式应用程序中，所有这些都通常表示为组件。

示例代码：
````

function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);

````

代码中，我们创建了一个App组件，这个组件render了Welcome组件很多次。

+ Extracting Components

不要害怕将组件拆分成更小的组件

示例代码：
````
 function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}

````

拆分出组件Avatar:

````
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />

  );
}

````

继续拆分出组件UserInfo
````

function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}

````

拆分之后简化的组件结果如下：

````

function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}

````

+ Props are Read-Only

无论您是用function还是class来申请一个组件，它都不能改变它自己的props,示例代码：

````
function sum(a, b) {
  return a + b;
}

````

这种functions 被叫做“pure”,因为它们没有试图改变它们的inputs,并且在相同的inputs时总是
返回相同的结果

相反，下面的function就因为它改变了它自己的input值，所以是impurede 

````

function withdraw(account, amount) {
  account.total -= amount;
}

````

* react虽然灵活但是它有一条严格的规定：所有的React components must act like pure functions with respect
to their props.



