# Collision and Rigid Body Dynamics

## Do You Want Physics in Your Game

- `collision detection system` 碰撞检测系统，游戏引擎的核心组件之一，确保物体不会相互穿过，通常与物理引擎（`physics engine`）紧密集成
- `Rigid body dynamics simulation` 刚体动态模拟。物理学领域是巨大的，现在大多数游戏引擎称呼为“物理学（`physics`）”的东西描述为刚体动态模拟更准确

### Things You Can Do with a Physics System

- 检测动态物体和静态世界几何之间的碰撞
- 模拟受重力和其他力影响的自由刚体
- `Spring-mass systems` 弹簧质点系统
- 可破坏的建筑物和结构
- 射线和形状（用于确定视线、子弹撞击等）
- `Trigger volumes` 触发体（决定物体何时进入、离开或位于游戏中的预定义区域）
- 复杂的机器（起重机、移动平台拼图等）
- 陷阱（如崩落的巨石）
- 具有逼真悬架的可驾驶车辆
- 布娃娃角色死亡
- `Powered rag doll` 传统动画和布娃娃物理的现实融合
- 悬垂的道具（水壶、项链、剑），半真实的头发，衣服的动作
- 布料模拟
- 水面模拟和浮力
- 音频传播
- ...
- 物理模拟可以运行时执行或作为生成动画片段的离线预处理步骤执行，例如 Maya 中有大量可用的物理插件

### Is Physics Fun

- `Simulations (Sims)` 模拟游戏的主要目标是重现真实的体验，例如飞行模拟器、GT 赛车、纳斯卡赛车系列游戏，刚体动力学系统提供的真实性非常适合这类游戏
- `Physics Puzzle Games` 物理益智游戏的主要想法是让用户玩动态模拟玩具，这种游戏的核心机制几乎完全依赖于物理学，例如 Bridge Builder、不可思议的机器、奇妙装置、蜡笔物理学
- `Sandbox Games` 沙盒游戏中，可能根本没有目标，或者可能存在大量可选目标，玩家的主要目标通常是“乱搞”和探索游戏世界中的物体可以做什么，例如侠盗猎车手 5、孢子、小小大星球 2、撕纸小邮差、我的世界。沙盒游戏可以很好地使用真实动态模拟，尤其是如果许多乐趣来源于游戏世界中物体间的真实或半真实交互。有时动态模拟可能通过各种方式微调以实现正确的感觉或增加乐趣因素，如比真实大的爆炸，比正常更强或更弱的重力
- `Goal-Based and Story-Driven Games` 基于目标的游戏有规则和玩家必须完成以推动进度的特定目标，故事驱动的游戏中讲故事至关重要。将物理系统集成到这类游戏中十分棘手。我们通常会放弃控制以换取逼真的模拟，这种失控会抑制玩家完成目标和游戏讲述故事的能力。开发者必须小心应用物理并采取措施以各种方式控制模拟的行为，以确保它不阻碍游戏玩法。为玩家提供一个摆脱困境的方法通常也是个好主意，例如光晕系列游戏玩家可以按 X 翻转颠倒的车辆

### Impact of Physics on a Game

- `Design Impacts` 设计影响
  - `Predictability` 可预测性。将物理模拟行为从动画行为中分离的固有的混乱和多变性是不可预测性的一个来源。如果某些事情总是以具体的方式发生，通常最好使用动画，而不是尝试强迫动态模拟来产生可靠运动
  - `Tuning and control` 调整和控制。物理定律（准确建模时）是固定的，游戏中可以调整重力值或刚体恢复系数，但是调整物理参数的结果通常是间接的，难以可视化。调整力让角色向所需方向移动比调整角色行走动画难得多
  - `Emergent behaviors` 应急行为。有时物理会在游戏中引入意想不到的功能，例如军团要塞中的火箭发射器跳跃技巧，光晕中的高空爆炸疣猪，心理战中的飞行的“冲浪板”
- `Engineering Impacts` 工程影响
  - `Tools pipeline` 工具流水线。良好的碰撞/物理流水线需要时间来构建和维护
  - `User interface` 用户接口。玩家如何控制物理物体，如 Trespasser 中使用虚拟手臂握住他们，半条命 2 中使用“重力枪”
  - `Collision detection` 碰撞检测。旨在动态模拟中使用的碰撞模型比非物理驱动的可能要更详细和更精心的构造
  - `AI` 人工智能。在存在物理模拟物体的情况下，可能无法预测路径，引擎可能需要处理可以移动或爆炸的动态覆盖点
  - `Misbehaved objects` 行为不端的物体。物体由动态模拟驱动时，可能由意想不到的方式相互反弹或抖动严重。可能需要应用碰撞过滤允许物体稍微互相穿透，需要建立机制确保物体稳定并适当进入睡眠状态
  - `Rag doll physics` 布娃娃物理。布娃娃需要大量微调且通常受到在模拟中不稳定的影响。动画可能驱动角色身体的部分穿透到其他碰撞体，当角色变为布娃娃时，这些互相穿透可能造成巨大的不稳定性。需要采取措施避免这个问题
  - `Graphics` 物理学。物理驱动的运动可能影响可渲染物体的包围体（否则它们将是静态的或更可预测的），可破坏建筑和物体的存在可能使某些预计算照明和阴影方法无效
  - `Networking and multiplayer` 网络和多人游戏。不影响游戏玩法的物理效果可以独立地只在每个客户端机器上模拟，影响游戏玩法的物理效果必须在服务器上模拟并准确复制到所有客户端
  - `Record and playback` 记录和回放。记录游戏并在以后播放它，作为调试和测试辅助手段十分有用，还可以作为一个有趣的游戏功能。这个功能要求每个引擎系统以确定的方式运行
