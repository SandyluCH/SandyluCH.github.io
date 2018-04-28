[文档链接](https://cn.vuejs.org/v2/api/#model);
## vue的model选项
   v2.2.0中新增
   类型：{ prop? : string  , event ? : string }
   即允许一个自定义组件在使用v-model时 定制prop 和event。
   
   个人理解：该model选项有两个作用：
	   1. form表单组件的v-model指令的自定义实现，即，如果我们对于表单
	   组件中的value有其他的使用想法（例如格式化等），我们可以使用model选项，将表单组件自定义一下    
	   2.  实现父子组件的传值的双向绑定（当然实现数据双向绑定的方式有很多）
         
### form表单组件 进行v-model自定义实现
#### form表单的输入绑定  
	   文档资料回顾：可以用v-model指令在表单input即textarea元素上创建双向数据绑定。
	   它会根据空间类型自动选取正确的方法来更新元素。另外v-model的本事上不过是语法糖。
	   他负责监听用户的输入时间以及更新数据，并对一些极端场景进行了一些特殊处理。
	   注 :v-model会忽略所有表单元素的value、checked、selected特性的初始化值，而总是将
	   Vue实例的数据作为数据来源。我们可以通过Javascript在组件的data选项中声明初始值。
	   
##### 文本
		    <input type='text' v-model="message" />
			相当于
			<input type='text'  :value='value'  @input="value=$event.target.value" />
那么自定义的input组件的写法可以如下：

		  //自定义文本组件    位置：components/inputtext.vue
		    <template>
      		  <input type="text" :value="tval" @input="inputClick($event)"/>
		    </template>
			<script>
			   export default{
			        model:{
				      prop:'tval',//名字可以自定义
					  event:'tinput',//事件名字也可以自定义
				    },
				    props:['tval'],	//父组件改变文本同步给子组件
				    methods:{
						inputClick($event){
						//子组件文本变化同步给父组件
							let val = $event.target.value;
							this.$emit('tinput',val);
						}
					}
			   }	
              //注意，当tval是基本数据类型非引用类型数据时，在子组件中不能直接改变tval的值，如果改变会
              // 报vue  warning警告，因为会有子组件改变父组件内容的可能性，这是vue框架所不允许的。			  
			</script>
			
			//父组件app.vue中使用该自定义组件
			<template>
			   <div>
			     <inputtext v-model="inputTx"></inputtext>
             ---{{inputTx}}
			   </div>
			</template>
			<script>
			    import inputtext from './components/inputtext.vue';
				export default{
				   data:function(){
				       return {
					     inputTx:null
					   }				   
				   },
				   components:{
				       inputtext
				   }				
				}			
			</script>		
##### 多行文本	   
		    <textarea v-model="message" placeholder="add multiple lines"></textarea>	
            //相当于
            <textarea :value="content" @input="inputContent" ></textarea>			
那么自定义的textarea组件的写法如下：

	        //自定义组件textarea
			<template>
			   <div>
                  <textarea :value="content" @input="inputContent"></textarea>
               </div>
			</template>
			<script>
				export default {
					model:{
						prop:'content',
						event:'inputC',
					},
					props:['content'],
					methods:{
						inputContent($event){
						  this.$emit('inputC',$event.target.value)
						}
					}
				}
			</script>
			
			//在app.vue中使用自定义组件
			<template>
			   <div>
                  <mtextarea v-model="tcontent"></mtextarea>
                  -----{{tcontent}}
               </div>
			</template>
			<script>
			  import mtextarea from './components/textarea.vue';
			  export default {
			    data: function () {
                   return {
				      tcontent:'init content',				   
				   }
                },
				components: {
					mtextarea
				}				
			  }			
			</script>		  
	   
##### 复选框

		     <input type="checkbox" v-model="mcheck" />
              //相当于
			    <input type="checkbox" @change="checkChange" />
自定义checkbox组件写法

            //自定义组件文件
			<template>
				<!--自定义checkbox-->
				<div>
				  <input type="checkbox"
						 @change="changeCheck($event)"
						 :value="defaultVal"
						 :checked="isCheck"/>
				</div>
			</template>
			<script>
				export default {
					model:{
						prop:'tval',
						event:'mchange',
					},
					props:['tval','defaultVal'],//父组件可以通过tval的值来改变子组件的checkbox的选中状态，defaultVal是默认值
					data() {
						return {
							isCheck:false,
						}
					},
					watch:{
						'tval':function (v1) {
							let _self = this;
							_self.checkIsCheck(v1);
						}
					},
					mounted(){
						let _self = this;
						_self.checkIsCheck(_self.tval);
					},
					methods:{
						changeCheck($event){
							console.info($event);
							let _self = this;
							let target = $event.target;
							_self.checkIsArray(Boolean(target.checked),target.value);
						},
						checkIsArray(isTrue,defaultV){
							//判断是否是数组
							let _self = this;
							if(Array.isArray(_self.tval)){
								let arr = [];
								for(let index = 0; index < _self.tval.length; index++){
									let i = _self.tval[index];
									if(i != defaultV){
										arr.push(i);
									}
								}
							   if(isTrue){
									arr.push(defaultV);
							   }
								_self.$emit('mchange',arr);
							}else{
								if(isTrue){
									_self.$emit('mchange',defaultV);
								}else{
									_self.$emit('mchange',null);
								}
							}
						},
						checkIsCheck(v1){
							//判断是否需要checkbox是否是选中状态
							let _self = this;
							if(Array.isArray(v1)){
								_self.isCheck =  v1.indexOf(_self.defaultVal)>-1?true:false;
							} else {
								_self.isCheck =  v1 == _self.defaultVal?true:false;
							}
						}
					}
				}
			</script>
			
			
			//父组件中写法
			<template>
			    <div>
				    <cuscheckbox v-model="tcheck"  :defaultVal="1"></cuscheckbox>					
					<cuscheckbox v-model="tcheck"  :defaultVal="2"></cuscheckbox>
					-----{{tcheck}}				
				</div>
			</template>
			<script>
			     import cuscheckbox from './components/cuscheckbox.vue';
				 export default{
				    data() {
						return {
							tcheck:[],// 当tcheck的类型是数组的时候，是多选； 当tcheck的类型是object的时候，是单选
						}
					},
					components: {
					   cuscheckbox
					}				 
				 }			
			</script> 	   	  	   
##### 单选按钮
	<input type="radio" v-model="pick" v-bind:value="a">
	<!-- 相当于 -->
	<input type="radio" @change="changeRadio($event)"/>
自定义radio子组件写法如下：

   子组件

	<template>
	    <div>
	        <!--自定义radio-->
	        <input type="radio"
	               @change="changeRadio($event)"
	               :value="defaultVal"
	               :checked="tval == defaultVal"/>{{defaultVal}}
	    </div>
	</template>
	<script>
	    export default {      
	        model:{
	            prop:'tval',
	            event:'mchange',
	        },
	        props:['tval','defaultVal'],
	        methods:{
	            changeRadio($event){
	                let _self = this;
	                _self.$emit('mchange',$event.target.value);
	            }
	        }
	    }
	</script>

   父组件使用

	<template>
	    <div id="app">
	        <inputradio v-model="inputRadio" :defaultVal="1"></inputradio>
	        <inputradio v-model="inputRadio" :defaultVal="2"></inputradio>
	        ---{{inputRadio}}
	    </div>
	</template>
	<script>
	   import inputradio from './components/inputradio.vue';
		export default{
			data: function () {
		  			inputRadio:1,
		    }
		}	 
	</script>







##### 选择框
	<select v-model="selected">
	  <!-- 内联对象字面量 -->
	  <option v-bind:value="{ number: 123 }">123</option>
	</select>	
	  
		
   