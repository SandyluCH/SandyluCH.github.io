### [expo使用](https://expo.io/learn)
Expo是一个免费和开源的工具链，围绕着React Native的构建来帮助你使用JavaScript和React构建原生的iOS和Android项目。
#### 功能：
1. 相机，推送通知，ARKit
2. 空中更新已发布的项目
3. 分发到App Store

[expo的文档](https://docs.expo.io/versions/v27.0.0/introduction/installation)

#### [Expo development tool (expo的开发工具)](https://expo.io/tools#cli)
1. Expo Client: expo的客户端

在部署之前运行项目，然后通过扫描QR码在客户端打开项目，或直接查看运行的项目并打开。
有android端和iOS端的安装包。

2. Expo CLI: expo命令行环境

serve、share、build and publish Expo项目

步骤：

(1).  全局安装npm install exp -g；
(2).  在当前项目目录下面启动项目exp start

3. Expo Snack
  
  让你可以在浏览器中运行完整的React Native项目
  [地址](https://snack.expo.io/)

4. Expo SDK 
   
  该工具是一种库。可以在你的Expo 项目中访问原生跨平台设备API。

5. Expo XDE
  
  提供给桌面使用的。如果你不想使用Expo CLI,那么Expo XDE会提供一个图形化的开发环境来serve,share and publish 你的
  Expo 项目。[下载地址](https://github.com/expo/xde/releases)



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
   ScrollViews必须有一个有界的高度，所以要么直接设置一个具体的高度，或者
保证所有的parent views都有一个确定的高度。不要使用flex:1,会导致错误的。

   ScrollView & FlatList
   
   ScrollView 仅仅只是render 所有的react 子组件一次，不适用与多数据列表，
   而FlatList render items lazily,只有在items 出现在屏幕时才加载，而移除
   哪些被滑出屏幕的items以节省内存。

6. StyleSheet   样式类（用来创建样式，类似于CSS StyleSheets）
   创建一个stylesheet类

	<pre>
      <code>
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
      </code>
	</pre>
	

代码中的‘container’，‘title’,'activeTitle'是命名，该命名是可自定义的，
只要定义和使用的命令一直就可以。

   使用

	<View style={styles.tcontainer}>
	  <Text style={[styles.title, this.props.isActive && styles.activeTitle]} />
	</View>

##### user interface(用户接口)
1. Button
2. Picker
3. Slider
4. Switch

##### List Views
1. FlatList
2. SectionList

##### iOS 组件和APIs
以下组件提供了通用UIKit 类的包装器
1. ActionSheetIOS
2. AlertIOS
3. DatePickerIOS
4. ImagePickerIOS
5. NavigatorIOS
6. ProgressViewIOS
7. PushNotificationIOS
8. SegmentedControlIOS
9. TabBarIOS

##### Android 组件和APIs
以下组件提供了通用android类的包装器
1. BackHandler
2. DatePickerAndroid
3. DrawerLayoutAndroid
4. PermissionsAndroid
5. ProgressBarAndroid
6. TimePickerAndroid
7. ToastAndroid
8. ToolbarAndroid
9. ViewPagerAndroid

##### 其他
以下组件可能在特定的应用中有用。
1. ActivityIndicator
2. Alert
3. Animated
4. CameraRoll
5. Clipboard
6. Dimensions
7. KeyboardAvoidingView
8. Linking
9. Modal
10. PixelRatio
11. RefreshControl
12. StatusBar
13. WebView


#### react  native 中平台代码区分
当构建跨平台的应用程序时，会希望尽可能多地使用代码。可能会出现以下场景，代码可能会不同，比如你可能想去为android和iOS实现不同可视化组件。
react native提供了两种简单的方式去阻止你的代码并按照平台分割他们。
##### 1、使用Platform module来区分
1.Platform.OS 获取当前系统
当react native运行在ios系统上面上的时候，Platform.OS == 'ios'（为true）
当react native运行在android系统上的时候，Platform.OS == 'android'(为true)

2.Platform.select（）
这个方法传入以Platform.OS为key的对象，返回当前运行平台的相关数据

	for example1:
	import {Platform, StyleSheet} from 'react-native';
	const styles = StyleSheet.create({
	  container: {
	    flex: 1,
	    ...Platform.select({
	      ios: {
	        backgroundColor: 'red',
	      },
	      android: {
	        backgroundColor: 'blue',
	      },
	    }),
	  },
	});

	for example2:
	const Component = Platform.select({
	  ios: () => require('ComponentIOS'),
	  android: () => require('ComponentAndroid'),
	})();
	<Component />;

3. Platform.Version  获取版本
检测android版本
<pre>
	<code>
		import {Platform} from 'react-native';
		if (Platform.Version === 25) {
		  console.log('Running on Nougat!');
		}
	</code>
</pre>
检测ios版本
<pre>
	<code>
	import {Platform} from 'react-native';
	const majorVersionIOS = parseInt(Platform.Version, 10);
	if (majorVersionIOS <= 9) {
	  console.log('Work around a change in behavior');
	}
	</code>
</pre>



##### 2、使用platform-specific file extensions 来区分
当你的平台差异性代码很负责的时候，你应该考虑将代码分割到一个单独的文件中。React Native将会检测到文件名后缀为.ios或.android的文件并下载相关的平台文件，当required 其他组件时。

For example, say you have the following files in your project:

	BigButton.ios.js
	BigButton.android.js

You can then require the component as follows:

	const BigButton = require('./BigButton');

React Native will automatically pick up the right file based on the running platform.






   