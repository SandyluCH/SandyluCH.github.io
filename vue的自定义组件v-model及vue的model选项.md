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
         
   
   