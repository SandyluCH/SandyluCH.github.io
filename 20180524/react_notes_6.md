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
