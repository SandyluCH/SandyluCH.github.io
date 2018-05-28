[官方文档](https://reactjs.org/docs/forwarding-refs.html)
[中文文档](https://doc.react-china.org/docs/error-boundaries.html)

## Forwarding Refs

Ref forwarding是一种将ref从组件传递给它的后代的技术。这个技术对高阶组件特别有用。

不使用React.forwardRef()的代码实例：
````
import React, { Component } from 'react';


class FancyButton extends React.Component{
    constructor(props){
        super(props);
        console.info('FancyButton constructor----');
        this.handleClick = this.handleClick.bind(this);
        this.curinputRef = React.createRef();
    }
    handleClick(){
        console.info('FancyButton handleClick----');
        this.curinputRef.current.focus();
    }
    render(){
        return (
            <div>
                <input type="text" ref={this.curinputRef}/>
                <button onClick={this.props.handleClick}>fancy button</button>
            </div>
        );
    }
}


function logProps(WrappedComponent){
    class LogProps extends React.Component{
        componentDidUpdate(prevProps){
            console.info('old props:', prevProps);
            console.info('new props:', this.props);
        }
        render(){
            console.info('logProps render---');
            console.info(this.props);
            return <WrappedComponent  {...this.props} />
        }
    }

    return LogProps;

}

//Rather than exporting FancyButton , we export LogProps.
//It will render a FancyButton though.

const TFancyButton = logProps(FancyButton);

export default class App extends React.Component{
    constructor(props){
        super(props);
        this.handleClick = this.handleClick.bind(this);
        this.textinputref = React.createRef();
    }
    handleClick(){
        console.info('App handle click this.textinputref=');
        console.info(this.textinputref.current);
        //this.textinputref.current指向LogProps实例，
        // 因此无法调用FancyButton的handleClick事件
        // this.textinputref.current.handleClick();
    }

    render(){
        return <TFancyButton label="click me"
                             handleClick={this.handleClick}
                             ref={this.textinputref}/>
    }
}

````

使用React.createRef()实例代码：
````
import React, { Component } from 'react';


class FancyButton extends React.Component{
    constructor(props){
        super(props);
        console.info('FancyButton constructor----');
        this.handleClick = this.handleClick.bind(this);
        this.curinputRef = React.createRef();
    }
    handleClick(){
        console.info('FancyButton handleClick----');
        this.curinputRef.current.focus();
    }
    render(){
        return (
            <div>
                <input type="text" ref={this.curinputRef}/>
                <button onClick={this.props.handleClick}>fancy button</button>
            </div>
        );
    }
}


function logProps(WrappedComponent){
    class LogProps extends React.Component{
        componentDidUpdate(prevProps){
            console.info('old props:', prevProps);
            console.info('new props:', this.props);
        }
        render(){
            console.info('logProps render---');
            console.info(this.props);
            const {forwardedRef,...rest} = this.props;
            //Assign the custom prop "forwardedRef" as a ref
            return <WrappedComponent ref={forwardedRef}   {...rest} />
        }
    }

    //Note the second param "ref" provided by React.forwardRef.
    // we can pass it along to LogProps as a regular prop,e.g."forwardedRef"
    // And it can then be attached to the Component.
    function fforwardRef(props, ref){
        return <LogProps {...props} forwardedRef={ref} />
    }

    //these next line are not necessary,
    //But they do give the component a better display name in DevTools,
    //e.g."ForwardRef(logProps(MyComponent))"
    const name = WrappedComponent.displayName || WrappedComponent.name;
    fforwardRef.displayName = `logProps(${name})`;

    return React.forwardRef(fforwardRef);

}

//Rather than exporting FancyButton , we export LogProps.
//It will render a FancyButton though.

const TFancyButton = logProps(FancyButton);

export default class App extends React.Component{
    constructor(props){
        super(props);
        this.handleClick = this.handleClick.bind(this);
        this.textinputref = React.createRef();
    }
    handleClick(){
        console.info('App handle click this.textinputref=');
        console.info(this.textinputref.current);
        //this.textinputref.current 指向组件FancyButton实例，故可以调用FancyButton的handleClick事件
        this.textinputref.current.handleClick();
    }

    render(){
        return <TFancyButton label="click me"
                             handleClick={this.handleClick}
                             ref={this.textinputref}/>
    }
}


````

## Render Props
"Render prop"是指一种在React组件之间使用一个值为函数的prop在React组件间共享代码的简单技术。
带有render prop的组件带有一个返回一个React元素的函数并调用该函数而不是实现自己的渲染逻辑。
````
<DataProvider 
   render = {
     data => (<h1>Hello {data.target}</h1>)
   }
/>

````

使用render props的库包括React Router 和Downshift。

### 在交叉关注点（Cross-Cutting Concerns）使用render props

组件在React中是主要的代码复用单元，但如何共享状态或一个组件的行为封装到其他需要相同状态的组件中并不是很明了。

例如，下面的组件在web应用追踪鼠标位置，代码示例：
````
import React from 'react';
export default class MouseTracker extends React.Component{
    constructor(props){
        super(props);
        this.handleMouseMove = this.handleMouseMove.bind(this);
        this.state = {
            x:0,
            y:0
        };
    }

    handleMouseMove(event){
        this.setState({
            x:event.clientX,
            y:event.clientY
        });
    }

    render(){
        return (
            <div style={{height:'100%'}} onMouseMove={this.handleMouseMove}>
                <h1>Move the mouse around!</h1>
                <p>The current mouse position is ({this.state.x},{this.state.y})</p>
            </div>
        );
    }
}

````

重构一部分代码能够在<Mouse>组件中封装我们需要在其他地方的行为，重构之后
代码示例：
````
import React from 'react';
// The <Mouse> component encapsulates the behavior we need ...
class Mouse extends React.Component{
    constructor(props){
        super(props);
        this.handleMouseMove = this.handleMouseMove.bind(this);
        this.state = {
            x:0,
            y:0
        };
    }
    handleMouseMove(event){
        this.setState({
            x:event.clientX,
            y:event.clientY
        });
    }
    render(){
        return (
            <div style={{height:'100%'}} onMouseMove={this.handleMouseMove}>
                <p>The current mouse position is ({this.state.x},{this.state.y})</p>
            </div>
        );
    }
}

export default class MouseTracker extends React.Component{
    render(){
        return (
            <div >
                <h1>Move the mouse around!</h1>
                <Mouse/>
            </div>
        );
    }
}

````

上述代码中<Mouse>组件封装了所有关于监听mousemove事件和存储鼠标（x,y）位置的
行为，但是还不是真正的可重用。

例如，假设我们现在有一个屏幕上跟随鼠标渲染一张猫的图片的<Cat>组件。我们可能
使用<Cat mouse={{x,y}} prop来告诉组件鼠标的坐标以让它知道图片应该在屏幕
哪个位置。

我们可以提供一个带有函数prop的<Mouse>组件，它能够动态决定什么需要渲染，而不是
将<Cat>硬编码到<Mouse>组件里，并有效地改变它的渲染结果。
如下示例代码所示
````
//index.css
.tborder{
  position: relative;
  width:300px;
  height:300px;
  border:2px solid red;
}

//app.js
import React from 'react';
import './index.css';
class Cat extends React.Component{
    render(){
        const mouse = this.props.mouse;
        return (
            <img src="/cat.jpg" style={{position:'absolute',left:mouse.x,top:mouse.y}}/>
        );
    }
}

// The <Mouse> component encapsulates the behavior we need ...
class Mouse extends React.Component{
    constructor(props){
        super(props);
        this.handleMouseMove = this.handleMouseMove.bind(this);
        this.state = {
            x:0,
            y:0
        };
    }
    handleMouseMove(event){
        this.setState({
            x:event.clientX,
            y:event.clientY
        });
    }
    render(){
        return (
            <div className="tborder"  onMouseMove={this.handleMouseMove}>
                {/*
                  Instead of providing a static representation of What <Mouse> renders,
                  use the `render` prop to dynamically determine what to render.
                */}
                <div>[{this.state.x},{this.state.y}]</div>
                {
                    this.props.render(this.state)
                }
            </div>
        );
    }
}

export default class MouseTracker extends React.Component{
    render(){
        return (
            <div >
                <Mouse render={
                    mouse => (
                        <Cat mouse={mouse} />
                    )
                } />
                <h1>Move the mouse around!</h1>
            </div>
        );
    }
}


````

如上述代码所示，我们提供了一个render prop以上<Mouse>能够动态决定什么需要渲染，而不是克隆<Mouse>组件并硬编码来解决特定的用例。

* render prop是一个组件用来了解要渲染什么内容的函数prop.

关于render props, 你可以使用给一个带有render props的常规组件来实现大量的高阶
组件(HOC)。例如，如果你更偏向于使用一个withMouse的高阶组件而不是一个Mouse组件，你可以轻松的创建一个带有render prop的常规<Mouse>组件的高阶组件。

示例代码：
````
//index.css
.tborder{
  position: relative;
  width:300px;
  height:300px;
  border:2px solid red;
}

//app.js
import React from 'react';
import './index.css';
class Cat extends React.Component{
    render(){
        const mouse = this.props.mouse;
        return (
            <img src="/cat.jpg" style={{position:'absolute',left:mouse.x,top:mouse.y}}/>
        );
    }
}

// The <Mouse> component encapsulates the behavior we need ...
class Mouse extends React.Component{
    constructor(props){
        super(props);
        this.handleMouseMove = this.handleMouseMove.bind(this);
        this.state = {
            x:0,
            y:0
        };
    }
    handleMouseMove(event){
        this.setState({
            x:event.clientX,
            y:event.clientY
        });
    }
    render(){
        return (
            <div className="tborder"  onMouseMove={this.handleMouseMove}>
                {/*
                  Instead of providing a static representation of What <Mouse> renders,
                  use the `render` prop to dynamically determine what to render.
                */}
                <div>[{this.state.x},{this.state.y}]</div>
                {
                    this.props.render(this.state)
                }
            </div>
        );
    }
}

//If you really want a HOC for some reason, you can easily
//create on using a regular component with a render prop!
function withMouse(Wcomponent){
    return class extends React.Component{
        render(){
            return (
                <Mouse render={mouse => (
                   <Wcomponent {...this.props} mouse={mouse} />
                )}/>
            );
        }
    }
}

const TwithMouse = withMouse(Cat);

export default class MouseTracker extends React.Component{
    render(){
        return (
            <div >
                <TwithMouse/>
                <h1>Move the mouse around!</h1>
            </div>
        );
    }
}

````

### Using Props Other Than render

仅仅因为这一个模式被称为"render props",而不必为使用该模式而用一个名
为render的prop。 组件能够知道什么需要渲染的任何函数prop在技术上都是“render prop”.

例如我们也可以简单的使用children prop!

````
<Mouse
  children={
    mouse => (
      <p>The mouse position is {mouse.x},{mouse.y} </p>
    )
  }
/>

````

另外children prop并不真正需要添加到JSX元素的“attributes”列表中。相反，
你可以直接放置到元素的内部！
````
<Mouse>
{
    mouse => (
      <p>The mouse position is {mouse.x},{mouse.y}</p>
    )
}

</Mouse>

````


### 注意事项
1. 在React.PureComponent 中使用render props要注意

如果你在render方法里创建函数，那么使用render prop会抵消使用React.PureComponent
带来的优势。这是因为浅prop比较新props总会返回false,并且在这种情况下每一个render
对于render prop将会是一个新的值。

例如，之前例子中使用的<Mouse>组件， 如果Mouse继承自React.PureCOmponent而不是
    React.Component,代码如下：
````
class Mouse extends React.PureComponent {
  // Same implementation as above...
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>

        {/*
          This is bad! The value of the `render` prop will
          be different on each render.
        */}
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}

````

这样，每次<MouseTracker>渲染，它会生成一个新的函数作为<Mouse render>的prop,因而在
同时也抵消了继承自React.PureComponent的<Mouse>组件的效果！

解决办法，代码如下：
````
class MouseTracker extends React.Component {
  constructor(props) {
    super(props);

    // This binding ensures that `this.renderTheCat` always refers
    // to the *same* function when we use it in render.
    this.renderTheCat = this.renderTheCat.bind(this);
  }

  renderTheCat(mouse) {
    return <Cat mouse={mouse} />;
  }

  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>
        <Mouse render={this.renderTheCat} />
      </div>
    );
  }
}

