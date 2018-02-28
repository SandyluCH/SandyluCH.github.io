## vue-test-utils是Vue.js官方的单元测试实用工具库
https://github.com/vuejs/vue-test-utils-getting-started   该用例使用点:
    1、  regenerator-runtime   
     独立运行 for 再生器编译 生成器和异步方法
	2、  vue-jest
	 作用：帮助jest处理*.vue文件
	 
	 
	### 用jest测试单文件组件
     1. 安装jest, webpack, vue-loader, Babel ,vue-test-utils
        
	 
	 2. 安装和配置vue-jest预处理器：
	 <pre>
	    <code>
		   npm install --save-dev vue-jest
		</code>
	 </pre>
	 接下来在 package.json 中创建一个 jest 块：
     <pre>
	    <code>
		{
		  // ...
		  "jest": {
			"moduleFileExtensions": [
			  "js",
			  "json",
			  // 告诉 Jest 处理 `*.vue` 文件
			  "vue"
			],
			"transform": {
			  // 用 `vue-jest` 处理 `*.vue` 文件
			  ".*\\.(vue)$": "<rootDir>/node_modules/vue-jest"
			},
			"mapCoverage": true
		  }
		}
		</code>
	 </pre>
	 注意：vue-jest 目前并不支持 vue-loader 所有的功能，比如自定义块和样式加载。
	 额外的，诸如代码分隔等 webpack 特有的功能也是不支持的。
	 如果要使用它们，请阅读教程里的用 Mocha + webpack 测试单文件组件。
	 如果你在 webpack 中配置了别名解析，比如把 @ 设置为 /src 的别名，
	 那么你也需要用 moduleNameMapper 选项为 Jest 增加一个匹配配置：
	 <pre>
	    <code>
		    {
			  // ...
			  "jest": {
				// ...
				// 支持源代码中相同的 `@` -> `src` 别名
				"moduleNameMapper": {
				  "^@/(.*)$": "<rootDir>/src/$1"
				}
			  }
		    }
		</code>
	 </pre>
	 
	 3. 为Jest配置Babel
	 尽管最新版本的 Node 已经支持绝大多数的 ES2015 特性，
	 你可能仍然想要在你的测试中使用 ES modules 语法和 
	 stage-x 的特性。为此我们需要安装 babel-jest：
	 <pre>
	    <code>
		   npm install --save-dev babel-jest
		</code>
	 </pre>
	 
	 接下来，我们需要在package.json的jest.transform里添加一个
	 入口，来告诉Jest用babel-jest处理Javascript测试文件：
	 <pre>
	    <code>
		  {
			  // ...
			  "jest": {
				// ...
				"transform": {
				  // ...
				  // 用 `babel-jest` 处理 js
				  "^.+\\.js$": "<rootDir>/node_modules/babel-jest"
				},
				// ...
			  }
          }
		</code>
	 </pre>
	 
	 默认情况下，babel-jest 会在其安装完毕后自动进行配置。尽管如此，
	 因为我们已经显性的添加了对 *.vue 文件的转换，所以现在我们也需
	 要显性的配置 babel-jest。
	 
	 4.  测试快照
	       你可以使用vue-server-renderer将组件渲染为一个字符串，这样它就可以
	 为Jest快照测试保存一个快照。
	        vue-server-renderer的渲染结果包含了一些服务器渲染特有的特性，且忽略
	 空格，也不易于检索变更。我们可以通过一个自定义的序列化程序来改进被保存的快照。
        <pre>
		   <code>  npm install --save-dev jest-serializer-vue  </code>
		</pre>	
     然后在package.json中配置它：
     <pre>
	      <code>
		   {
			  // ...
			  "jest": {
				// ...
				// 快照的序列化程序
				"snapshotSerializers": [
				  "<rootDir>/node_modules/jest-serializer-vue"
				]
			  }
			}		  
		  </code>
	 </pre>
     ### 放置测试文件
	     默认情况下，Jest 将会递归的找到整个工程里所有 .spec.js 或 .test.js 
	 扩展名的文件。如果这不符合你的需求，你也可以在 package.json 里的配置
	 段落中改变它的 testRegex。
	     Jest 推荐你在被测试代码的所在目录下创建一个 __tests__ 目录，
	 但你也可以为你的测试文件随意设计自己习惯的文件结构。不过要当心 Jest 
	 会为快照测试在临近测试文件的地方创建一个 __snapshots__ 目录。
	 
	 5.  测试覆盖率
	     Jest可以被用来生成各种格式的测试覆盖率报告。以下是一个简单的起步的例子：
	     扩展你的jest配置（通常在package.json或jest.config.js中）的collectCoverage
     选项，然后添加collectCoverageFrom 数组来定义需要手机测试覆盖率信息的文件。你还
     需要设置mapCoverage 为true   ,以确保测试覆盖率数据的精准。
     <pre>
	     <code>
		    {
			  "jest": {
				// ...
				"collectCoverage": true,
				"collectCoverageFrom": [
				  "**/*.{js,vue}",
				  "!**/node_modules/**"
				],
				"mapCoverage": true
			  }
			}
		 
		 </code>
	 </pre>	 
	 6. [trace example](https://coding.net/u/Sandych/p/vue-test/git)
 
	 ### 用Mocha和webpack测试单文件组件
	     根据官方文档可知 该策略是指通过 webpack 编译所有的测试文件然后在测试运行器中运行。
	 这样做的好处是可以完全支持所有 webpack 和 vue-loader 的功能，
	 所以我们不必对我们的源代码做任何妥协。
	 从技术的角度讲，你可以使用任何喜欢的测试运行器并把所有的东西都手动串联起来，
	 但是我们已经找到了 mocha-webpack 能够为这项特殊任务提供非常流畅的体验。
	     1.  前提条件：安装webpack 、mocha、mocha-webpack
		 2.  在package.json中定义一个测试脚本
		 <pre>
		   <code>
		      "scripts": {
				"test": "mocha-webpack --webpack-config webpack.test.config.js --require test/setup.js test/**/*.spec.js"
			  }
		   </code>
		 </pre>
		 这里有一些注意事项：
			-- webpack-config 标识指定了该测试使用的 webpack 配置文件。在大多数情况下该配置会在其实际
			项目的配置文件基础上做一些小的调整。我们晚些时候会再聊到这一点。
			-- require 标识确保了文件 test/setup.js 会在任何测试之前运行，这样我们可以在该文件中设置
			测试所需的全局环境。最后一个参数是该测试包所涵盖的所有测试文件的聚合。   
	    3. 暴露 NPM 依赖
		     在测试中我们很可能会导入一些 NPM 依赖——这里面的有些模块可能没有针对浏览器的场景编写，也不适合
		被 webpack 打包。另一个考虑是为了尽可能的将依赖外置以提升测试的启动速度。我们可以通过 
		webpack-node-externals 外置所有的 NPM 依赖：
		webpack.base.config.js的代码如下：
		<pre>
		   <code>
		   
		   </code>
		</pre>
		在webpack.base.config.js基础上修改  代码：
		<pre>
		   <code>
		     // webpack.test.config.js
				const nodeExternals = require('webpack-node-externals')
				const baseconfig = require('webpack.base.config.js');
				var merge = require('webpack-merge');
				
				module.exports = merge(baseconfig,{				 
				   externals: [nodeExternals()],
				   devtool: 'inline-cheap-module-source-map'
				});
		   </code>
		</pre>
		4. 设置浏览器环境
		    vue-test-utils需要在浏览器环境中运行。我们可以在node中使用jsdim-global
		进行模拟：
		    <pre>
			   <code>
			        npm install --save-dev jsdom jsdom-global
			   </code>
			</pre>
		在test/setup.js中写入：
		<pre>
		    <code>
			   require('jsdom-global')();
			</code>
		</pre>
		5. 选用断言库
		Chai 是一个流行的断言库，经常和 Mocha 配合使用。你可能也想把 Sinon 用于创建间谍和存根。
		另外你也可以使用 expect，它现在是 Jest 的一部分，且在 Jest 文档里暴露了完全相同的 API。
		这里我们将使用 expect 且令其全局可用，这样我们就不需要在每个测试文件里导入它了：
		<pre>
		   <code>
		       npm install --save-dev expect
		   </code>
		</pre>
		然后在 test/setup.js 中编写：
		<pre>
		    <code>
				require('jsdom-global')()
				global.expect = require('expect')
			</code>
		</pre>
		6. 使用babel-loader来处理JavaScript.
		  安装："babel-core",  "babel-loader","babel-preset-env",
		  在 .babelrc 文件配置如下：
		  <pre>
		     <code>
			 "env": {
				"test": {
				  "presets": [
					["env", {
					  "modules": false,
					  "targets": { "node": "current" }
					}]
				  ]
				}
			  }
			 </code>
		  </pre>
		  7. 测试
		  <pre>
		     <code>
					<template>
						<div>
						  {{ count }}
						  <button @click="increment">自增</button>
						</div>
					</template>

					<script>
					export default {
					  data () {
						return {
						  count: 0
						}
					  },

					  methods: {
						increment () {
						  this.count++
						}
					  }
					}
					</script>			 
			 </code>
		  </pre>
		  然后创建一个名为 test/Counter.spec.js 的测试文件并写入如下代码：
		   <pre>
		     <code> 
				import { shallow } from 'vue-test-utils'
				import Counter from '../src/Counter.vue'

				describe('Counter.vue', () => {
				  it('计数器在点击按钮时自增', () => {
					const wrapper = shallow(Counter)
					wrapper.find('button').trigger('click')
					expect(wrapper.find('div').text()).toMatch('1')
				  })
				})
			 </code>
		  </pre>
		  现在我们运行测试：npm run test

		