# Graphics

## Depth-Buffered Triangle Rasterization

### Describing a Scene

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

### Describing the Visual Properties of a Surface

- `Light-Object Interactions` 光在可以被吸收、反射（漫反射和镜面反射）、衍射（一般不考虑）、散射、折射
- `Color Model` 用三元组或四元组表示色彩的数学模型
- `Color Space` 色彩模型所能定义的色彩范围即色彩空间、色域
- `Vertex Attributes` 顶点属性可以用来描述物体表面的视觉特性
  - `Position vector` 顶点在模型空间的位置
  - `Vertex normal` 顶点处的单位表面法线
  - `Vertex tangent and bitangent` 与顶点法线一起两两垂直，构成一个坐标系 `tangent space`（切线空间），用于逐像素照明计算
  - `Diffuse color` 漫反射色
  - `Specular color` 高光反射色
  - `Texture coordinates` 纹理坐标，用于将二维或三维位图映射到网格表面上（`texture mapping`），描述顶点在纹理的二维归一化坐标空间（`texture space`）中的位置，左下角（0，0），右上角（1，1）
  - `Skinning weights` 蒙皮权重,一个顶点受连接骨骼的多个关节影响
- `Vertext Formats` 保存的顶点属性的数据结构的布局
- `Attribute Interpolation` 渲染三角形基于逐像素而不是逐顶点,所以需要对顶点属性进行插值得到内部点的属性
- `Gouraud shading` 高洛德着色，对顶点的色彩属性进行插值
- `Textures` 纹理，包含色彩信息和其他信息，可以映射到网格的三角形上；一些图形硬件约束纹理位图的大小为2的幂
  - `Diffuse map` 漫反射贴图，描述物体表面每个纹素上的漫反射表面色彩
  - `Albedo map` 无光颜色贴图，同漫反射贴图
  - `Normal map` 法线贴图，保存每个纹素的单位法线，RGB编码
  - `Gloss map` 光泽贴图，保存每个纹素的亮度
  - `Environment map` 环境贴图，保存一张周围环境的图片，用于渲染反射
- `Texture Addressing Modes` 纹理寻址模式，处理超出范围的纹理坐标的方法
  - `Wrap` 纹理直接重复
  - `Mirror` 纹理翻转后重复，相邻纹理镜像对称
  - `Clamp` 超过范围的纹素颜色为纹理边界颜色
  - `Border color` 超过范围的纹素颜色由用户定义
- `Texture Formats` 保存在磁盘上：tga/png/bmp/tif；保存在内存中：RGB888/RGBA8888/RGB565/RGBA5551；压缩纹理：DXT/S3TC
- `Texel Density` 纹素与像素的比例称为纹素密度；纹素密度小于1可能会看到纹素的边，纹素密度大于1可能会造成不必要的内存消耗和镜头微调时像素点的闪烁；纹素密度为1时最佳
- `Mipmapping` 多层次纹理贴图，与 `LOD` 多层次模型相似；创建多个纹理级别，每个级别纹理的宽和高都是的上一个级别的一半，图形硬件选择其中一个或相邻的两个级别用于纹理映射，使得纹素密度尽可能接近1
- `World-Space Texel Density` 纹素与自然空间物体表面积的比例；不需要接近1，但是所有物体需要一致
- `Texture Filtering` 渲染三角形的一个像素时，图形硬件通过像素中心在纹理空间的落点，采样多个纹素并混合结果色作为实际采样的纹素颜色，这个过程叫纹理过滤
  - `Nearest neighbor` 选择最接近像素中心的纹素；选择纹素密度大于等于1且最接近1的 `mip level`
  - `Bilinear` 选择像素中心周围的4个纹素，混合权重基于纹素中心与像素中心的距离；选择纹素密度最接近1的 `mip level`
  - `Trilinear` 选择纹素密度分别大于等于1和小于等于1且最接近1的2个 `mip level`，依次 `Bilinear` 并将结果进行线性插值
  - `Anisotropic` 各向异性过滤；视角变化导致物体表面倾斜时，在一个梯形（或矩形、平行四边形）区域内进行纹素采样，提高该场景下的纹理质量；与 `Isotropy`（各向同性，包括双线性和三线性过滤） 相对
- `Materials` 材质，包含纹理和其他高级别特性，例如着色器程序、着色器程序入参、其他控制图形加速硬件功能的参数
  - `Mesh-material pairs` 包含渲染一个物体所需的所有信息，因此有时也称为 `render packets`，`geometric primitive`（几何图元）的概念有时也扩展包含 `Mesh-material pairs`
  - `Submeshes` 一个 3D 模型通常使用多个材质，因此网格经常划分为子网格，分别对应单个材质

