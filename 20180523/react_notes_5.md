[react官方文档](https://reactjs.org/docs/context.html)

## Context

上下文提供了一种通过组件树传递数据的方法，而不必在每个级别手动地传递props。

在典型的React application中， 数据是通过props自顶向下传递的，但是这对于
某些类型的props(例如：区域偏好，UI主题)来说是繁琐的，这是应用程序中许多
组件所需要的。上下文提供了一种在组件之间共享这些值的方法，而不必显式的通过数的每一级传递道具。


### When to Use Context

Context被设计去分享数据，这些数据被视为是全局的，对于React components树来说。
未使用context的示例代码：
````
function Toolbar(props){
    // The Toolbar component must take an extra "theme" prop
    // and pass it to the ThemedButton. This can become painful
    // if every single button in the app needs to know the theme
    // because it would have to be passed through all components.
    return (
        <div>
            <ThemedButton theme={props.theme} />
        </div>
    );
}

function ThemedButton(props){
    return <button theme={props.theme}>test</button>
}

class App extends React.Component{
    render(){
        return <Toolbar theme="dark" />
    }
}


````

使用Context,我们可以避免通过中间元素来传递props,示例代码：
````
// A component in the middle doesn't have to
// pass the theme down explicitly anymore
function Toolbar(props){
    return (
        <div>
            <ThemedButton  />
        </div>
    );
}

function ThemedButton(props){
    //Use a consumer to read the current theme context.
    // React will find the closest theme Provider above and user its value.
    // In this example, the current theme is "tdark"
    return (
        <ThemeContext.Consumer>
            {
                ttheme => <button theme={ttheme} >test</button>
            }
        </ThemeContext.Consumer>
    );
}


//Context lets us pass a value deep into the component tree
//without explicity threading it through every component
//Create a context for the current theme(with "ttlight" as the default)
const ThemeContext = React.createContext('ttlight');
export default class App extends React.Component{
    render(){
        // use a Provider to pass the current theme to the tree below
        // Any component can read it ,no matter how deep it is
        // In this example , we're passing "dark" as the current value
        return (
            <ThemeContext.Provider value="tdark">
                <Toolbar/>
            </ThemeContext.Provider>
        );
    }
}

````
代码运行结果显示的是```<button theme="tdark" >test</button>```


### API

1. React.createContext
```const {Provider,Consumer} = React.createContext(defaultValue) ;```

创建{Provider,Consumer}对。 当React 渲染一个context Consumer, 它将会从tree中最近的matching Provider上面读取当前 context value 。

只有Consumer在tree中没有相匹配的Provider时，Consumer才会使用defaultValue的参数值
示例代码：
````
import React from 'react';

// A component in the middle doesn't have to
// pass the theme down explicitly anymore
function Toolbar(props){
    return (
        <div>
            <ThemedButton  />
        </div>
    );
}

function ThemedButton(props){
    //Use a consumer to read the current theme context.
    // React will find the closest theme Provider above and user its value.
    // In this example, the current theme is "tdark"
    return (
        <ThemeContext.Consumer>
            {
                ttheme => <button theme={ttheme} >test</button>
            }
        </ThemeContext.Consumer>
    );
}


//Context lets us pass a value deep into the component tree
//without explicity threading it through every component
//Create a context for the current theme(with "ttlight" as the default)
const ThemeContext = React.createContext('ttlight');
export default class App extends React.Component{
    render(){
        // use a Provider to pass the current theme to the tree below
        // Any component can read it ,no matter how deep it is
        // In this example , we're passing "dark" as the current value
        return (            
                <Toolbar/>           
        );
    }
}

````
代码运行结果显示的是```<button theme="ttlight" >test</button>```


2. Provider
```<Provider value={/* some value */}> ```

允许Consumer去订阅上下文更改的React Component。

接收value prop 去传给这个Provider的后代的Consumer。
一个Provider可以被许多的Consumers连接。 Provider可以被嵌套以覆盖树内更深的值。


3. Consumer
````
<Consumer>
  {value => /* render something based on the Context value */}
</Consumer> 

````

允许订阅上下文更改的React component。

需要一个函数作为子节点。这个函数接收当前的context value 并返回一个React node。
传给函数value参数 将等于tree上中最近的对应上下文的provider 传入的 value prop值。
如果这个上下文中没有对应provider,  the value参数的值将等于 createContext()方法
的默认参数值defaultValue.

无论Provider的value prop的值什么时候改变， 它所有的后代consumers都将重新渲染。从
Provider 到它的后代consumers 的传播不受 方法shouldComponentUpdate的控制，所以
即时祖先组件没有更新，the consumer 也会被更新。

### 例子

#### Dynamic Context
theme-context.js代码如下：
````
import React from 'react';
export const themes = {
    light: {
        foreground: '#000000',
        background: '#eeeeee',
    },
    dark: {
        foreground: '#ffffff',
        background: '#222222',
    },
};

export const ThemeContext = React.createContext(
    themes.dark // default value
);

````

themed-button.js代码如下：
````
import {ThemeContext} from './theme-context';
import React from 'react';
function ThemedButton(props) {
    return (
        <ThemeContext.Consumer>
            {theme => (
                <button
                    {...props}
                    style={{backgroundColor: theme.background}}
                />
            )}
        </ThemeContext.Consumer>
    );
}

export default ThemedButton;

````

app.js代码如下：
````
import React from 'react';
import {ThemeContext, themes} from './theme-context';
import ThemedButton from './themed-button';

// An intermediate component that uses the ThemedButton
function Toolbar(props) {
    return (
        <ThemedButton onClick={props.changeTheme}>
            Change Theme
        </ThemedButton>
    );
}

export default class App extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            theme: themes.light,
        };

        this.toggleTheme = () => {
            this.setState(state => ({
                theme:
                    state.theme === themes.dark
                        ? themes.light
                        : themes.dark,
            }));
        };
    }

    render() {
        // The ThemedButton button inside the ThemeProvider
        // uses the theme from state while the one outside uses
        // the default dark theme
        return (
            <div>
                <ThemeContext.Provider value={this.state.theme}>
                    <Toolbar changeTheme={this.toggleTheme} />
                </ThemeContext.Provider>
                <div>
                    <ThemedButton >currentTheme</ThemedButton>
                </div>
            </div>
        );
    }
}


````

#### Updating Context from a Nested Component


