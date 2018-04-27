[文档链接](https://cn.vuejs.org/v2/api/#model);
## vue的model选项
   v2.2.0中新增
   类型：{ prop? : string  , event ? : string }
   即允许一个自定义组件在使用v-model时 定制prop 和event。
   个人理解：该model选项有两个作用：
	   1 、 form表单组件的v-model指令的自定义实现，即，如果我们对于表单
	   组件中的value有其他的使用想法（例如格式化等），我们可以使用model选项，将
	   表单组件自定义一下    
	   2、  实现父子组件的传值的双向绑定（当然实现数据双向绑定的方式有很多）
         
   ### form表单组件 进行v-model自定义实现
      *  form表单的输入绑定  
	   文档资料回顾：可以用v-model指令在表单input即textarea元素上创建双向数据绑定。
	   它会根据空间类型自动选取正确的方法来更新元素。另外v-model的本事上不过是语法糖。
	   他负责监听用户的输入时间以及更新数据，并对一些极端场景进行了一些特殊处理。
	   
	   注 :v-model会忽略所有表单元素的value、checked、selected特性的初始化值，而总是将
	   Vue实例的数据作为数据来源。我们可以通过Javascript在组件的data选项中声明初始值。
	   
	   ** 文本
	   <pre>
	      <code>
		    <input type='text' v-model="message" />
			相当于
			<input type='text'  :value='value'  @input="value=$event.target.value" />
		  </code>
	   </pre>
	   那么自定义的input组件的写法可以如下：
	    <pre>
	      <code>
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
			    <inputtext v-model="inputTx"></inputtext>
             ---{{inputTx}}
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
		  
		  </code>		  
		</pre>
			
	   ** 多行文本	   
	    <pre>
	      <code>
		    <textarea v-model="message" placeholder="add multiple lines"></textarea>			
		  </code>
	   </pre>
	   
	   ** 复选框
	    <pre>
	      <code>
		     <input type="checkbox" v-model="mcheck" />		    		
		  </code>
	   </pre>	   
	   	  	   
	   **  单选按钮
	    <pre>
	      <code>
		    <input type="radio" v-model="pick" v-bind:value="a">	
		  </code>
	   </pre>
	   
	   **  选择框
	    <pre>
	      <code>
			<select v-model="selected">
			  <!-- 内联对象字面量 -->
			  <option v-bind:value="{ number: 123 }">123</option>
			</select>	
		  </code>
	   </pre>
	   
	   
	   
	   <pre>
	      <code>
		  
		  
		  </code>		  
		</pre>
	   
	   
	   
	   
	   
		
   