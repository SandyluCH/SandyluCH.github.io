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

