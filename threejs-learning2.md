[摘自](https://mp.weixin.qq.com/s?__biz=Mzg3MTUyNzQzNg==&mid=2247489071&idx=1&sn=22300e691f1f32474defc91dc1cee274&chksm=cefc734cf98bfa5a830394b9ad6810528cf25dba2c07132036c5d4c999529e98c83e3548d847&cur_album_id=2405559566127480834&scene=189#wechat_redirect)
## 纹理贴图
纹理就是覆盖几何体表面的图像，比如我们已经看腻了的红色立方体，加上纹理，立刻就可以变成一个快递包装盒子。

纹理的作用远不止取代颜色。

### Map 
(ColorMap) 颜色贴图是最简单的贴图方式，将图片上的每一个像素应用到几何体上。

### Emissive Map
自发光纹理贴图，使用一种有不同色阶灰色的图（灰度图），用于描述材质上哪些像素具有自发光特性。需要配合材质的emissive属性使用。

### Alpha Map
透明纹理贴图也是使用灰度图，越白则透明度越低，越黑则透明度越高。需要配合材质的transparent属性使用。

### Bump Map 、Normal Map 、 Displace Map
Bump Map凹凸贴图，Normal Map 法线贴图， Displacement Map置换贴图都是用于创建更丰富细节的一种技术。

凹凸贴图几乎已被淘汰，取代它的正是法线贴图，因为法线贴图可以存储比凹凸贴图更多的信息。但法线贴图并不会真正的改变模型，它只是和光发生反应，形成虚假的立体感细节，所以法线贴图在强化细节的同时不会产生额外的性能负担。

置换贴图则会通过改变模型结构来创建真正的细节，不过，这种方式会产生额外的性能消耗。

### Rough. Map
粗糙度纹理贴图也是使用灰度图，它用于标识材质表面的粗糙度，颜色越白则越粗糙，越黑则越光滑。

### Metal. Map
金属度纹理贴图也是使用灰度图，它将标识材质的哪个部分是金属 (白色) 和非金属 (黑色)。这些信息有助于创造反射。

### Env Map
环境反射贴图，用于创建在材质表面反射环境细节的效果。比如金属球可以像镜子一样反射出周围环境的细节。

### Light Map
光照贴图，用于来模拟物体和灯光均处于静止状态下的光照的效果。比如不会运动的墙面和吊灯射灯。

### AO Map 
全称是Ambient Occlusion环境闭塞贴图也是使用灰度图，但它用来模拟更加真实的阴影效果。

### 纹理贴图的使用
#### 使用原生JS
我们可以使用原生的Javascript来创建一个image对象并监听其onload事件，对这个对象的src属性赋值即可完成图片的加载。

````
const image = new Image()
image.onload = () =>
{
    console.log('image loaded')
}
image.src = '/textures/door/color.jpg'


````
不过，我们并不能直接使用image对象。我们需要用它来创建一个Texture纹理类型的对象。
````
const image = new Image()
image.addEventListener('load', () =>
{
    const texture = new THREE.Texture(image)
})
image.src = '/textures/door/color.jpg'
````
创建完Texure后，我们就可以在创建Material(材质)时使用这个纹理了。
````
const material = new THREE.MeshBasicMaterial({ map: texture })
````
有的时候，我们可能不等加载结束，需要先创建Texture对象并赋值，这样的话就需要在加载完成之后将needsUpdate属性设置为true来通知纹理刷新变量。
````
const image = new Image()
const texture = new THREE.Texture(image)
image.addEventListener('load', () =>
{
    texture.needsUpdate = true
})
image.src = '/textures/door/color.jpg'
````
这样的好处是，不用等待图片加载结束我们就可以使用纹理变量了，加载完成后再会刷新渲染即可。

#### 使用 TextureLoader
了解了原生JavaScript如何加载纹理图片后，我们再来学习Three.js中提供的一种更简单的方法TextureLoader。

创建TextureLoader，并使用其load(...) 方法加载纹理图片，
````
const textureLoader = new THREE.TextureLoader()
const texture = textureLoader.load('/textures/door/color.jpg')
````
只需要两行代码就可以，当然你也可以像这样写成一行。
````
const texture = new THREE.TextureLoader().load('/textures/door/color.jpg')
````
load方法还可以添加三个回调函数作为参数，

onload 当图片加载完成时
onprogress 当图片加载中，可以拿到加载进度
onerror 当图片加载遇到问题时
````
const textureLoader = new THREE.TextureLoader()
const texture = textureLoader.load(
  '/textures/door/color.jpg',
  () => {
    console.log('loading finished')
  },
  () => {
    console.log('loading progressing')
  },
  () => {
    console.log('loading error')
  }
)
````
通过这几个回调函数可以监听到图片文件的加载状况，用于创建加载进度条或是提示加载出错。
