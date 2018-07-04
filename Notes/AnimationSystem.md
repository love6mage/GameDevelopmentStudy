# Animation System

## Types of Character Animation

### Cel Animation

- `traditional animation` / `hand-drawn animation` 传统动画 / 手绘动画，所有游戏动画技术的先导，用于最早的动画片，通过快速连续地显示称为帧（`frame`）的一系列静止图像来产生运动的错觉。实时 3D 渲染可以被认为是传统动画的电子格式
- `Cel Animation` Cel 动画，一种特殊的传统动画。一个 Cel 是一个可以在其上绘制图像的塑料透明薄片。可以将动画的 Cel 序列放在固定的背景之上以产生运动的幻觉，而不必反复重绘静态背景
- `Sprite Animation` 精灵动画，Cel 动画的电子格式。一个精灵是一个可以覆盖在全屏背景图像上且不破坏它的小位图，通常由专门的图形硬件协助绘制。所以，精灵之于 2D 游戏动画等同 Cel 之于传统动画
- `looping animation` 循环动画，帧序列被设计为即使在无限重复时也能平滑地动画化。人物通常具有许多循环动画周期，包括各种空闲周期（`idle cycles`）、一个步行周期（`walk cycle`）、一个奔跑周期（`run cycle`）

### Rigid Hierarchical Animation

- 随着 3D 图形的出现，精灵技术开始失去吸引力。*毁灭战士*使用了一个类精灵动画系统：它的怪物只是面向相机的四边形，每个四边形显示一系列纹理位图（`animated texture`）来产生运动幻觉。这个技术至今仍用于低分辨率和遥远的物体
- `Rigid hierarchical animation` 刚体分层动画，3D 人物动画最早的方法。这个方法中，人物使用一个刚体件集合建模，这些刚体件以分层方式彼此约束，类似于哺乳动物的骨骼通过关节连接。该方法最大的问题是关节处的开裂问题，对于机器人和机械效果很好，但不适用与鲜活丰满的人物。一个典型的分层结构如下
  - 骨盆
    - 躯干
      - 上右臂
        - 下右臂
          - 右手
      - 上左臂
        - 下左臂
          - 左手
    - 上右腿
      - 下右腿
        - 右脚
    - 上左腿
      - 下左腿
        - 左脚

### Per-Vertex Animation and Morph Targets

- `Per-vertex animation` 每顶点动画，网格顶点由美术师动画化，运动数据导出用于描述运行时顶点如何运动。这个技术可以产生任何可以想象的网格变形，但是一个数据密集型技术，要为每个顶点保存时变运动信息，所以很少应用于实时游戏
- `Morph target animation` 变形目标动画，每顶点动画的变体。网格顶点由动画师移动，以创建一组相对较小的固定极端姿势。运行时可以混合两个或多个这些固定的姿势产生动画。每个顶点的位置可以使用每个极端姿势中顶点位置间的简单线性插值来计算，常用于面部动画
  - 一些工作室使用包含数百个关节的关节面部装备替换变形目标；另一些工作室则结合这两种技术，使用关节装备实现面部的主要姿势，然后通过变形目标应用微调

### Skinned Animation

- `skinned / skeletal animation` 蒙皮 / 骨骼动画，像刚体分层动画一样由刚性的“骨头”构造一个骨架（`skeleton`），但是不会直接渲染这些刚体件，而是让他们保持隐藏，在其上覆盖平滑连续的三角形网格，这些网格称为蒙皮（`skin`），与骨架的关节绑定，其顶点跟踪关节的运动。蒙皮网格的每个顶点可以加权到多个关节，所以当关节移动时，蒙皮可以以自然的方式伸展
- 骨骼动画可以像每顶点动画一样允许网格三角形变形，还享有刚体分层动画的更高的性能和内存使用特性，首先用于*超级马里奥 64*，至今仍然被游戏产业和故事片行业广泛使用。众多著名的现代游戏和电影角色全部或部分使用了蒙皮动画技术进行动画制作，包括*侏罗纪公园*中的恐龙，*合金装备4*中的 Solid Snake，*魔戒*中的咕噜，*神秘海域*中的 Nathan Drake，*玩具总动员*中的巴斯光年，*战争机器*中的 Marcus Fenix，*最后生还者*中的 Joel

