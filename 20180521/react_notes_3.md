[官方文档](https://reactjs.org/docs/lists-and-keys.html) 

### Lists and Keys

+ Rendering Multiple Components
你可以构建元素集合并使用大括号{}把它们包含在JSX中。

下面，我们可以使用JavaScript的map方法来循环numbers数组。代码示例：
````
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);

ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);

````

+ Basic List Component
通常我们在组件中渲染lists。  
我们可以重构上述示例代码输出一个无序的elements list。
````
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);

````

### Booleans, Null, and Undefined Are Ignored

false,null,undefined,and true 是有效的children。他们仅仅不渲染。
下面这些JSX表达式将显示相同的结果：
````
<div />

<div></div>

<div>{false}</div>

<div>{null}</div>

<div>{undefined}</div>

<div>{true}</div>

````


### Typechecking with PropTypes

prop-types模块
用于检查传入的props。

PropTypes最初作为React核心模块的一部分被暴露出来，并且通常与React 组件一起使用。
使用PropTypes参考代码：
````
import  PropTypes from 'prop-types';
MyComponent.propTypes ={
  // You can declare that a prop is a specific JS type. By default, these
  // are all optional.
optionalArray:PropTypes.array,
optionalBool:PropTypes.bool,
optionalFunc:PropTypes.func,
optionalNumber:PropTypes.number,
optionalObject:PropTypes.object,
optionalString:PropTypes.string,
optionalSymbol:PropTypes.symbol,

  // Anything that can be rendered: numbers, strings, elements or an array
  // (or fragment) containing these types.
  optionalNode:PropTypes.node,

  // A React element
  optionalElement:PropTypes.element,

  //You can also declare that a prop is an instance of a class. This uses
  //JS's instanceof operator
  optionalMessage:PropTypes.instanceOf(Message),

  // You can ensure that your prop is limited to specific values by treating
  // it as an enum
  optionalEnum:PropTypes.oneOf(['News','Photos']),

  // An object that could be of many types
  optionalUnion:PropTypes.oneOfType([
      PropTypes.string,
      PropTypes.number,
      PropTypes.instanceOf(message)
  	]),

  // An array of a certain type
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // An object with property values of a certain type
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),

  // An object taking on a particular shape
  optionalObjectWithShape: PropTypes.shape({
     color:PropTypes.string,
     fontSize:PropTypes.number
  	}),


// You can chain any of the above with 'isRequired' to make sure a warning
// is shown if the prop isn't provided
  requiredFunc: PropType.func.isRequired,


  // A value of any data type
  requireAny: PropType.any.isRequired,


  //You can also specify a custom validator. It should return an Error
  // object if the validation fail. Don't 'console.warn' or throw, as this
  // won't work inside 'oneOfType'
  customProp:function(props,propName,componentName){
  	if(!/matchme/.test(props[propName])){
      return new Error('Invalid Prop `' + propName + '` supplied to '+
      '`'+ componentName + '`.Validation failed.'  
      );
  	}
  }


  // You can also supply a custom validator to `arrayOf` and `objectOf`.
  // It should return an Error object if the validation fails. The validator
  // will be called for each key in the array or object. The first two
  // arguments of the validator are the array or object itself, and the
  // current item's key.
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })


}


````


在组件中更实用PropTypes示例：
````
import PropTypes from 'prop-types';
class MyComponent extends React.Component{
	render(){
		const children = this.props.children;
        return (
          <div>
             {children}
          </div>
        );
	}
}

MyComponent.propTypes = {
	children:PropTypes.element.isRequired
};

````


+ Default Prop Values
你可以定义传入props的默认值，通过设置defaultProps属性：

示例代码1：
````
class Greeting extends React.Component{
	render(){
		return (
            <h1>Hello, {this.props.name}</h1>
			);
	}

	//设置props的默认值
    Greeting.defaultProps = {
    	name:'Stranger',
    };

    //Render "Hello ,Stranger":
    ReactDOM.render(
<Greeting />, document.getElementById('example')
    	);

}


````

示例代码2：

````
class Greeting extends React.Component{
   static defaultProps = {
   	name:'stranger'
   };

   render(){
   	return (
         <div>Hello , {this.props.name}</div>
   		);
   }
}

````

### Static Type Checking
静态类型检查器像Flow 和TypeScript 识别某些类型的问题在运行代码之前。
他们也可以通过添加诸如auto-completion的特性来提高开发工作流程。因此，我们推荐使用
Flow 或者TypeScript 来代替更大的代码库PropTypes。

#### Flow

