[react官方文档](https://reactjs.org/docs/uncontrolled-components.html)

## Uncontrolled Components
在大多数情况下，我们推荐使用controlled components 去实现表单。在 controlled component里， form data 被React 组件处理。还有一种是uncontrolled components,
form data 被 DOM自己处理。

an uncontrolled component示例代码：
````
class NameForm extends React.Component{
    constructor(props){
        super(props);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleSubmit(event){
        alert('A name was submitted: '+ this.element.value);
        event.preventDefault();
    }

    render(){
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    Name:
                    <input type="text" ref={element=> this.element = element}  />
                </label>
                <input type="submit" value="Submit" />
            </form>
        );
    }
}

````

由于uncontrolled component 在DOM中保留了真实性的来源， 所以在使用uncontrolled component时，有时更容易集成React 和非React 代码。 如果你想要变得quick 和 dirty,
它也可以是 slightly less code。 否则，你应该使用controlled component.

[受控组件与非受控组件的对比](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/)

### Default Values
在React的渲染时间里，表单元素上的value属性值将会覆盖DOM中的value。
在uncontrolled component， 你通常想用React去指定初始值，但是不允许后续更新。
因此你可以指定一个defaultValue属性代替value。
例如：
````
class NameForm extends React.Component{
    constructor(props){
        super(props);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleSubmit(event){
        alert('A name was submitted: '+ this.element.value);
        event.preventDefault();
    }

    render(){
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    Name:
                    <input value="hello world"
                           type="text"
                           ref={element=> this.element = element}  />
                </label>
                <input type="submit" value="Submit" />
            </form>
        );
    }
}
````


上述代码中输入框的值一直是“hello world”,即被禁止输入了, 如果需要解除禁止输入,并且input的初始值是"hello world"，上述代码应该改为：
````
class NameForm extends React.Component{
    constructor(props){
        super(props);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleSubmit(event){
        alert('A name was submitted: '+ this.element.value);
        event.preventDefault();
    }

    render(){
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    Name:
                    <input defaultValue="hello world"
                           type="text"
                           ref={element=> this.element = element}  />
                </label>
                <input type="submit" value="Submit" />
            </form>
        );
    }
}

````

* 注意：```<input type="checkbox">``` and ```<input type="radio">``` 支持 defaultChecked, and ```<select>``` and ```<textarea>``` 支持 defaultValue.

### The file input Tag
在HTML中，  ```<input type="file">``` 让用户选择一个或多个文件从他们的设备存储其中，然后上传文件到服务器上面使用Javascript并通过File API来实现。

在React中， ```<input type="file"> ``` 总是一个不受控制的组件。因为它的值只能通过
用户设置，而不是programmatically。

你应该使用File API 去与文件交互。

示例代码：

````
class FileInput extends React.Component{
    constructor(props){
        super(props);
        this.handleSubmit = this.handleSubmit.bind(this);
    }

    handleSubmit(event){
        event.preventDefault();
        alert(`selected file - ${this.fileInput.files[0].name}`);
    }

    render(){
        return (
            <form onSubmit={this.handleSubmit}>
                <label>
                    Upload file:
                    <input type="file" ref={element => {
                        this.fileInput = element;
                    }} />
                </label>
                <br/>
                <button type="submit">Submit</button>
            </form>
        );
    }
}

````