### Lighting Basics

- `Shading` 着色处理，光线加其他视觉效果的广泛概况
- `Light transport models` 渲染引擎使用的光线-表面和光线-体交互的数学模型
  - `Direct lighting` 直接光，从光源发出、在物体表面反弹一次后射到虚拟镜头的光照
  - `Indirect lighting` 间接光，从光源发出、在多个表面反弹多次后射到虚拟镜头的光照
  - `Local illumination models` 局部光照模型，只考虑直接光，一个物体不会影响另一个物体的外观
  - `Global illumination models` 全局光照模型，考虑直接光和间接光，一些全局光照模型旨在模拟特殊的视觉现象，如逼真的阴影、反射面、物体间光线相互反射、水面或闪亮金属表面强烈反射的光学焦散效果；另一些则模拟广泛的光学现象，如 `Ray tracing`（光线追踪）和 `radiosity methods`（辐射度方法）技术
- `Rendering equation` / `Shading equation` 描述全局光照的数学公式，由 J. T. Kajiya 在一个意义重大的 `SIGGRAPH`（计算机图形学专业组）论文中提出 ；每个渲染技术都可以看作是渲染方程的完整或部分解决方案
- `The Phong Lighting Model` 冯氏光照模型，最常采用的局部光照模型
  - `Ambient` 环境光，对场景整体光照水平进行建模，是场景中间接光的总体效果的近似
  - `Diffuse` 漫反射光，光源照射到粗糙物体表面然后向各个方向反射
  - `Specular` 高光，光源照射到光滑物体表面然后反射到镜头
- 冯氏光照模型的入参
  
  参数 | 解释
  :-|:-
  $V\ =\ [V_x\ V_y\ V_z]$ | 观察方向向量，从反射点到镜头焦点
  $A\ =\ [A_R\ A_G\ A_B]$ | 环境光三个颜色通道上的强度
  $N\ =\ [N_x\ N_y\ N_z]$ | 反射点的法线
  $K_A\ =\ [K_{AR}\ K_{AG}\ K_{AB}]$ | 表面反射特性：环境光反射率
  $K_D\ =\ [K_{DR}\ K_{DG}\ K_{DB}]$ | 表面反射特性：漫反射率
  $K_S\ =\ [K_{SR}\ K_{SG}\ K_{SB}]$ | 表面反射特性：镜面反射率
  $\alpha$ | 表面反射特性：镜面光泽度指数
  $C_i\ =\ [C_{iR}\ C_{iG}\ C_{iB}]$ | 每个光源的颜色和强度
  $L_i$ | 反射点到每个光源的方向向量

- 冯氏光照模型一个点反射光强度 $I$ 的计算
  - $I\ =\ (K_A\bigotimes A)+\sum_i[K_D(N\cdot L_i)+K_S(R_i\cdot V)^\alpha]\bigotimes C_i$
  - $R$ 与 $L$ 和 $N$ 的关系如下图，计算公式为 $R\ =\ 2L_N-L$ 或 $R\ =\ 2(N\cdot L)N-L_T$ 
  
    ![LNR](Images/LNR.PNG)

- `Blinn-Phong` 冯氏模型的变种（高光反射计算不同），精度更低（尽管对于某些表面比冯氏模型更贴近实际效果），运行时效率更高，几乎只用于早期电脑游戏和硬连线到早期GPU的固定功能流水线。
  - 高光部分为 $(N\cdot H)^\alpha$，与冯氏模型的 $(R\cdot V)^\alpha$ 对应，其中 $H$ 在 $V$ 和 $L$ 的中间
- `BRDF Plots` / `bidirectional reflection distribution function` 双向反射分布函数，一种通用本地反射模型，计算延观察方向 $V$ 反射的辐射亮度 `radiance` 与延入射光线 $L$ 入射的辐射照度 `irradiance` 的比例；一个 `BDRF` 可以看做一个半球图（`plot`），离球心越远的点可以看到的光的强度越低，冯氏模型中漫反射光和高光的半球图如下

  ![DiffuseSpecular](Images/DiffuseSpecular.PNG)

- `Modeling Light Sources` 对真实世界的光源进行建模
  - `Static Lighting`
  - `Ambient Lights`
  - `Directional Lights`
  - `Point (Omnidirectional) Lights`
  - `Spot Lights`
  - `Area Lights`
  - `Emissive Objects`