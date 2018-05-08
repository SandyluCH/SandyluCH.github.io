## [react native](http://facebook.github.io/react-native/docs/tutorial.html)

### navigating between screens(页面之间的导航)

 手机app很少只有一个页面。管理多页面之间的展示、切换， react native不像web browser有全局的内置的history stack，所以通常是用navigator来处理的。

 navigator的方式有：
 1. React Navigation(反应式导航)
	反应导航提供了一种易于使用的导航解决方案，具有在IOS和Android上呈现公共堆栈导航和标签导航模式的能力。
	它是Javascript实现的，因此它提供了最大的可配置性和灵活性在与这个诸如Redux之类的状态管理库集成时
	[官方文档](https://reactnavigation.org/docs/getting-started.html);
    安装 yarn add react-navigation    
    页面之间的切换和navigation历史管理是通过 React Navigation的stack navigator来做的。






 2. NavifatorIOS
    该组件不能运行在Android系统上面，只能运行在iOS系统上面。它提供了一种原生的look和感觉以最小的配置的导航方式，它提供了一个原生UINavigationController类的包装器。

 3. native-navigation 
    可用来实现原生的外观和效果在android和iOS两个平台上面

 4. react-native-navigation
    可用来实现原生的外观和效果在android和iOS两个平台上面