Flow是JavaScript代码的静态类型检查器。
使用Flow, 你需要：
1.  添加Flow到你的project中 作为 dependency依赖

首先安装最新版本的flow-bin：

```yarn add --dev flow-bin ```
或```npm install --save-dev flow-bin```

然后在package.json中添加代码：

````
{
	"scripts"：{
		"flow":"flow"
	}
}

````

然后运行一下命令:

```yarn run flow init ```
该命令会创建一个flow的配置文件(需要commit的)



2.  确保Flow语法是从编译的代码中被剥离的

首先安装bebel-preset-flow最新版本：
```yarn add --dev babel-preset-flow ```

然后配置babel,即在.babelrc文件中添加代码:
````
{
  "presets":[
     "flow",
     "react"
  ]
}
````


3.  添加类型注释然后运行Flow去检查

运行命令：```yarn flow```


更多内容参考[flow的官方文档](https://flow.org/)



#### TypeScript

TypeScript是一种变成语言，它是JavaScript的超集。

使用TypeScript,步骤：
1. Add Typescript as a dependency to your project

首先安装typescript:
```yarn add --dev typescript```

然后在package.json中添加代码：
````
{
	"scripts":{
		"tscbuild":"tsc"
	}
}

````

2. Configure the TypeScript compiler options

首先运行命令：```tsc -- init```
然后在tsconfig.json修改代码：
````
{
  "compilerOptions":{
  	"rootDir":"src",
  	"outDir":"build"
  }

}

````

3. Use the right file extensions

在React中，你可能使用后缀名为js的文件来写你的组件代码，在TypeScript中，我们有2种文件后缀名：.ts和.tsx。
（.ts是默认的文件后缀名，.tsx是包含jsx语法的文件的后缀名）

4. Add definitions for libraries you use

首先安装react的typescript definition:```yarn add --dev @types/react ```


创建React app时使用Using TypeScript,运行命令：
```create-react-app my-app --scripts-version=react-scripts-ts```


### Refs and the DOM

Refs提供了一种访问渲染方法中创建的DOM节点或React元素的方法。

#### 使用Refs的情况
这有一些好的使用案例情况：
- Managing focus, text selection, or media playback
- Triggering imperative animations
- Integrating with third-party DOM libraries

#### 不要过度使用Refs

#### Creating Refs

Refs 是使用React.createRef()来创建的 并且 通过ref attribute附属到React元素上。

示例代码：
````
class MyComponent extends React.Component{
	constructor(props){
		super(props);
		this.myRef = React.createRef();
	}
	render(){
		return <div ref={this.myRef}/>;
	}
}

````

#### 访问Refs
 当在渲染时，a ref被传给了元素，那么这个节点将变的可访问通过 ref的当前属性值。
```const node = this.myRef.current; ```
上述代码的值依赖于node的类型不同而不同：
- 如果ref attribute 使用在html元素上，  这个ref 创建在构造函数中并使用React.createRef()
来进行的，它会收到这个相关的DOM elements 来作为当前的属性。

- 当ref 属性被使用在自定义的class component 上时， 这个ref 对象是接收组件的mounted instance 来作为它当前的节点。

- 你可能不会在函数组件上使用ref attribute, 因为他们没有实例

##### 给DOM Element添加ref
示例代码：
````
class CustomTextInput extends React.Component{
    constructor(props){
        super(props);
        this.textInput = React.createRef();
        this.focusTextInput = this.focusTextInput.bind(this);
    }

    focusTextInput(){
        this.textInput.current.focus();
    }

    render(){
        return (
            <div>
                <input type="text" ref={this.textInput}/>
                <input type="button"
                       value="Focus the text input"
                       onClick={this.focusTextInput}/>
            </div>
        );
    }

}

````

##### 给Class Component添加ref

示例代码：
````
import React from 'react';
class CustomTextInput extends React.Component{
    constructor(props){
        super(props);
        this.textInput = React.createRef();
        this.focusTextInput = this.focusTextInput.bind(this);
    }

    focusTextInput(){
        this.textInput.current.focus();
    }

    render(){
        return (
            <div>
                <input type="text" ref={this.textInput}/>
                <input type="button"
                       value="Focus the text input"
                       onClick={this.focusTextInput}/>
            </div>
        );
    }

}

export default class AutoFocusTextInput extends React.Component{
    constructor(props){
        super(props);
        this.textInput = React.createRef();
    }

    componentDidMount(){
        this.textInput.current.focusTextInput();
    }

    render(){
        return (
            <CustomTextInput ref={this.textInput} />
        );
    }
}

````

*注意：只有CustomTextInput被定义为class组件时，上述功能才有效

