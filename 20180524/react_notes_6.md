[react官方文档](https://reactjs.org/docs/fragments.html)

## Fragments
[中文文档](https://doc.react-china.org/docs/fragments.html)

Fragments可以让你聚合一个子元素列表，而不在DOM中增加额外的节点。

Fragments看起来像空的JSX标签如下代码所示：
````
render(){
	return (
		<>
			<ChildA />
			<ChildB />
			<ChildC />
		</>
	);
}

````

### 解决的问题

如果没有Fragment，会出现下面情况
````
class Table extends React.Component{
	render(){
		return (
			<table>
			  <tr>
                 <Columns />
			  </tr>
			</table>
		);
	}
}

class Columns extends React.Component{
	render(){
		return (
			<div>
				<td>Hello</td>
				<td>World</td>
			</div>
		);
	}
}


````

Columns 组件中的td必须被一个DOM包起来，不然会有语法错误，
但是如果用DOM包起来， 最终渲染出来的表格不是有效的，渲染最终的结果如下代码所示：

````
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>

````

因此需要用Fragment, 使用Fragment的Column组件的示例代码如下：
````
import React from 'react';
class Columns extends React.Component{
    render(){
        return (
            <React.Fragment>
                <td>Hello</td>
                <td>World</td>
            </React.Fragment>
        );
    }
}
export default  class Table extends React.Component {
    render() {
        return (
            <table>
                <tr>
                    <Columns />
                </tr>
            </table>
        );
    }
}

````

渲染之后的结果是：
````
<table><tr><td>Hello</td><td>World</td></tr></table>

````




## Portals

语法：```ReactDOM.createPortal(child,container) ```
第一个参数child :  任何可渲染的React child,例如element, string 或者fragment.
第二个参数container: 是一个DOM element

### Usage
将render返回的组件插入到指定的DOM节点中，示例代码：
````
 render(){
 	//React does not create a new div. It renders the children into "domNode"
 	//"domNode" is any valid DOM node, regardless of its location in the DOM.
 	return ReactDOM.createPortal(
         this.props.children,
         domNode,
 	);
 }

````

### Event Bubbling Through Portals

尽管portal可以被放置在DOM树的任何地方，但在其他方面其行为和普通的React子
节点行为一致。 由于portal仍存在于React树中，而不用考虑其在DOM树中的位置，所以
无论其子节点是否portal, 如上下文特性依然能够如之前一样正确的工作。


一个从portal内部会触发的事件会一直冒泡至包含React树的祖先。
示例代码如下：
/public 下的index.html
````
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="theme-color" content="#000000">
    <!--
      manifest.json provides metadata used when your web app is added to the
      homescreen on Android. See https://developers.google.com/web/fundamentals/engage-and-retain/web-app-manifest/
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json">
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
    <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.

      Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
    <title>React App</title>
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <div id="app-root"></div>
    <div id="modal-root"></div>
    <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.

      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.

      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
  </body>
</html>

````

App.js代码：
````
import React from 'react';
import ReactDOM from 'react-dom';

const modalRoot = document.getElementById('modal-root');

class Modal extends React.Component{
    constructor(props){
        super(props);
        this.el = document.createElement('div');
    }

    componentDidMount(){
        //The portal element is inserted in the DOM tree after
        //the Modal's children are mounted, meaning that children
        // will be mounted on a detached DOM node. If a child
        // component requires to be attached to the DOM tree
        // immediately when mounted, for example to measure a
        //DOM node, or uses 'autoFocus' in a descendant, add
        // state to Modal and only render the children when Modal
        // is inserted in the DOM tree.
        modalRoot.appendChild(this.el);
        console.info(this.props);
    }

    componentWillUnmount(){
        modalRoot.removeChild(this.el);
    }

    render(){
        return ReactDOM.createPortal(
            this.props.children,
            this.el
        );
    }
}

function Child(){
    // The click event on this button will bubble up to parent,
    //because there is no 'onClick' attribute defined
    return (
        <div className="modal">
            <button>Click</button>
        </div>
    );
}

export default class Parent extends React.Component{
  constructor(props){
      super(props);
      this.state = {clicks:0};
      this.handleClick = this.handleClick.bind(this);
  }

  handleClick(){
      //This will fire when the button in child is clicked
      // updating Parent's state , even though button
      // is not direct descendant in the DOM
      this.setState(prevState => ({
          clicks: prevState.clicks + 1
      }));
  }

  render(){
      return (
          <div onClick = {this.handleClick}>
              <p>Number of clicks: {this.state.clicks}</p>
              <p>
                  Open up the browser DevTools to observe that the
                  button is not a child of the div with the onClick
                  handler.
              </p>
              <Modal name="test modal">
                  <Child/>
                  <button>test</button>
              </Modal>
          </div>
      );
  }

}

````

index.js代码：
````
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';
ReactDOM.render(
    <App  />,
    document.getElementById('app-root')
);
registerServiceWorker();

````

代码中，modal组件的button的click事件冒泡到 包含它的React组件上即Parent组件上。