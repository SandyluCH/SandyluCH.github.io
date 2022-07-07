## [draco](https://google.github.io/draco/)
使用 Draco 库压缩的几何图形加载器。

Draco 是一个开源库，用于压缩和解压缩 3D 网格和点云。 压缩后的几何图形可以明显更小，代价是客户端设备上的额外解码时间。

独立的 Draco 文件具有 .drc 扩展名，并包含顶点位置、法线、颜色和其他属性。 Draco 文件不包含材质、纹理、动画或节点层次结构——要使用这些功能，请将 Draco 几何图形嵌入到 glTF 文件中。 
可以使用 glTF-Pipeline 将普通的 glTF 文件转换为 Draco 压缩的 glTF 文件。 当使用带有 glTF 的 Draco 时，GLTFLoader 将在内部使用 DRACOLoader 的实例。

建议创建一个 DRACOLoader 实例并重复使用它，以避免加载和创建多个解码器实例。

官方提供的npm库有：
- draco3d
- draco3dgltf
- draco_animation




