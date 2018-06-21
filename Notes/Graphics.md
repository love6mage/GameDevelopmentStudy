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
  - `Static Lighting` 提前计算好网格每个顶点的冯氏反射保存为顶点属性，或者提前计算每个像素的光照保存在 `light map`中
    - `Light map` 光照贴图，一种纹理贴图；每个光源生成一个光照贴图，运用在光源影响区域内的所有物体上；可以使经过光源的动态物体被正确照亮；分辨率一般比漫反射纹理贴图低；纯的光照贴图比包含漫反射色信息的光照贴图更好压缩
  - `Ambient Lights` 环境光，与视角无关且没有方向，所以只用一个颜色建模，强度和颜色在游戏世界的不同区域不同
  - `Directional Lights` 平行光，太阳光，光源离物体无限远，所以用颜色 $C$ 和方向 $L$ 建模
  - `Point (Omnidirectional) Lights` 点光源，在特定位置向所有方向发射光线；用光源位置 $P$，光源颜色和强度 $C$，最大半径 $r_{max}$建模；点光源不影响最大半径外的物体
  - `Spot Lights` 聚光灯，手电筒光源，光线限制在一个圆锥体区域内的点光源；用光源位置 $P$，光源颜色 $C$，中心方向 $L$，最大半径 $r_{max}$，内圆锥偏角 $\theta_{min}$ 和外圆锥偏角 $\theta_{max}$ 建模；内圆锥光线强度最大，内圆锥外、外圆锥内光线强度随偏角增大而减小直到 0
  - `Area Lights` 真实的光源是有区域的，可以投射出本影（`umbra`）和半影（`penumbra`）。渲染引擎一般不对这种光源进行建模，而是采用其他方法模拟区域光源的效果，例如混合多个阴影或者模糊阴影边界来模拟半影
  - `Emissive Objects` 自发光物体，使用自发光纹理贴图建模
    - `Emissive texture map` 自发光纹理贴图，纹理颜色的强度不受周围光线影响，始终最大
- `Flashlight - Effect Combination` 手电筒是多种光源效果组合的简单实例
  - 镜头正对手电筒：自发光物体
  - 手电筒发射光线到场景中：聚光灯
  - 手电筒的光锥：黄色半透明网格
  - 眩光：镜头前的透明卡片
  - `bloom` 泛光效果：`high dynamic range light` 高动态范围图像
  - 焦散效果：投射纹理

### The Virtual Camera

- `The Virtual Camera` 焦点加焦点前的矩形虚拟感应面 / 成像矩阵 `imaging rectangle`；成像矩阵包含方形或矩形虚拟光线感应器，每个感应器对应屏幕上的一个像素。渲染就是决定这些虚拟感应器要记录的光线的颜色和强度的过程
- `View Space`/ `Camera Space` 虚拟摄像头的焦点是观察空间的原点，且一般摄像头正对 z 轴正或负方向，上对 y 轴正方向，左或右对 x 轴正方向；渲染网格时，网格顶点需要从模型空间变换到自然空间再到观察空间
- `Projections` 投影，将 3D 的场景变换到 2D 的图像平面。
  - `perspective` 透视投影，有 `perspective foreshortening`（透视收缩，物体离镜头越远看起来越小）的效果
  - `orthographic` 等距正交投影，渲染 3D 模型的平面图或者 2D 图形
- `View volume` 视体，可视空间，镜头可以看到的区域。由 6 个平面定义
  - `Near plane` 近平面，即成像矩阵
  - `Four side planes` 虚拟屏幕的边
  - `Far plane` 远平面，用于渲染优化，保证极远的物体不被绘制
  - `Frustum` 平截头体，透视投影渲染时的视体；正交投影时视体为长方柱
- `Homogeneous Clip Space` 透视投影和正交投影都是将点从观察空间变换到齐次裁剪空间中（从观察空间视体到 `Canonical View Volume` 正规化视体）
  - 该空间独立于采用的投影类型、屏幕的分辨率和宽高比（`aspect ratio`）
  - 一般为左手坐标系（`left-handed`）
  - 正规化视体为长方柱，便于对三角形进行裁剪
  - x 轴和 y 轴坐标范围为 $[-1,1]$，z 轴坐标范围为 $[-1,1]$（OpenGL）或 $[0,1]$（DirectX）
  - 齐次向量转换为三维坐标时 x/y/z 分量都要除以 w 分量，透视投影时变换的齐次向量的 w 分量是 z 坐标乘一个常数，所以齐次裁剪空间的坐标都除以了 z 坐标，造成透视收缩的效果
