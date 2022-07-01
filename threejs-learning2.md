[摘自](https://mp.weixin.qq.com/s?__biz=Mzg3MTUyNzQzNg==&mid=2247489071&idx=1&sn=22300e691f1f32474defc91dc1cee274&chksm=cefc734cf98bfa5a830394b9ad6810528cf25dba2c07132036c5d4c999529e98c83e3548d847&cur_album_id=2405559566127480834&scene=189#wechat_redirect)

[摘自2](https://mp.weixin.qq.com/s/cEM3UBHHhYFc8HHI2rTaoA)


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

#### 使用LoadingManager
背景：在商业项目中，我们经常需要加载很多纹理图片，3D模型等等。我们需要建立一个整体的加载管理器，以确保用户加载完了所有必要资源后才使用我们的网站。这个时候就可以使用LoadingManager。

创建LoadingManager类的实例，并将其传递给TextureLoader：
````
const loadingManager = new THREE.LoadingManager()
const textureLoader = new THREE.TextureLoader(loadingManager)

````
我们可以对loadingManager添加onStart, onLoad, onProgress和  onError的监听来了解全部资源的加载情况
````
const loadingManager = new THREE.LoadingManager()
loadingManager.onStart = () =>
{
    console.log('loading started')
}
loadingManager.onLoad = () =>
{
    console.log('loading finished')
}
loadingManager.onProgress = () =>
{
    console.log('loading progressing')
}
loadingManager.onError = () =>
{
    console.log('loading error')
}

const textureLoader = new THREE.TextureLoader(loadingManager)

````
接着我们可以一次性添加所有的纹理贴图资源并管理他们的加载进度
````
// ...

const colorTexture = textureLoader.load('/textures/door/color.jpg')
const alphaTexture = textureLoader.load('/textures/door/alpha.jpg')
const heightTexture = textureLoader.load('/textures/door/height.jpg')
const normalTexture = textureLoader.load('/textures/door/normal.jpg')
const ambientOcclusionTexture = textureLoader.load('/textures/door/ambientOcclusion.jpg')
const metalnessTexture = textureLoader.load('/textures/door/metalness.jpg')
const roughnessTexture = textureLoader.load('/textures/door/roughness.jpg')
LoadingManager非常有用，除了TextureLoader，在Three.js中其他内置的Loader都可以使用LoadingManager来统一管理。

````

### UV
假如我们把3D立方体的面一一展开，立方体上每一个顶点其实都对应了图片上的一个二维坐标

为了和三维空间的XYZ区分，所以使用UV来代表XY。

所有3D模型都是由面构成的，我们都可以将他们的面展开到一个平面上，通常情况下，是个矩形

这叫做UV展开。我们可以从几何体的attributes.UV属性中看到这些2D坐标：
```
console.log(geometry.attributes.uv)
```

### 转换纹理贴图
我们可以使用repeat属性来使贴图重复，repeat的x，y属性分别代表了在x轴和y轴上的重复次数。
````
const colorTexture = textureLoader.load('/textures/door/color.jpg')
colorTexture.repeat.x = 2
colorTexture.repeat.y = 3

````
设置轴向上的重复次数外，我们还应该设置两个轴向上重复的模式

wrapS 代表x轴上的重复模式
wrapT 代表y轴上的重复模式
````
colorTexture.wrapS = THREE.RepeatWrapping
colorTexture.wrapT = THREE.RepeatWrapping

````

## 材质
材质的作用， 简单来说就是给几何体的每个可见像素加上颜色。但加颜色是要根据光照、材质的物理特性等因素，经过一系列的算法后， 才能决定这些像素到底显示什么颜色。这个算法
被卸载名为着色器shader的程序中。

Threejs中也有一些预置的材质。

### 预置材质——MeshBasicMaterial
MeshBasicMaterial如同它的名字一样，的确是最 “基础” 的材质......

我们可以在实例化材质时传递属性参数，也可以在创建后再来更改这些属性：

````
const material = new THREE.MeshBasicMaterial({
    map: doorColorTexture
})

// Equals
const material = new THREE.MeshBasicMaterial()
material.map = doorColorTexture
````
我们就使用第二种方法吧，看起来更清晰一些

map颜色贴图属性可以在几何体的表面映射一个纹理贴图
````
material.map = doorColorTexture
````
color颜色属性将在几何体的表面上设置统一的颜色。我们可以使用多种Web端常见的表示颜色的值，但必须实例化一个THREE.Color对象。
````
material.color = new THREE.Color('#ff0000')
material.color = new THREE.Color('#f00')
material.color = new THREE.Color('red')
material.color = new THREE.Color('rgb(255, 0, 0)')
material.color = new THREE.Color(0xff0000)

````
同时使用`color` 和 `map`时，纹理贴图将被颜色影响。
````
material.map = doorColorTexture
material.color = new THREE.Color('#ff0000')

````
将wireframe线框属性设置为true时将显示组成几何体的三角形网格，无论相机的距离是多少，网格线都是1像素的线宽。
````
material.wireframe = true

````
除了通过opacity属性整体设置材质的透明度以外，我们还可以使用alphaMap透明度贴图来让材质的个别部分透明。
````
material.transparent = true
material.alphaMap = doorAlphaTexture
````
三维世界中的几何体是由面组成的，而一个面也分为正面和反面，默认情况下，Three.js只会渲染正面。
*注意：并非面向相机就叫正面。

而通过设置side面渲染属性可以让我们决定到底渲染正面还是背面THREE.BackSide或者两面都渲染THREE.DoubleSide
*注意：当设置为DoubleSide时，性能消耗更大，因为要渲染2倍数量的三角形面
```
material.side = THREE.DoubleSide
```

### 预置材质——法线材质MeshNormalMaterial

法线就是始终垂直于平面的一根线，也就代表了面的朝向。而在三维引擎中，每个顶点都有法线信息。
既然法线代表了顶点的朝向，那自然就可以用于计算如何反射光线或折射光线。

当使用MeshNormalMaterial时，颜色只会显示法线相对相机的方向。这就是说如果我们绕着球体旋转，你会看到颜色总是一样的。

除了wireframe，opacity等基础属性，MeshBasicMaterial还可以使用一个新的flatShading平面着色属性：
````
material.flatShading = true

````
MeshNormalMaterial通常用来调试和观测法线信息，但它看起来很绚丽，所以也可以直接拿来做一些很独特的效果。

### 预置材质——材质捕捉材质MeshMatcapMaterial
这个名字有点绕口，但Matcap的确是由Material和Capture两个单词组合而成，其意思就是材质捕捉。

渲染通常需要几何体、光源、材质、shader 的共同参与。而matcap 是将光源、材质信息在3D建模软件中直接烘焙到一张纹理贴图上，渲染时直接拿来用即可，计算量自然大大减少，性能提升明显。我们还可以很方便的在不同的 matcap 纹理之间切换，看上去就和切换材质一样。

使用MeshMatcapMaterial材质时必须使用一个看起来像球体的参考纹理贴图。

材质将根据相对于相机的法线方向在纹理贴图上提取颜色。

设置材质捕捉贴图：
````
const material = new THREE.MeshMatcapMaterial()
material.matcap = matcapTexture
````

有一些面看起来好像被光照亮了，但其实并不需要任何光照参与计算。

“
不过有一个问题，由于光照和材质信息是预先烘焙到纹理贴图上的，所以无论相机方向如何改变，灯光如何调整角度，它看上去的效果是一样的。

”
下面我们可以多尝试几个不同的matcap纹理贴图
````
const matcapTexture = textureLoader.load('/textures/matcaps/2.png')
const matcapTexture = textureLoader.load('/textures/matcaps/3.png')
const matcapTexture = textureLoader.load('/textures/matcaps/4.png')
const matcapTexture = textureLoader.load('/textures/matcaps/5.png')
const matcapTexture = textureLoader.load('/textures/matcaps/6.png')
const matcapTexture = textureLoader.load('/textures/matcaps/7.png')
const matcapTexture = textureLoader.load('/textures/matcaps/8.png')
````
网上可以找到很多matcap纹理贴图，可以理解为开箱即用的材质 https://github.com/nidorx/matcaps

### 预置材质——深度材质MeshDepthMaterial
MeshDepthMaterial这种材质的外观不是由光照或者某个材质决定，而是由物体到相机的远近距离决定，当物体离相机较近时会呈现白色，较远时会呈现黑色。

### 预置材质——光照材质MeshLambertMaterial
MeshLambertMaterial是我们要学习的第一种会对光产生反应的材质：

### 预置材质——Phong材质MeshPhongMaterial
Phong是20世纪70年代被提出的一种渲染逼真光照效果的算法，以作者Bui Tuong Phong的姓氏命名。
MeshPhongMaterial则是应用这种算法的材质。效果和MeshLambertMaterial类似，但光影明暗过度更加自然，性能的消耗也略高于MeshLambertMaterial。
您可以通过亮度属性控制光的反射。值越大反射越强，表面越亮，看上去更光洁。您还可以使用specular高光色属性来更改反射的颜色:
````
material.shininess = 100
material.specular = new THREE.Color(0x1188ff)
````
### 预置材质——卡通材质MeshToonMaterial
MeshToonMaterial卡通材质的可以让我们的几何体表现出2次元卡通的风格，俗称3渲2：
````
const material = new THREE.MeshToonMaterial()
````
默认情况下，我们只能看到两种的颜色 (一个用于暗面，一个用于亮面)。如果想要更多的颜色过度，可以使用gradientMap属性并加载gradientTexture：
```
material.gradientMap = gradientTexture
```
如果我们直接设置gradientMap，会发现卡通效果失效了，明暗过度太丝滑了。这是因为我们使用的梯度纹理很小，这和我们在纹理贴图小节中了解过的minFilter，magFilter和mipmapping有关系。

解决方法也很简单，只需要将minFilter 和 magFilter设置为THREE.NearestFilter即可

别忘了加入generatempmaps = false：
````
gradientTexture.minFilter = THREE.NearestFilter
gradientTexture.magFilter = THREE.NearestFilter
gradientTexture.generateMipmaps = false
````

### 预置材质——标准材质MeshStandardMaterial
MeshStandardMaterial标准材质使用基于物理规则的渲染原理，就是我们在纹理课中了解过的PBR。之所以被称之为标准，是因为PBR已经成为很多3D渲染引擎的标准，而无论你在任何软件，引擎中使用标准材质时，得到的结果都是一样的。
像MeshLambertMaterial和MeshPhongMaterial一样，标准材质必须有灯光参与，有更加逼真的光影明暗过度和更多的参数，比如粗糙度roughness和金属度metalness。

### 预置材质——物理材质MeshPhysicalMaterial
物理材质MeshPhysicalMaterial是MeshStandardMaterial的扩展或者说加强版，提供更高级的基于物理的渲染属性

### 预置材质——Points材质PointsMaterial
专门给THREE.Points使用的一种材质，后面我们做粒子效果的时候再来用用看它。

### 预置材质——着色器材质ShaderMaterial
着色器材质ShaderMaterial和原始着色器材质RawShaderMaterial都可以用来创建自己的材质，它需要我们学习GLSL语法，暂时先跳过，我们将在专门的课程中学习它。