- `Art Impacts` 美术影响
  - `Additional tool and workflow complexity` 额外的工具和工作流复杂度。装配具有质量、摩擦力、约束和其他用于动态模拟的属性的物体的需求使得美术部门的工作也更加困难
  - `More-complex content` 更复杂的内容。可能需要为物体提供多个视觉相同但具有不同碰撞和动态配置的版本，例如一个原始版本和一个可破坏版本
  - `Loss of control` 失控。物理驱动物体的不可预测性使控制场景的美术构成更加困难
- `Other Impacts` 其他影响
  - `Interdisciplinary impacts` 跨学科影响。在游戏中引入动态模拟需要工程、美术、音频和设计之间的紧密合作
  - `Production impacts` 生产影响。物理学会增加项目的开发成本、技术和组织复杂度以及风险

## Collision/Physics Middleware

### I-Collide, SWIFT, V-Collide and RAPID

- `I-Collide` 是 UNC 开发的开源碰撞检测库，它可以检测凸（`convex`）体间的交叉。I-Collide 已经被更快、功能更丰富的库 `SWIFT` 取代。UNC 还开发了可以处理复杂非凸形状的碰撞检测库 `V-Collide` 和 `RAPID`。这些库无法正确地开箱即用，但是它们可以为构建功能齐全的游戏碰撞检测引擎提供良好的基础

### ODE

- `ODE` 代表“开放动力学引擎（`Open Dynamics Engine`）”，是开源的碰撞和刚体动力学 SDK。其功能集与商业产品 Havok 相近。好处是免费和完整代码可用

### Bullet

- `Bullet` 是开源的碰撞检测和物理库，用于游戏和电影行业。它的碰撞引擎集成到了它的动态模拟中，但是提供了钩子使碰撞系统可以单独使用或集成到其他物理引擎中。它支持连续碰撞检测（`continuous collision detection / CCD`），也称为碰撞时间（`time of impact / TOI`）碰撞检测，当模拟包含小且快速移动的物体时非常有帮助

### TrueAxis

- 碰撞/物理 SDK，可以免费用于非商业用途

### PhysX

- `PhysX` 最开始是由 Ageia 制作和发行的称为 Novodex 的库，作为推出他们的专用物理协处理器的战略的一部分，后被 NVIDIA 购买和改良，使它能使用 NVIDIA 的 GPU 作为协处理器运行。Ageia 和 NVIDIA 的部分营销策略是完全免费提供 SDK 的 CPU 版本，以推动物理协处理器市场向前发展。开发者可以付费获取完全源代码和根据需要定制库的能力。PhysX 现在与 APEX 绑定，APEX 是 NVIDIA 的可扩展多平台动态框架。PhysX / APEX 可用于 Windows、Linux、Mac、Android、Xbox 360、PlayStation 3、Xbox One、PlayStation 4 和 Wii

### Havok

- `Havok` 是商业物理 SDK 的黄金标准，提供了最丰富的功能集之一，并在所有支持平台上具有出色的性能特征。Havok 由一个核心碰撞/物理引擎和一些可选附加产品，包括车辆物理系统、用于建模可破坏环境的系统、一个直接集成到 Havok 布娃娃物理系统的功能齐全的动画 SDK 组成。可用于 Xbox 360、PlayStation 3、Xbox One、PlayStation 4、PlayStation Vita、Wii、Wii U、Windows 8、Android、Apple Mac 和 iOS

### Physics Abstraction Layer (PAL)

- 开源库，允许开发者在一个项目上使用多个物理 SDK，为 PhysX（Novodex）、Newton、ODE、OpenTissue、Tokamak、TrueAxis 和其他一些 SDK 提供钩子

### Digital Molecular Matter (DMM)

- Pixelux Entertainment S.A. 制作的一个独特物理引擎，使用有限元方法来模拟可变形体和易碎物体的动力学。该引擎同时具有离线和运行时组件。可以在 LucasArts 的星球大战：原力释放中看到
