# Audio

## The Physics of Sound

- 声音是在空气中传播的压缩波，声波振幅（`amplitude`）的单位为压强（`pressure`），SI 单位中压强的单位为帕斯卡（`Pascals / Pa`），$1Pa=1N/m^2=1kg/(m\cdot s^2)$
- `instantaneous acoustic pressure` 瞬间声压，为环境大气压加特定时刻声波引起的扰动

  $$
  p_{inst}=p_{atmos}+p_{sound}
  $$

- 可以将瞬间声压绘制为关于时间的函数，这样的时变函数在信号处理原理（`signal processing theory`）中称为信号（`signal`）

  ![Signal](Images/Signal.PNG)

### Properties of Sound Waves

- 乐器演奏长而稳定的音符时，声压信号是周期性的（`periodic`），波形由特定乐器的重复模式组成，任意重复模式的周期（`period`）$T$ 描述连续模式实例间经过的最短时间，SI 单位中通常单位为秒
- `frequency` 声波的频率 $f=1/T$，单位为赫兹（`Hertz / Hz`）
- `angular frequency` 角频率  $w=2\pi f=2\pi/T$，单位为弧度每秒
- `phase` 相位，周期信号沿时间轴向左或向右偏移的量，是一个相对术语，例如 $\sin(t)$ 可由 $\cos(t)$ 沿 $t$ 轴相移 $+\frac{\pi}{2}$ 得到
- `speed` 速度 $v$ 取决于介质的材质和物理属性，包括物态（固态，气态或液态）、压强、温度、密度

### Perceived Loudness and the Decibel

- 为了判断听到的声音的响度，耳朵在短暂的滑动时间窗口内连续平均输入声音信号的振幅，这个平均效果使用有效声压（`effective sound pressure`）建模，它定义为特定时间间隔内瞬间声压的均方根（`root mean square / RMS`）
- 对于 $n$ 个离散的声压 $p_i$

  $$
  p_{rms}=\sqrt{\frac{1}{n}\sum_{i=1}^np_i^2}
  $$

- 对于从时间 $T_1$ 开始持续到时间 $T_2$ 的连续的瞬间声压 $p(t)$

  $$
  p_{rms}=\sqrt{\frac{1}{T_2-T_1}\int_{T_1}^{T_2}(p(t))^2\mathrm{d}t}
  $$

- 感知响度与声强（`acoustic intensity`）成正比，声强与声压均方根成正比
- 人类可以感知的声压范围很大，为了管理这样大的动态范围，我们通常以分贝（`decibels / dB`）作为声强的单位。声强以分贝测量时称为声压级（`sound pressure level / SPL`），如下

  $$
  L_p=10\log_{10}(\frac{p_{rms}^2}{p_{ref}^2})dB=20\log_{10}(\frac{p_{rms}}{p_{ref}})dB
  $$

  - 常用的空气中的参考声压为 $p_{ref}=20Pa$
- `Equal-Loudness Contours` 等响度轮廓线。人类耳朵对频率为 2-5 kHz 间的声音最敏感，当频率超出这个范围时，产生相同的感知响度需要更大的声强。每个等响度轮廓线对应不同的感知响度等级，最低的等响度轮廓线表示最安静的声压或听觉的绝对阈值，最高的等响度轮廓线是疼痛的阈值
- `The Audible Frequency Band` 典型的成年人可以听见低至 20 Hz 高至 20 kHz 的声音，等响度轮廓线可以帮助解释为什么人类耳朵能感知的声音在频率的有限“带”里

### Sound Wave Propagation

- 声波是纵波（`longitudinal wave`），声压振荡发生在波的传播方向上，没有偏振（`polarization`）
- 游戏中我们通常对虚拟声波的吸收、反射和有时衍射进行建模，但是通常会忽略折射效果，因为人类听觉不容易注意到这些效果
- `Fall-Off with Distance` 假设声源在所有方向上辐射相等，声压和声强随半径 $r$ 增大而减小

  $$
  p(r)\propto\frac{1}{r}
  $$
  $$
  I(r)\propto\frac{1}{r^2}
  $$
  $$
  L_p(r)=L_p(0)+10\log_{10}(\frac{1}{4\pi r^2})dB=L_p(0)-10\log_{10}({4\pi r^2})dB
  $$

  - 声源不总是全方位的

    ![SoundSources](Images/SoundSources.PNG)

- `Atmospheric Absorption` 由于高频率的波更容易被大气吸收，低频率的声音可以被听到的距离更长
- 声音的减弱依赖于距离、频率、温度和湿度
- `Phase Shift and Interference` 相移和干涉。多个波交互称为干涉，波相互增强振幅称为建设性干涉（`Constructive interference`），波相互抵消称为破坏性干涉（`Destructive interference`）。两波频率匹配，干涉简单增强或减弱振幅；两拨频率显著不同，波的同相和不同相周期交替，导致交替的较高和较低振幅的周期
  - `Comb Filtering` 梳状滤波，声波从表面反射，几乎完全抵消或完全增强某些频率，结果频率有很多狭窄的峰谷，绘制时看起来像梳子。这对音频录制和重现有很大影响
- `Reverb and Echo` 混响和回声
  - 来自声源的三种声波
    - `Direct (dry)` 声波沿直接的无遮挡的路径从声源到达接听者，称为直接或干燥声
    - `Early reflections (echo)` 声源沿间接的路径，在周边表面反射和部分吸收后，经过更长的路径和时间到达接听者，第一组反射声波到达接听者时只与一个或两个表面交互过，还是相对“干净”的信号，所以感知为回声
    - `Late reverberations (tail)` 声波反射一段时间后，叠加和干涉使得接听者再也无法检测出明显的回声，这称为后期混响或扩散尾部（`diffuse tail`）。我们谈到空间声学时，很大程度上在说后期混响对声音质量和音色的影响
  - 回声和混响与干燥声结合可以创建湿（`wet`）声
  - `pre-delay` 预延迟，直接声波到达和第一个反射声波到达间的时间间隔，反映房间或空间的大小
  - `decay` 衰变，反射声波消失花费的时间，反映多少声音被周边吸收，间接说明周边表面的材质
  - `reverb` 混响，用于描述声音湿部件的质量。现在，数字信号处理芯片或软件不仅可用于在录制的音效和音乐中重现自然的混响，还可以增加有各种有趣效果的录音，这些效果通常不会在自然中听到
  - `anechoic chamber` 消音室，用于录制没有混响的纯的声音，这种纯的声音通常是输入数字信号处理流水线的完美候选
