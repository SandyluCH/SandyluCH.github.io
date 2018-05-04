### [exp使用](https://expo.io/learn)
步骤：
1.  全局安装npm install exp -g；
2. 在当前项目目录下面启动项目exp start

### [react](https://reactjs.org/tutorial/tutorial.html)

 react相关基础： JSX, components, state 和 props。

   1. JSX[相关文档地址](http://www.css88.com/react/docs/jsx-in-depth.html)

React使用JSX来替换常规的JavaScript。JSX的优点：
执行更快，因为它在编译为JavaScript代码后进行了优化；类型安全，在编译
过程中就能发现错误，使用JSX编写模板更加简单快捷。



### [react native](http://facebook.github.io/react-native/docs/tutorial.html)
    
#### react native组件和api

##### 基础组件
1. View   最基础的组件
  View is a container (支持flexbox布局，style，some touch handling 和
  accessibility controls)。
  View直接映射到原生的同等view上面，无论react native运行在哪个平台上，如：
  UIView,<div>,android.view等。

2. Text   文本
3. Image  图片
4. TextInput    输入框
5. ScrollView   可滑动的视图或者容器
6. StyleSheet   样式类（用来创建样式，类似于CSS StyleSheets）
   创建一个stylesheet类

	
	const styles = StyleSheet.create({
	  tcontainer: {
	    borderRadius: 4,
	    borderWidth: 0.5,
	    borderColor: '#d6d7da',
	  },
	  title: {
	    fontSize: 19,
	    fontWeight: 'bold',
	  },
	  activeTitle: {
	    color: 'red',
	  },
	});

代码中的‘container’，‘title’,'activeTitle'是命名，该命名是可自定义的，
只要定义和使用的命令一直就可以。

   使用

	<View style={styles.tcontainer}>
	  <Text style={[styles.title, this.props.isActive && styles.activeTitle]} />
	</View>








   