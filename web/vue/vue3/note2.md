## 获取dom节点
### 方式一
````
<template>
  <input ref="inputRef"/>
</template>
<script>
export default{
  setup(){
    let inputRef = ref(null)
    // 访问input 节点， 使用inputRef.value
    return {
      inputRef
    }
  }
}
</script>
````
### 方式二
````
<template>
 <input :ref="el=>{if(el) item.inputRef = ref}"/>
<template>
<script>
export default {
  setup(){
    let item = reactive({
      inputRef: null
    })
    return {
      item
    }
  }
}
</script>


````