### 参考文档地址
[英文文档](https://v3.vuejs.org/api/instance-properties.html#refs)
[中文文档](https://v3.cn.vuejs.org/api/instance-properties.html#data)

## render、 createVNode、h的使用
使用示例mask.js
````
export default function Mask(){
  const maskComp = {
    name:'mask',
    render(){
      return h('div',{
        class:'mask',
        onClick: ()=>{
          this.close()
        }
      },h('p',{
        class:'mask-msg'
      },this.dateMsg))
    },
    setup(){
      let mask = ref(null)
      let {dateMsg} = useTimer()
      const close = ()=>{
        if(vm.el&&vm.el.parentNode){
          vm.el.parentNode.removeChild(vm.el)
        }
      }
      onUnmounted(()=>{
        close()
      })
      return {
        dateMsg,
        close,
      }
    },
  }
  const vm = createVNode(maskComp)
  const container = document.createElement('div')
  render(vm, container)
  document.body.appendChild(vm.el)
  return {
    vm
  }
}
````

调用：
````
import Mask from './mask.js'
Mask()

````