### Animation Methods as Data Compression Techniques

- 最灵活的动画系统可以让动画师控制对象表面上每个无穷小的点，当然，像这样的动画会导致动画可能包含无限量的数据。每顶点动画、变形目标动画、蒙皮动画实际上可以看做是对这些无限量数据的压缩。我们通常的目标就是选择一个不会产生不可接受的视觉伪影的、提供最佳压缩的动画方法。
- 人物四肢大多数情况下像刚体一样随骨骼高效移动，这种情况下骨骼动画提供了最佳压缩；但是面部运动更加复杂，顶点的运动更独立，如果使用骨骼动画令人信服地动画化面部，则关节的数量趋近于顶点的数量，这减弱了其作为压缩算法的有效性。面部的动画使用变形目标压缩效果更好，因为顶点被约束为仅沿固定数量的预定义顶点位置之间的线性路径移动，而且变形目标往往是动画师工作的一种更自然的方式

## Skeletons

- 骨骼由称为关节（`joints`）的刚性件的层次结构组成。在游戏行业中，我们经常互换地使用术语关节和骨头（`bone`），但术语骨头实际上用词不当。从技术上讲，关节是动画师直接操作的物体，而骨头只是关节间的空白空间，游戏引擎不关心骨头，只有关节很重要。所以行业中使用“骨头”一词实际上 99% 是指关节。

### The Skeleal Hierarchy

- 骨骼动画的骨骼层次结构由关节组成，与典型的刚体层次结构看起来几乎相同
- 一个人形角色的关节层次结构可能如下，每个关节只有一个父亲，所以可以通过存储每个关节的父亲索引完全描述这个层级结构
  - 骨盆
    - 下脊椎
      - 中脊椎
        - 上脊椎
          - 右肩
            - 右肘
              - 右手
                - 右拇指
                - 右食指
                - 右中指
                - 右无名指
                - 右小指
          - 左肩
            - 左肘
              - 左手
                - 左拇指
                - 左食指
                - 左中指
                - 左无名指
                - 左小指
          - 颈部
            - 头
              - 左眼
              - 右眼
              - 各种面关节
    - 右大腿
      - 右膝盖
        - 右脚踝
    - 左大腿
      - 左膝盖
        - 左脚踝

### Representing a Skeleton in Memory

- 通常由一个小的顶部数据结构表示，该结构包含各个关节的数据结构数据
- 关节通常按顺序列出，以确保子关节始终出现在数组中的父关节之后，所以关节 0 总是骨架的根
- `Joint indices` 关节索引，通常用于指向动画数据结构中的关节。该方法在存储方面和查询一个关节的速度方面更高效
- 每个关节数据结构通常包含的信息
  - 关节名，一个字符串或一个散列 32 位字符串 ID
  - 父关节的索引
  - `inverse bind pose transform` 逆绑定姿势变换。关节的绑定姿势是该关节绑定到蒙皮网格顶点时的位置、方向、缩放
- 一个典型的骨架数据结构

  ```C
  struct Joint
  {
    Matrix4x3 m_invBindPose; // inverse bind pose transform
    const char* m_name; // human-readable joint name
    U8 m_iParent; // parent index or 0xFF if root
  };
  struct Skeleton
  {
    U32 m_jointCount; // number of joints
    Joint* m_aJoint; // array of joints
  };
  ```

## Poses

- 姿势是动画师给人物注入生命的主要工具，动画就是将角色的身体排列成一系列离散的静止姿势然后快速连续显示（通常在相邻姿势间线性插值而不是逐个显示姿势）
- 关节的姿势由关节的位置、方向和缩放定义，相对于某些参考帧。关节姿势通常用 $4\times 4$ 或 $4\times 3$ 矩阵或 SQT 数据结构（scale, quaternion rotation and vector translation）表示。骨架的姿势是关节姿势的集合，用一个简单的矩阵或 SQT 的数组表示

