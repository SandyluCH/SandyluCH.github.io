1、 vue-cli  : 2.x类型的vue-cli版本
2、 @vue/cli  :3.x的最新vue-cli版本
    @vue/cli-init: vue init 命令插件 for @vue/cli,   是2.x类型的vue-cli版本的别名。
3、 @vue/cli-service-global 
    安装这个模块可以让你直接运行run serve和vue build命令而不需要任何本地的依赖。
    流程：
    <pre>
	  <code>
	    >    npm install -g @vue/cli-service-global
		//将内容<template><h1>Hello!</h1></template>输入到当前目录的App.vue文件中
		>    echo "<template><h1>Hello!</h1></template>" > App.vue
		>    vue serve
	  </code>
	</pre>	
	
	<pre>
	   <code>
	      //package.json    中script代码改为：默认mode是development  ,当前改为production
	       vue-cli-service serve   --mode production
	   </code>
	</pre>
	
4、[vue.config.js](https://github.com/vuejs/vue-cli/blob/dev/docs/config.md)	