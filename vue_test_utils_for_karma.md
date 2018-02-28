##  [用karma测试单文件组件](https://vue-test-utils.vuejs.org/zh-cn/guides/testing-SFCs-with-karma.html) 学习
    karma是一个启动浏览器运行测试并生成报告的测试运行器。
	### 1. 设置mocha
	在已配置好的webpack-simple模板的条件下，
	安装模块
	<pre>
	   <code>
	      npm install --save-dev @vue/test-utils karma karma-chrome-launcher 
		  karma-mocha karma-sourcemap-loader karma-spec-reporter 
		  karma-webpack mocha

	   </code>
	</pre>
	### 2. Karma配置
	在项目的主目录创建一个karma.conf.js文件：
	<pre>
	   <code>
	   // karma.conf.js

		var webpackConfig = require('./webpack.config.js')

		module.exports = function (config) {
		  config.set({
			frameworks: ['mocha'],

			files: [
			  'test/**/*.spec.js'
			],

			preprocessors: {
			  '**/*.spec.js': ['webpack', 'sourcemap']
			},

			webpack: webpackConfig,

			reporters: ['spec'],

			browsers: ['Chrome']
		  })
		}
	   </code>	
	</pre>
	该文件用来配置karma.因为需要用webpack预处理文件，因此，需要将webpack
	添加为预处理器，并引入我们的webpack配置。我们可以在项目基础中使用该
	webpack配置文件而无需任何修改。
	
	### 3. 选用一个断言库
	Chai 是一个流行的常配合 Mocha 使用的断言库。你也可以选用 Sinon 来创建监视和存根。
	安装chai断言库
	<pre>
	   <code>
	     npm install chai
	   </code>
	</pre>

    我们可以安装 karma-chai 插件以在我们的测试中使用 chai。
	<pre>
	   <code>
	     npm install --save-dev karma-chai
	   </code>
	</pre>
	### 4. 添加一个测试
	在 src 中创建一个名为 Counter.vue 的文件：
	<pre>
	   <code>
	     <template>
		  <div>
			{{ count }}
			<button @click="increment">Increment</button>
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
	然后添加一个名为 test/Coutner.spec.js 的测试文件，并写入如下代码：
	<pre>
	  <code>
	    import { expect } from 'chai'
		import { shallow } from '@vue/test-utils'
		import Counter from '../src/Counter.vue'

		describe('Counter.vue', () => {
		  it('increments count when button is clicked', () => {
			const wrapper = shallow(Counter)
			wrapper.find('button').trigger('click')
			expect(wrapper.find('div').text()).contains('1')
		  })
		})
	  </code>
	</pre>
	### 5. 覆盖率
	我们可以使用karma-coverage插件来设置karma的代码覆盖率。
	默认情况下，karma-coverage不会使用source map来对照覆盖率报告。所以我们需要使用babel-plugin-istanbul
	来确认正确匹配的覆盖率。
	安装karma-coverage、babel-plugin-istanbul 和cross-env:
	<pre>
	   <code>
	     npm install --save-dev  karma-coverage cross-env
	   </code>
	</pre>
    我们会使用cross-env来设置一个BABEL_ENV环境变量。这样我们就可以在编译测试的额时候使用babel-plugin-istanbul
    因为不想在生产环境下引入babel-plugin-istanbul:
    <pre>
	   <code>
	       npm install --save-dev  babel-plugin-istanbul
	   </code>
	</pre>
    更新你的 .babelrc 文件，在因测试设置了 BABEL_ENV 时使用 babel-plugin-istanbul：	
	<pre>
	   <code>
	       {
			  "presets": [
				["env", { "modules": false }],
				"stage-3"
			  ],
			  "env": {
				"test": {
				  "plugins": ["istanbul"]
				}
			  }
			}
	   </code>
	</pre>
	现在更新 karma.conf.js 文件来进行覆盖率测试。添加 coverage 到 reporters 数组，并添加一个 coverageReporter 字段
	<pre>
	  <code>
	    // karma.conf.js

		module.exports = function (config) {
		  config.set({
		  // ...

			reporters: ['spec', 'coverage'],

			coverageReporter: {
			  dir: './coverage',
			  reporters: [
				{ type: 'lcov', subdir: '.' },
				{ type: 'text-summary' }
			  ]
			}
		  })
		}
	  </code>
	</pre>
	然后更新 test 脚本来设置 BABEL_ENV：
	<pre>
	   <code>
	     // package.json
			{
			  "scripts": {
				"test": "cross-env BABEL_ENV=test karma start --single-run"
			  }
			}
	   </code>
	</pre>