### Bind Pose

- `Bind pose` / `Reference pose` / `Rest pose` 绑定姿势，绑定到骨架前的 3D 网格的姿势。也称为 T 姿势（`T-pose`），因为人物通常站立时双脚略微分开，双臂伸出，形成字母 T 的形状

### Local Poses

- 关节的姿势通常相对于其父关节定义，我们通常使用术语局部姿势来描述相对于父关节的姿势。局部姿势大多数保存为 SQT 格式
- `Joint space` 关节空间，一个关节定义的坐标空间
- 一个关节姿势只不过是一个仿射变换，关节 $j$ 的姿势可以写成 $4\times 4$ 的仿射变换矩阵 $P_j$，由平移矢量 $T_j$，$3\times 3$ 的对角缩放矩阵 $S_j$，$3\times 3$ 的旋转矩阵 $R_j$ 组成。整个骨架的姿势 $P^{skel}$ 可以写成所有姿势 $P_j$ 的集合
  
  $$
  P_j=\begin{bmatrix}
    S_jR_j & 0 \\
    T_j & 1
  \end{bmatrix},\quad
  P^{skel}=\{P_j\}|_{j=0}^{N-1}
  $$

- `Joint Scale` 关节缩放
  - 有些引擎假设关节不会被缩放，所以 $S_j$ 被简单地省略并假设为单位矩阵
  - 有些引擎假设关节只会被均匀缩放（`uniform scale`），所以缩放可以用单个标量 $s_j$ 表示
  - 有些引擎允许非均匀缩放（`nonuniform scale`），所以缩放可以紧凑表示为三元向量 $s_j=\begin{bmatrix} s_{jx} & s_{jy} & s_{jz}\end{bmatrix}$。它对应 $S_j$ 的三个对角线元素，所以本身并不真的是一个向量
  - 几乎所有引擎不允许切变，因为切变需要一个完整的 $3\times 3$ 切变矩阵表示
  - 对缩放的省略或约束减少了内存使用，简化了数学逻辑和引擎处理
- `Representing a Joint Pose in Memory` 关节姿势在内存中的表示，通常以 SQT 格式保存，在 C++ 中的数据结构可能如下，S 被放在最后以优化结构体包装

  ```C++
  struct JointPose
  {
    Quaternion m_rot; // Q
    Vector3 m_trans; // T
    F32 m_scale; // S (uniform scale only)
    // Vector4 m_scale; // S (nonuniform scale is permitted)
  };
  struct SkeletonPose
  {
    Skeleton* m_pSkeleton; // skeleton + num joints
    JointPose* m_aLocalPose; // local joint poses
  };
  ```

- `The Joint Pose as a Change of Basis` 任何仿射变换可以认为是点或向量从一个坐标空间向另一个坐标空间的变换。关节姿势变换 $P_j$ 应用于表示在关节 j 空间中的点或向量，结果是表示在父关节空间中的该点或向量，所以关节 j 的局部姿势可以写作 $P_{j\to p(j)}$，其中 $p(j)$ 返回关节 j 的父关节索引

### Global Poses

- 有时在模型或自然空间中表示关节姿势十分方便，这称为全局姿势。关节的全局姿势可以这样计算：在骨架中从当前关节到根关节进行遍历，将路径上所有关节的局部姿势串联相乘

  
  $$
  P_{j\to M}=\prod_{i=j}^0P_{i\to p(i)}
  $$

- `Representing a Global Pose in Memory` 全局姿势在内存中的表示，扩展 `SkeletonPose` 结构体，使其包含全局姿势

  ```C++
  struct SkeletonPose
  {
    Skeleton* m_pSkeleton; // skeleton + num joints
    JointPose* m_aLocalPose; // local joint poses
    Matrix44* m_aGlobalPose; // global joint poses
  };
  ```

## Clips
