## [react官方文档](https://reactjs.org/)

 react相关基础： JSX, components, state 和 props。

### JSX使用

JSX[相关文档地址](http://www.css88.com/react/docs/jsx-in-depth.html)

React使用JSX来替换常规的JavaScript。JSX的优点：
执行更快，因为它在编译为JavaScript代码后进行了优化；类型安全，在编译
过程中就能发现错误，使用JSX编写模板更加简单快捷。

可用作：
1. 变量声明

```const element= = <h1>Hello,world!</h1>```

2. 嵌入式表达
<pre>
	<code>
	const name = 'Josh Perez';
	const element = <h1>Hello, {name}<\/h1>;
	ReactDOM.render(
	  element,
	  document.getElementById('root')
	);
	</code>
</pre>
	

或者如下
<pre>
	<code>
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
	</code>
</pre>

3. 也作为一种expression
即我们可以在if条件和for循环中，将jsx表达式作为一种变量，或者参数 ，或者作为函数的返回值
<pre>
	<code>
	function getGreeting(user) {
	  if (user) {
	    return <h1>Hello, {formatName(user)}!</h1>;
	  }
	  return <h1>Hello, Stranger.</h1>;
	}
	</code>
</pre>

4. 指定Attributes值

如： ```const element = <div tabIndex="0"></div>;```

或者 ```const element = <img src={user.avatarUrl}></img>;```

5. 指定Children

如果一个tag是空的，你可以用“/>”关闭它，如：```const element = <img src={user.avatarUrl} />;```
JSX的tags可以包含Children ，如：
<pre>
	<code>
		const element = (
			  <div>
			    <h1>Hello!</h1>
			    <h2>Good to see you here.</h2>
			  </div>
			);		
	</code>
</pre>


6. JSX阻止注入攻击

在JSX中嵌入user input 是安全的。
如：
<pre>
	<code>
		const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
    </code>
</pre>

默认情况下， React DOM 在渲染jsx之前会释放掉任何嵌入在JSX中的值。因此，它确保你
不会注入未在应用程序中显示写入的任何内容。在渲染之前，所有的东西都被转为字符串了，
这有助于阻止XSS（跨站点脚本攻击）。

7. 表示对象

方式一：
<pre>
	<code>
		const element = (
		  <h1 className="greeting">
		    Hello, world!
		  </h1>
		);
	</code>
</pre>


方式二：
<pre>
	<code>
	const element = React.createElement(
	  'h1',
	  {className: 'greeting'},
	  'Hello, world!'
	);	
	</code>
</pre>


方式一和方式二是相同的。
方式二的本质等同于：
<pre>
	<code>
		const element = {
		  type: 'h1',
		  props: {
		    className: 'greeting',
		    children: 'Hello, world!'
		  }
		}; 
	</code>
</pre>

