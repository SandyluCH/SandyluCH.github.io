### 参考文档地址
[英文文档](https://v3.vuejs.org/api/instance-properties.html#refs)
[中文文档](https://v3.cn.vuejs.org/api/instance-properties.html#data)

## render、 createVNode、h的使用
### 使用示例mask.js
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

### 使用实例2
````
import EToastContructor from './index.vue'
import { createVNode, isVNode, render } from 'vue'

let seed = 1
let instances = []

const EToast = function(opts = {}){
  if(typeof window === 'undefined')return  
  const id = 'toast' + seed++
  const userOnClose = opts.onClose
  let options = {
    ...opts,
    onClose: () => {
      close(id, userOnClose)
    },
    id
  }
  const container = document.createElement('div')
  container.className = `container_${id}`
  const message = options.txt

  const vm = createVNode(
    EToastContructor,
    options,
    isVNode(options.txt) ? { default: () => message } : null,
  )
  // clean message element preventing mem leak
  vm.props.onDestroy = () => {
    render(null, container)
    // since the element is destroy, then the VNode should be collected by GC as well
    // we do not want cause any mem leak because we have returned vm as a reference to users
    // so that we manually set it to false.
     // here we were suppose to call document.body.removeChild(container.firstElementChild)
    // but render(null, container) did that job for us. so that we do not call that directly
  }
  vm.props.onTimeout = ()=>{
    typeof opts.onTimeout === 'function' && opts.onTimeout()
  }
  render(vm, container)
  instances.push({ vm })
  document.body.appendChild(container.firstElementChild)
  return {
    close: ()=> vm.component.proxy.isVisible = false
  }
}

export function close(id, userOnClose) {
  const idx = instances.findIndex(({ vm }) => {
    const { id: _id } = vm.component.props
    return id === _id
  })
  if (idx === -1) {
    return
  }

  const { vm } = instances[idx]
  if (!vm) return
  userOnClose?.(vm)

  const removedHeight = vm.el.offsetHeight
  instances.splice(idx, 1) // Remove vm to avoid mem leak.

}


export function closeAll(){
  for (let i = instances.length - 1; i >= 0; i--) {
    const instance = instances[i].vm.component
    instance.ctx.close()
  }
}

EToast.closeAll = closeAll

EToast.install = (app)=>{
  app.config.globalProperties.$toast = EToast
}
export default EToast

````