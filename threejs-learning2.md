[摘自](https://mp.weixin.qq.com/s?__biz=Mzg3MTUyNzQzNg==&mid=2247489071&idx=1&sn=22300e691f1f32474defc91dc1cee274&chksm=cefc734cf98bfa5a830394b9ad6810528cf25dba2c07132036c5d4c999529e98c83e3548d847&cur_album_id=2405559566127480834&scene=189#wechat_redirect)
## 纹理贴图
纹理就是覆盖几何体表面的图像，比如我们已经看腻了的红色立方体，加上纹理，立刻就可以变成一个快递包装盒子。

纹理的作用远不止取代颜色。

### Map 
(ColorMap) 颜色贴图是最简单的贴图方式，将图片上的每一个像素应用到几何体上。

### Emissive Map
自发光纹理贴图，使用一种有不同色阶灰色的图（灰度图），用于描述材质上哪些像素具有自发光特性。需要配合材质的emissive属性使用。
