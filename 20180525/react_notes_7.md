[官方文档](https://reactjs.org/docs/error-boundaries.html)
[中文文档](https://doc.react-china.org/docs/error-boundaries.html)
## Error Boundaries

过去，组件内的JavaScript错误常常会破坏React内部状态并在下次渲染时产生加密的错误信息。这些错误总会在应用代码的早期触发，但React并没有提供一种方式能够在组件内部优雅地来处理，也不能从错误中恢复。


### 介绍
部分UI的异常不应该破坏了整个应用。为了解决React用户的这一问题，React 16引入了一种称为“错误边界”的新概念。

错误边界是用户捕获器子组件树JavaScript异常，记录错误并展示一个回退的UI的React组件，而不是整个组件树的异常，错误组件在渲染期间，生命周期方法内，以及整个组件树构造函数内捕获错误。

错误边界无法捕获如下错误：
- 事件处理

React不需要错误边界在事件处理器内将其从错误中恢复，不像渲染方法或生命周期钩子，事件处理器不会再渲染周期内触发。因此若他们抛出异常，
React仍然能够知道需要在屏幕上显示什么。

如果你需要在事件处理器内部捕获错误，使用普通的JavaScript try/catch语句


- 异步代码，如setTimeout 或requestAnimationFrame 回调函数
- 服务端渲染
- 错误边界自身跑出来的错误（而不是其子组件）

如果一个类组件定义了一个名为componentDidCatch(error,info)的新方法，则其成为一个错误边界，示例代码如下：

````
import React from 'react';

class ErrorBoundary extends React.Component{
    constructor(props){
        super(props);
        this.state = {
            hasError:false,
            error: null,
            errorInfo: null
        };
    }

    componentDidCatch(error, info){
        //Display fallback UI
        this.setState({
            hasError:true,
            error: error,
            errorInfo: info
        });
        //You can also log the error to an error reporting service
        // logErrorToMyService(error,info);
        // logComponentStackToMyService(info.componentStack);
    }

    render(){
        if(this.state.hasError){
            // You can render any custom fallback UI
            return <h1>Something went wrong.</h1>;
        }
        return this.props.children;
    }
}



class Mywidget extends React.Component{
    constructor(props) {
        super(props);
        this.state = { counter: 0 };
        this.handleClick = this.handleClick.bind(this);
    }

    handleClick() {
        this.setState(({counter}) => ({
            counter: counter + 1
        }));
    }
    render(){
        if (this.state.counter === 5) {
            // Simulate a JS error
            throw new Error('I crashed!');
        }
        return <h1 onClick={this.handleClick}>{this.state.counter}</h1>;
    }
}

export default class App extends React.Component{
    render(){
        return (
            <ErrorBoundary>
                <Mywidget/>
            </ErrorBoundary>
        );
    }
}

````

componentDidCatch()方法机制类似于JavaScript catch{},但是componentDidCatch()
方法是针对组件的。仅有类组件可以成为错误边界。实际上，大多数时间你
仅想要定义一个错误边界组件并在你的整个应用中使用。

注意：错误边界仅可以捕获其子组件的错误。错误边界无法捕获其自身的错误。如果一个错误边界无法渲染错误边界，则错误会向上冒泡至最接近的边界错误。这也类似于JavaScript中的catch{}的工作机制。

### componentDidCatch参数

error是被抛出的错误。

info 是一个含有componentStack属性的对象。这一属性包含了错误期间关
于组件的堆栈信息。


### 使用注意事项

错误边界的粒度完全取决于你的应用。你可以将其包装在最顶层的路由组件
并为用户展示一个“发生异常”的错误信息，就像服务端框架通常处理崩溃一
样。你也可以将单独的插件包装在错误边界内部以保护应用不受该组件崩溃的影响。

目前自React 16开始，任何未被错误边界捕获的错误将会卸载整个React组
件树。

例如：Facebook Messenger将侧边栏、信息面板、对话框以及信息输入框包
装在单独的错误边界中，如果其中的某些UI组件崩溃，其余部分仍然能够交
互。


## Web Components

React和web组件被用以解决不同问题。web组件为可重用组件提供了强大的封装能力，而React则是提供了保持DOM 和数据同步的声明式库。我们可以随意地在web组件里使用React, 或者
在React里使用web组件，或都有。

### 在React中使用Web组件

web组件通常保罗一个必要的API,为访问组件的必要API,你需要使用一个引用以能够直接和
DOM节点交互。若你正在使用第三方Web组件，其最好的解决方案是编写一个React组件，以包
装该Web组件。

由Web组件触发的事件可能无法通过React渲染树来正确冒泡。
你需要手动捕获事件处理器以处理那些在React组件里的事件。

````
class HelloMessage extends React.Component {
  render() {
    return <div>Hello <x-search>{this.props.name}</x-search>!</div>;
  }
}

````

### 在Web组件中使用React
示例代码：
````
const proto = Object.create(HTMLElement.prototype, {
  attachedCallback: {
    value: function() {
      const mountPoint = document.createElement('span');
      this.createShadowRoot().appendChild(mountPoint);

      const name = this.getAttribute('name');
      const url = 'https://www.google.com/search?q=' + encodeURIComponent(name);
      ReactDOM.render(<a href={url}>{name}</a>, mountPoint);
    }
  }
});
document.registerElement('x-search', {prototype: proto});

````

## 高阶组件

高阶组件（HOC）是React中对组件逻辑进行重用的高级技术。但是高阶组件本身
并不是React API。它只是一种模式，这种模式是由react自身的组合性质必然产生的。

具体而言，高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件。
即高阶组件将一个组件转换成另一个新组件。

高阶组件在React第三方库中很常见，比如Redux的connect方法和Relay的createContainer.

### 使用高阶组件（HOC）解决交叉问题

在React中，组件是代码复用的主要单元。

在使用高阶组件（HOC）之前，示例代码如下：
````
import React from 'react';
class DataSource{
    static commentList = [];
    static blogPost = {};
    constructor(){
        this.commentList = [];
        this.blogPost = {};
    }

    static  getComments(){
        return this.commentList;
    }
    static  getBlogPost(id){
        return this.blogPost;
    }

    static  addChangeListener(event){
        this.fetchData(event);
    }

    static removeChangeListener(event){

    }

    static fetchData(event){
        let _self = this;
        setTimeout(function () {
            _self.commentList = [{id:1,text:'comment 1'},{id:2, text:'comment 2'}];
            _self.blogPost = {id:1,text:'comment 1'};
            if(typeof event == 'function'){
                event();
            }
        },2000);
    }
}

class CommentList extends React.Component{
    constructor(){
        super();
        this.handleChange = this.handleChange.bind(this);
        this.state = {
            //"DataSource"就是全局的数据源
            comments:DataSource.getComments()
        };
    }

    componentDidMount(){
        //添加事件处理函数订阅数据
        DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount(){
        //清除事件处理函数
        DataSource.removeChangeListener(this.handleChange);
    }

    handleChange(){
        //任何时候数据发生改变就更新组件
        this.setState({
            comments:DataSource.getComments()
        });
    }

    render(){
        return (
            <div>
                {
                    this.state.comments.map((comment) => (
                        <div  comment={comment} key={comment.id} >
                            {comment.text}
                        </div>
                    ))
                }
            </div>
        );
    }
}

class BlogPost extends React.Component {
    constructor(props) {
        super(props);
        this.handleChange = this.handleChange.bind(this);
        this.state = {
            //"DataSource"就是全局的数据源
            blogPost: DataSource.getBlogPost(props.id)
        };
    }

    componentDidMount() {
        DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
        DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
        this.setState({
            blogPost: DataSource.getBlogPost(this.props.id)
        });
    }

    render() {
        return <div text={this.state.blogPost} >{this.state.blogPost.text}</div>;
    }
}


export default  class App extends React.Component{
    render(){
        return (
            <div>
                <CommentList/>
                <div>------------------------------</div>
                <BlogPost id="1"/>
            </div>
        );
    }
};

````

CommentList 和 BlogPost组件并不相同，他们调用调用了DataSource的不同方法
获取数据，并且他们渲染的输出结果也不相同。但是，他们大部分实现逻辑是一样的：
- 挂载组件时，向DataSource添加一个监听函数
- 在监听函数内，每当数据源发生变化，都是调用setState函数设置新数据
- 卸载组件时， 移除监听函数

那么我们可以抽象出一个模式，该模式允许我们在一个地方定义逻辑并且能对所有的组件使用，这就是高阶组件的精华所在。

我们写一个函数withSubscription，该函数能够创建类似CommonList 和BlogPost 从DataSource数据
原订阅数据的组件，该函数接受一个子组件作为其中的一个参数，并从数据源订阅
数据作为props属性传入子组件.

使用高阶组件（HOC）之后示例代码：

````

import React from 'react';
class DataSource {
    static commentList = [];
    static blogPost = {};
    constructor(){
        this.commentList = [];
        this.blogPost = {};
    }

    static  getComments(){
        return this.commentList;
    }
    static  getBlogPost(id){
        return this.blogPost;
    }

    static  addChangeListener(event){
        this.fetchData(event);
    }

    static removeChangeListener(event){

    }

    static fetchData(event){
        let _self = this;
        setTimeout(function () {
            _self.commentList = [{id:1,text:'comment 1'},{id:2, text:'comment 2'}];
            _self.blogPost = {id:1,text:'comment 1'};
            if(typeof event == 'function'){
                event();
            }
        },2000);
    }
}

class CommentList extends React.Component{
    constructor(props){
        super(props);
    }
    render(){
        return (
            <div>
                {
                    this.props.data.map((comment) => (
                        <div  comment={comment} key={comment.id} >
                            {comment.text}
                        </div>
                    ))
                }
            </div>
        );
    }
}

class BlogPost extends React.Component {
    constructor(props) {
        super(props);
    }
    render() {
        return <div text={this.props.data} >{this.props.data.text}</div>;
    }
}

//函数接受一个组件参数......
function withSubscription(WrappedComponent, selectData){
    //返回一个新组件
    return class extends React.Component{
        constructor(props){
            super(props);
            this.handleChange = this.handleChange.bind(this);
            this.state = {
                data:selectData(DataSource,props)
            };
        }

        componentDidMount() {
            DataSource.addChangeListener(this.handleChange);
        }

        componentWillUnmount() {
            DataSource.removeChangeListener(this.handleChange);
        }

        handleChange() {
            this.setState({
                data: selectData(DataSource,this.props)
            });
        }

        render(){
            //......使用最新的数据渲染组件
            //注意此处将已有的props属性传递给原组件
            return <WrappedComponent data={this.state.data} {...this.props} />
        }
    };
}

const CommentListWithSubscription = withSubscription(
    CommentList,
    (DataSource)=> DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
    BlogPost,
    (DataSource, props) => DataSource.getBlogPost(props.id)
);


export default  class App extends React.Component{
    render(){
        return (
            <div>
                <CommentListWithSubscription/>
                <div>------------------------------</div>
                <BlogPostWithSubscription id="1"/>
            </div>
        );
    }
};

````
上述代码中，withSubscription和包裹组件之间的关联是完全基于props属性的。


高阶组件是通过将元组件包裹（Wrapping）在容器组件（container component）
里面的方式来组合（composes）使用原组件。高阶组件就是一个没有副作用的纯函数。

包裹组件接收容器组件的所有props属性以及一个新的data属性，并用data属性渲染
输出内容。高阶组件并不关心数据是如何以及为什么被使用，而包裹组件也不关心数据来自
何处。


### Don't Mutate the Original Component. Use Composition.
不要在高阶组件内部修改（或以其他方式修改）原组件的原型属性。


下面示例代码就是在高阶组件的内部修改了原组件的原型属性。
````
function logProps(InputComponent) {
  InputComponent.prototype.componentWillReceiveProps(nextProps) {
    console.log('Current props: ', this.props);
    console.log('Next props: ', nextProps);
  }
  // 我们返回的原始组件实际上已经
  // 被修改了。
  return InputComponent;
}

// EnhancedComponent会记录下所有的props属性
const EnhancedComponent = logProps(InputComponent);

````
代码中的高阶组件对没有生命周期函数的无状态函数式组件也是无效的。如果你用另一个高阶
转变EnhancedComponent,同样的也去改变了componentWillReceiveProps函数是，
第一个高阶组件即EnhancedComponent转换的功能就会被覆盖。

更改性高阶组件（mutating HOCs）泄露了组件的抽象性————使用者必须知道他们
的具体实现，才能避免与其他高阶组件的冲突。

解决办法是  使用组合技术，将inputComponent包含到容器组件中，如代码所示：
````
function logProps(WrapperComponent){
   return class extends React.Component{
   	 componentWillReceiveProps(nextProps){
   	 	console.log('Current props :', this.props);
   	 	console.log('Next props:', nextProps);
   	 }

   	 render(){
   	 	//用容器㢟组合包裹组件且不修改包裹组件，这才是正确的打开方式。
   	 	return <WrappedComponent {...this.props} />;
   	 }
   }
}


````

上述代码示例是组合型高阶组件示例，它避免了发生冲突的可能。 组合型高阶组件对
类组件和无状态函数式组件适用性同样好。而且，因为它是一个纯函数，它和其他高阶组件，
甚至它自身也是可组合的。

### 约定
1. 将不相关的props属性传递给Wrapped component
高阶组件给组件添加新特性。他们不应该大幅修改原组件的接口。高阶组件应该传递与它要实现
的功能点无关的props属性。大多数高阶组件都包含了render函数。

2. 最大化使用组合

有时，高阶组件仅仅接收一个参数，即包裹组件：
```const NavbarWithRouter = withRouter(NavBar);  ```

有时，高阶组件会接收额外的参数，如下代码，来自Relay的示例中 可配置对象用于指定组件的数据依赖关系：
```const CommentWithRelay = Relay.createContainer(comment, config); ```

***待熟悉中......



3. 包装显示名字以便于调试

示例代码：
````
function withSubscription(WrappedComponent) {
  class WithSubscription extends React.Component {/* ... */}
  WithSubscription.displayName = `WithSubscription(${getDisplayName(WrappedComponent)})`;
  return WithSubscription;
}

function getDisplayName(WrappedComponent) {
  return WrappedComponent.displayName || WrappedComponent.name || 'Component';
}

````


### 使用注意事项
1. 不要在render函数中使用高阶组件

React使用的差异算法（称为协调） 使用组件表示确定是否更新现有的子对象树或丢掉现有的额
子树并重新挂载。如果render函数返回的组件和之前render函数返回的组件是相同，React就递归
的比较新子对象树和旧子对象树的差异， 并更新旧子对象树，如果他们不相等，就会完全卸载旧的
子对象树。

如代码所示：
````
 render(){
 	//每一次render函数调用都会创建一个新的EnhancedComponent实例
 	//EnhancedComponent1 != EnhancedComponent2
 	const EnhancedComponent = enhance(MyComponent);
 	//每一次都会使子对象树完全被卸载或移除
 	return <EnhancedComponent />;
 }

````
上述代码中在render函数使用的高阶组件，带来了不仅仅是性能问题，还有，重新加载一个组件会引起原有组件的所有状态和子组件丢失。

相反，在组件定义外使用高阶组件，可以使新组件只出现一次定义。在渲染的整个过程中，保证都是同一个组件。无论在任何情况下，这都是最好的使用方式。

在很少的情况下，可能需要动态的调用高阶组件，那么可以在组件的构造函数或生命周期函数中调用。

2. 必须将静态方法做拷贝

当使用高阶组件包装组件，原始组件被容器组件包裹，也就意味着新组件会丢失原始组件的所有静态方法。

如代码所示：
````
// 定义静态方法
WrappedComponent.staticMethod = function() {/*...*/}
// 使用高阶组件
const EnhancedComponent = enhance(WrappedComponent);

// 增强型组件没有静态方法
typeof EnhancedComponent.staticMethod === 'undefined' // true

````

解决这个问题的方法是，将原始组件的所有静态方法全部拷贝给新组件：
````
function enhance(WrappedComponent){
  class Enhance extends React.Component{/*...*/}
  //但是这里我们必须得知道需要拷贝的方法：如staticMethod
  Enhance.staticMethod = WrappedComponent.staticMethod;
  return Enhance;
}

````
那么优化方式就是，使用hoist-non-react-statics来帮你自动处理，它会自动拷贝所有非React的静态方法，如代码所示：

```
import hoistNonReactStatic from 'hoist-non-react-statics';
function enhance(WrappedComponent){
  class Enhance extends React.Component{/*...*/}
  hoistNonReactStatic(Enhance, WrappedComponent);
  return Enhance;
}

```


另外一种可能的解决方案就是分别导出组件自身的静态方法，
````
// 替代……
MyComponent.someFunction = someFunction;
export default MyComponent;

// ……分别导出……
export { someFunction };

// ……在要使用的组件中导入
import MyComponent, { someFunction } from './MyComponent.js';

````

3. Refs属性不能传递

高阶组件可以传递所有的props属性给包裹组件，但是不能传递refs引用，因为refs是一个伪属性，React对他进行了特殊处理。 如果你想一个由高阶组件创建的组件的元素添加ref应用，那么ref指向的是最外层容器组件示例，而不是包裹组件。

解决方法是React 16.3版本中的API : React.forwardRef