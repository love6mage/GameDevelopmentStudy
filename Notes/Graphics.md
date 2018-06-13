# Graphics

## Depth-Buffered Triangle Rasterization

- `Triangle Mesh` 三角网格，用于模拟复杂物体表面
  - `Winding Order` 三角形绕序，顺时针或者逆时针
  - `Triangle Lists` 三角形列表构成三角网格
    - `Indexed Triangle Lists` 顶点数据在一处缓存保存，顶点索引列表在另一处缓存保存
    - `Strips and Fans` 顶点数据以条状或扇形排列，每个新加入顶点与已有的两个顶点构成三角形
    - `Vertex Cache Optimization` 通过离线的几何处理工具排序顶点，优化缓存顶点的复用
- `Back-face culling` 通过三角形绕序判断物体背面并剔除，避免浪费资源渲染物体不可见部分
- `Tessellation` 细分曲面技术，根据物体表面与镜头距离自动镶嵌顶点，使表面表现得平滑
- `Triangle-to-pixel density` 自动镶嵌顶点的目标：一个三角形对应一个像素
- `Level of detail (LOD)` 多细节层次，根据物体与镜头距离切换层次，每个层次是物体表面一个可替换的三角网格，常用于水面和各种地形
- `Model Space` 模型空间、本地空间、物体空间，网格顶点在物体本地的坐标系中表示
- `World Space` 自然空间，多个网格通过在自然空间中定位和定向组成一个完整的场景
- `Mesh Instancing` 物体的三角网格的顶点从模型空间变换到自然空间
- `Light-Object Interactions` 光在可以被吸收、反射（漫射和镜面反射）、衍射（一般不考虑）、散射、折射
- `Color Model` 用三元组或四元组表示色彩的数学模型
- `Color Space` 色彩模型所能定义的色彩范围即色彩空间、色域
- `Vertex Attributes` 顶点属性可以用来描述物体表面的视觉特性
  - `Position vector` 顶点在模型空间的位置
  - `Vertex normal` 顶点处的单位表面法线
  - `Vertex tangent and bitangent` 与顶点法线一起两两垂直，构成一个坐标系`tangent space`（切线空间），用于逐像素照明计算
  - `Diffuse color` 漫射色
  - `Specular color` 高光反射色
  - `Texture coordinates` 纹理坐标，用于将二维或三维位图映射到网格表面上（`texture mapping`），描述顶点在纹理的二维归一化坐标空间中的位置
  - `Skinning weights` 蒙皮权重,一个顶点受连接骨骼的多个关节影响
- `Vertext Formats` 保存的顶点属性的数据结构的布局
- `Attribute Interpolation` 渲染三角形基于逐像素而不是逐顶点,所以需要对顶点属性进行插值得到内部点的属性
- `Gouraud shading` 高洛德着色，对顶点的色彩属性进行插值
- `Textures` 纹理，包含色彩信息和其他信息，可以映射到网格的三角形上；一些图形硬件约束纹理位图的大小为2的幂
  - `Normal maps`
  - `Gloss maps`
  - `Environment maps`