- `Perspective-Correct Vertex Attribute Interpolation` 透视校正插值。顶点属性插值是在屏幕空间（`Screen space`）执行的，透视投影时的顶点属性插值需要考虑到透视收缩效果。
- `Screen space` 屏幕空间是以屏幕像素为单位长度的二维坐标，一般原点在屏幕左上角，x 轴向右，y 轴向下（CRT 从上到下扫描屏幕）；渲染三角形时可以直接使用齐次裁剪空间中的 x/y 坐标，只需要对 x/y 进行简单的缩放和平移操作（该操作称为 `Screen mapping`）
- `The frame buffer` 帧缓冲，位图颜色缓冲，保存最终被渲染的图像，渲染引擎一般维护至少两个帧缓冲
  - `double buffering` 双重缓冲技术，缓冲 A 正在被 CRT 扫描时缓冲 B 可以被引擎更新；两个缓冲在 `vertical blanking interval`（垂直回扫期）内交换，可以保证 CRT 每次扫描的都是完整的缓冲，避免 `tearing`（撕裂效果）的产生
  - `triple buffering` 三重缓冲技术，缓冲 A 正在被 CRT 扫描，B 已经被引擎更新完成时，C 可以被接着更新而不需要等待 A 扫描完成
  - `Render Targets` 渲染目标，引擎在其中绘制图形的缓冲；渲染引擎使用各种离屏渲染目标来保存渲染结果
- `Triangle Rasterization and Fragments` 栅格化是填充三角形覆盖的像素的过程，即将矢量图转化为位图。该过程中，三角形表面被分成多个分段，每个分段对应屏幕上的一个像素（`multisample antialiasing` 场景一个分段对应一个像素的一部分）。分段写入帧缓冲的时候需要通过一些测试，测试失败分段被丢弃，测试成功分段被写入缓冲或与缓冲中像素已有颜色混合
- `Occlusion and the Depth Buffer` 渲染引擎需要保证重叠的三角形能正确显示，单纯像画家一样从后到前渲染无法解决两个三角形穿插在一起的场景。渲染引擎使用深度缓冲（`depth buffering`）或 z 缓冲（`z-buffering`）实现三角形的正确遮挡，不依赖渲染顺序。每个分段有一个 z 坐标表示离屏深度，分段的颜色写入帧缓冲时深度也保存到深度缓冲中；其他分段写入相同像素时，引擎会比较两个分段的深度，选择深度小离屏幕近的分段保存
- `z-Fighting and the w-Buffer` 深度缓冲精度是有限的，当两个平面无限靠近时依靠深度缓冲无法确定前后，导致后平面一些像素被绘制到前平面上，产生的图像噪音效果叫 `z-fighting`；使用裁剪空间的 z 分量作为深度导致离屏远的平面深度精度比离屏近的平面深度精度低，因为观察空间 z 坐标和裁剪空间的 z 分量是倒数关系；使用观察空间 z 坐标或裁剪空间 w 分量作为深度可以使离屏远近不同的平面有相同的深度精度，这个技术叫 w 缓冲（`w-buffering`）；w 缓冲无法直接对深度进行线性插值，所以比 z 缓冲更慢

## The Rendering Pipeline

- `Pipeline` 多个计算阶段（`Stage`）的有序链，每个阶段是独立的单元，有特定目标、操作输入数据流、产生输出数据流，前面阶段的输出作为后面阶段的输入。阶段的独立性赋予流水线结构优秀的平行处理能力，链中每个阶段可以同时处理数据、每个阶段可以同时处理多条数据。
  - `Throughput` 吞吐量，流水线的吞吐量计量流水线每秒完整处理的数据量
  - `Latency` 时延，流水线的时延计量流水线完整处理一条数据的时间；阶段的时延计量阶段处理一条数据的时间；时延最大的阶段决定了流水线的吞吐量，是流水线吞吐量的瓶颈
- `High-level stages in pipeline` 高层次的阶段划分
  - `Tools stage (offline)` 离线工具阶段，定义几何图形和材质
  - `Asset conditioning stage (offline)` 离线资源调节阶段，资源调节流水线（`Asset conditioning pipeline` / `ACP`）将几何图形和材质处理为引擎可用的格式
  - `Application stage (CPU)` CPU应用阶段，CPU 识别出可视网格实例，将网格实例和材质提交给图形硬件进行渲染
  - `Geometry processing stage (GPU)` GPU 几何处理阶段，GPU 将顶点变换、照亮、投影到齐次裁剪空间；可选的几何着色器处理三角形并以视锥体（`View frustum`）裁剪三角形
  - `Rasterization stage (GPU)` GPU 光栅化阶段，GPU 将三角形转换为着色且通过不同测试（z-test, alpha test, stencil test, etc.）的分段，最终混合存入帧缓冲
- 每个阶段的数据转换
  - 工具阶段：处理网格和材质
  - CPU 阶段：处理网格实例和子网格
  - GPU 阶段
    - 几何处理阶段：子网格分成大量顶点平行处理，最后再将完整变换和着色的顶点重构成三角形
    - 光栅化阶段：三角形分成分段，这些分段要么丢弃，要么作为颜色信息存入帧缓冲
- `The Tools stage` 工具阶段
  - `Digital content creation` / `DCC` 数据内容创造；`DCC` 应用程序有 Maya, 3ds Max, Lightwave, Softimage/XSI, SketchUp, etc. 网格是由这些应用程序建模产生的，用 NURBS，四边形或三角形等定义