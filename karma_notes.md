# [Karma](http://karma-runner.github.io/2.0/index.html)
  Karma运行在Node.js的环境下，而且可作为一个npm package;
  ## 安装
  1. 搭建node.js环境
  2. 安装karma及其相应的插件
  <pre>
    <code>
	 # Install Karma:
     $ npm install karma --save-dev
     # Install plugins that your project needs:
     $ npm install karma-jasmine karma-chrome-launcher jasmine-core --save-dev
	</code>
  </pre>
 ## 配置
 <pre>
    <code>
	$ karma init my.conf.js

	Which testing framework do you want to use?
	Press tab to list possible options. Enter to move to the next question.
	> jasmine

	Do you want to use Require.js?
	This will add Require.js plugin.
	Press tab to list possible options. Enter to move to the next question.
	> no

	Do you want to capture a browser automatically?
	Press tab to list possible options. Enter empty string to move to the next question.
	> Chrome
	> Firefox
	>
	//(这里也可以使用phantomjs,如果电脑上已安装phantomjs)

	What is the location of your source and test files?
	You can use glob patterns, eg. "js/*.js" or "test/**/*Spec.js".
	Press Enter to move to the next question.
	> *.js
	> test/**/*.js
	>

	Should any of the files included by the previous patterns be excluded?
	You can use glob patterns, eg. "**/*.swp".
	Press Enter to move to the next question.
	>

	Do you want Karma to watch all the files and run the tests on change?
	Press tab to list possible options.
	> yes

	Config file generated at "/Users/vojta/Code/karma/my.conf.js".
	</code>
 </pre>
 
 ## 启动 Karma
	When starting Karma, the configuration file path can be passed in as the first argument.
	By default, Karma will look for karma.conf.js or karma.conf.coffee in the current directory.
	<pre>
		<code>
		# Start Karma using your configuration:
		$ karma start my.conf.js
		</code>
	</pre>
	有关Karma 配置文件的更多详情，例如 可用的options和features ,
	请查阅[configuration file docs](http://karma-runner.github.io/2.0/config/configuration-file.html);


 ## karma是如何工作的
 
 
 ## [代码测试覆盖率分析](https://www.v2ex.com/t/385149)
 
 ## karma + webpack 的代码覆盖率测试
     这里如果直接用 karma-coverage 会出现直接对 karma 配置中入口文件生成的 webpack 代码的代码覆盖率测试。
 会出现很多 webpack 生成的额外代码。而且也不是源代码的代码覆盖率测试。
     这里有两个方案：
	 1. karma-coverage + isparta-loader
	  isparta-loader  已废弃，推荐istanbul-instrumenter-loader 	  
	 2. karma-coverage-istanbul-reporter + istanbul-instrumenter-reporter 
	    karma-coverage-istanbul-reporter  仅仅是报告，不运行你的代码的实际设置。
		babel 用户需要使用babel-plugin-istanbul插件   来配置你的代码
		webpack+typescript用户 需要使用istanbul-instrumenter-loader  
		然后使用 这个karma reporter 来执行实际报告。
	    
 
 
	
	
	
	