````
<Mouse> 应继承React.Component，万一你没法提前在构造函数中绑定实例方法（如因为你可能要掩盖组件的 props 和/或 state)。


## 与第三方库协同

我们可以在任何网页应用中使用React。不仅可以把React添加到其它应用里，而且只要稍作改动，我们
也可以把其它应用添加到React项目里。

待完善......

## React高阶API
react是React库的入口点。如果你通过<script>标签加载React,这些高阶API可用于React全局。
如果你使用ES6,你可以使用 ```import React from 'react'```。如果你使用ES5, 
你可以使用 ```var React = require('react');```。

### Reference

1. [React.Component](https://reactjs.org/docs/react-component.html)

2. React.PureComponent

React.PureComponent与React.Component几乎完全相同，但 React.PureComponent通过
prop和state的浅对比来实现shouldComponentUpdate()

如果React组件的 render() 函数 在给定相同的props 和 state下渲染为相同的结果，在某
些场景下你可以使用 React.PureComponent来提升性能。

注意：
React.PureComponent 的 shouldComponentUpdate() 只会对对象进行浅对比。
如果对象包含复杂的数据结构，它可能会因深层的数据不一致而产生错误的否定判断(表现为对象深层
    的数据已改变视图却没有更新)。当你期望只拥有简单的props和state时，才去继承
     PureComponent ，
     或者在你知道深层的数据结构已经发生改变时使用
     [forceUpate](https://reactjs.org/docs/react-component.html#forceupdate) 。或者，考虑使用 
     [不可变对象](https://facebook.github.io/immutable-js/) 来促进嵌套数据的快速比较。

此外,React.PureComponent 的 shouldComponentUpate() 会忽略整个组件的子级。请确保所有的子级组件也是”Pure”的。
