## [react native](http://facebook.github.io/react-native/docs/tutorial.html)

### navigating between screens(页面之间的导航)

 手机app很少只有一个页面。管理多页面之间的展示、切换， react native不像web browser有全局的内置的history stack，所以通常是用navigator来处理的。

 navigator的方式有：

#### React Navigation(反应式导航)

反应导航提供了一种易于使用的导航解决方案，具有在IOS和Android上呈现公共堆栈导航和标签导航模式的能力。
它是Javascript实现的，因此它提供了最大的可配置性和灵活性在与这个诸如Redux之类的状态管理库集成时

[官方文档](https://reactnavigation.org/docs/getting-started.html);	

[React Navigation API](https://reactnavigation.org/docs/en/api-reference.html);    

页面之间的切换和navigation历史管理是通过 React Navigation的stack navigator来做的。

如果你的app只使用了一个堆栈导航器，那么它在概念上类似于web浏览器处理导航状态，当用户与它交互时，应用程序从导航堆栈中推送和弹出项目，从而使用户看到不同的页面。在web浏览器和react navigation如何工作中一个关键区别是react navigation的stack navigator提供了在堆栈中的路径之间导航时你所希望展示在android和ios平台上的手势和动画
 
使用：

##### 1. 安装  yarn add react-navigation

##### 2. 创建stack navigator

     createStackNavigator方法会返回一个React组件。它带有一个路由配置对象和其他的对象。因为createStackNavigator方法会返回一个React组件，所以我们可以之间在App.js中使用，把它作为我们app的根组件。

<pre>
	<code>
		//样例
//src/components/homescreen.js文件
import React from 'react';
import {StyleSheet,Text, View,TextInput,Button,Picker,Alert,Platform } from 'react-native';

class HomeScreen extends React.Component {
    constructor(props) {
        super(props);
        this.state = { language: 'java' };
        this.os = Platform.OS;
    }

    btnClick(){
        Alert.alert('You tapped the button!');
    }

    render() {
        let _self = this;
        return (
            <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
                <Text>Home Screen</Text>
                <View style={styles.tcontainer} nativeID={'123'}>
                    <TextInput style={styles.inputSty} defaultValue={'please  input text here'} />
                    <Text style={[styles.tt,styles.tc]}>hehehe Open up App.js to start working on your app!</Text>
                    <Text style={styles.tc}>Changes you make will automatically reload.</Text>
                    <Text>current os = {this.os}</Text>
                    <Text>below is user interface</Text>
                    <Button
                        onPress={_self.btnClick}
                        title="Learn More"
                        color="#841584"
                        accessibilityLabel="Learn more about this purple button"/>

                    <Picker
                        mode={'dropdown'}
                        selectedValue={this.state.language}
                        style={{ height: 50, width: 100 }}
                        onValueChange={(itemValue, itemIndex) => this.setState({language: itemValue})}>
                        <Picker.Item label="Java" value="java" />
                        <Picker.Item label="JavaScript" value="js" />
                    </Picker>
                </View>
            </View>
        );
    }
}
const styles = StyleSheet.create({
    tcontainer: {
        backgroundColor: '#FFF',
        marginTop:20,
        alignItems: 'center',
        justifyContent: 'center',
    },
    tt:{
        fontSize:19,
        fontWeight:'bold'
    },
    tc:{
        color:'red'
    },
    inputSty:{
        borderLeftWidth:2
    }
});
export default HomeScreen


//根目录下App.js文件
import React from 'react';
import {createStackNavigator} from 'react-navigation';
import HomeScreen from './src/components/homescreen';
export default createStackNavigator({
    Home:{
        screen:HomeScreen
    }
})
//或者App.js代码
import React from 'react';
import {createStackNavigator} from 'react-navigation';
import HomeScreen from './src/components/homescreen';
const RootStack = createStackNavigator({
  Home:{
    screen:HomeScreen
  }
});
export default class App extends React.Component{
    render(){
        return <RootStack/>
    }
}

	</code>
</pre>

    (1). 路由配置快捷键
    如果组件中唯一的路由配置是屏幕组件，那么不需要使用{screen:HomeScreen}格式进行配置，我们可以直接：
<pre>
	<code>
		const RootStack = createStackNavigator({
		 Home: HomeScreen
	});
	</code>
</pre>
   (2). 多路由配置
   所有配置在createStackNavigator的方法的options参数中指定。如果我们不给options赋值，那么它将会使用默认的配置。
   例如：多路由时需要指定默认路由。
<pre>
	<code>
		const RootStack = createStackNavigator(
	  {
	    Home: HomeScreen,
	    Details: DetailsScreen,
	  },
	  {
	    initialRouteName: 'Home',
	  }
	);
	</code>
</pre>

##### 3.页面之间跳转
   代码例子：
<pre>
	<code>
		  //src/components/homescreen.js代码：
	    import React from 'react';
		import {StyleSheet,Text, View,TextInput,Button,Picker,Platform } from 'react-native';
		class HomeScreen extends React.Component {
		    constructor(props) {
		        super(props);
		        this.state = { language: 'java' };
		        this.os = Platform.OS;
		    }

		    btnClick(){       
		        this.props.navigation.navigate('Detail');
		    }

		    render() {
		        let _self = this;
		        return (
		            <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
		                <Text>Home Screen</Text>
		                <View style={styles.tcontainer} nativeID={'123'}>
		                    <TextInput style={styles.inputSty} defaultValue={'please  input text here'} />
		                    <Text style={[styles.tt,styles.tc]}>hehehe Open up App.js to start working on your app!</Text>
		                    <Text style={styles.tc}>Changes you make will automatically reload.</Text>
		                    <Text>current os = {this.os}</Text>
		                    <Text>below is user interface</Text>
		                    <Button
		                        onPress={()=>_self.btnClick.apply(this)}
		                        title="to detail screen"
		                        color="#841584"
		                        accessibilityLabel="Learn more about this purple button"/>

		                    <Picker
		                        mode={'dropdown'}
		                        selectedValue={this.state.language}
		                        style={{ height: 50, width: 100 }}
		                        onValueChange={(itemValue, itemIndex) => this.setState({language: itemValue})}>
		                        <Picker.Item label="Java" value="java" />
		                        <Picker.Item label="JavaScript" value="js" />
		                    </Picker>
		                </View>
		            </View>
		        );
		    }
		}
		const styles = StyleSheet.create({
		    tcontainer: {
		        backgroundColor: '#FFF',
		        marginTop:20,
		        alignItems: 'center',
		        justifyContent: 'center',
		    },
		    tt:{
		        fontSize:19,
		        fontWeight:'bold'
		    },
		    tc:{
		        color:'red'
		    },
		    inputSty:{
		        borderLeftWidth:2
		    }
		});
		export default HomeScreen

		//点击按钮“to detail screen”跳转到detail.js的页面




		  //src/components/detail.js代码：
		  	import React from 'react';
			import {View,Text} from 'react-native';
			class DetailsScreen extends React.Component {
			    render() {       
			        return (
			            <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
			                <Text>Details Screen</Text>
			                <Text>test</Text>
			            </View>
			        );
			    }
			}
			export default DetailsScreen

		  //App.js代码：
		  	import React from 'react';
			import {createStackNavigator} from 'react-navigation';
			import HomeScreen from  './src/components/homescreen';
			import DetailsScreen from './src/components/detail';
			const RootStack = createStackNavigator({
			    Home:HomeScreen,
			    Detail:DetailsScreen
			},{
			    initialRouteName: 'Home',
			});
			export default class App extends React.Component{
			    render(){
			        return <RootStack/>
			    }
			}

	</code>
</pre>
  
上述代码中涉及的知识点：
###### [Navigation prop](https://reactnavigation.org/docs/en/navigation-prop.html)
   app中的每个screen组件都会自动提供导航prop，像这样：
   1. this.props.navigation 下可调用的方法：   
    (1). navigate:  
     调转到另一个screen
    (2). goBack:
     关闭当前screen回到stack弹出的screen
	(3). addListener: 注册更新导航lifecycle
	(4). isFocused:如果屏幕focused返回true否则返回false
	(5). state: 当前路由的state
	(6). setParams : 设置路由的参数
	(7). getParam: 回调获取特定的参数
	(9). dispatch: 发送路由action

* 注意：并不是所有的组件都有navigation prop的传递，只有screen组件自动获取到navigation prop。例如：如果你定义一个MyBackButton组件并将其作为子组件在screen 组件中渲染，你将不能在MyBackButton组件中获取到navigation。
   


####  NavifatorIOS
    该组件不能运行在Android系统上面，只能运行在iOS系统上面。它提供了一种原生的look和感觉以最小的配置的导航方式，它提供了一个原生UINavigationController类的包装器。

####  native-navigation 
    可用来实现原生的外观和效果在android和iOS两个平台上面

####  react-native-navigation
    可用来实现原生的外观和效果在android和iOS两个平台上面
