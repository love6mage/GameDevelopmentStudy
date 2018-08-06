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
- `Sound in Motion: The Doppler Effect` 多普勒效应，由于听者和声源间的相对速度，声源靠近听者时声音听起来更高，声源远离听者时声音听起来更低。多普勒频移是频率的变换，可以表示为

  $$
  f'=(\frac{c+v_l}{c+v_s})f
  $$

  - $f$ 为原始频率，$f'$ 为在听者处的多普勒频移频率，$c$ 为声音在空气中的速度，$v_l$ 和 $v_s$ 分别为听者和声源的速度
  - 如果声源的速度相对于声音的速度非常小，上式可以近似为

    $$
    f'=(\frac{1+(v_l-v_s)}{c})f=(\frac{1+\Delta{v}}{c})f
    $$

- `Perception of Position` 感知声音位置的一些因素
  - `Fall-off with distance` 提供对声音远近的大致了解，需要了解作为“基准”的近距离声音的响度
  - `Atmospheric absorption` 导致声音远离听者时高频部分消失
  - `Having two ears` 耳间时差（`interaural time difference`）与耳间强度差（`interaural intensity difference`），右边的声音右耳听起来比左耳响度大，右耳比左耳早听到，左耳听到略微低沉
  - `Ear shape` 耳朵杯状朝前，来自后面的声音听起来比前面的声音略微低沉
  - `The head-related transfer function` 耳朵褶皱对来自不同方向的声音的微小影响的一个数学模型

## The Mathematics of Sound

### Signals

- 信号是一个或多个自变量的任意函数，通常描述某种物理现象的行为
- `Be Discrete, Continuously` 信号有两种基本类型：连续和离散
  - `continuous-time signal` 自变量为实数，$t\in\mathbb{R}$
  - `discrete-time signal` 自变量为整数，$t\in\mathbb{I}$

### Manipulating Signals

- 关于 $t=0$ 反射一个信号，可以将信号方程中的 $t$ 替换为 $-t$
- 将整个信号向右时移 $s$，可以将信号方程中的 $t$ 替换为 $t-s$
- 扩展或压缩一个信号的域，可以缩放自变量

### Linear Time-Invariant (LTI) Systems

- 系统定义为将输入信号转化为新的输出信号的任何设备或处理过程
- 时不变系统是一个系统，它的输入信号的时移会导致输出信号的相等的时移，即该系统的行为不随时间改变
- 线性系统是一个有叠加（`superposition`）属性的系统，如果一个输入信号由其它信号的加权和组成，则输出信号是每个其它信号单独通过该系统产生的输出信号的加权和
- 线性时不变系统是非常有用的，因为它的行为很好理解且容易用数学方法工作，许多真实的物理系统可以用 LTI 系统精确建模
- 可以使用带有输入信号和输出信号的黑盒将系统可视化
  - A 系统的输出可以与 B 系统的输入连接，称为串联（`serial connection`）
  - 两个系统的输出可以加在一起
  - 一个系统的输出可以反馈到早先的输入，称为反馈回路（`feedback loop`）
  - LTI 系统的连接是顺序无关的

### Impulse Response of an LTI System

- 将任意输入信号描述为简单信号的加权和，则可以通过仅仅描述这些简单信号的响应来描述系统的行为
- `The Unit Impulse` 单位脉冲
  - 离散时间中的单位脉冲

    $$
    \delta[n]=\begin{cases}
    1 & \mathrm{if\ n=0} \\
    0 & \mathrm{otherwise}
    \end{cases}
    $$

  - 连续时间中的单位脉冲

    $$
    \delta(t)=\lim_{T\rightarrow 0}b(t)
    $$
    $$
    b(t)=\begin{cases}
    1/T & \mathrm{if\ t\ge 0\ and\ t\lt T} \\
    0 & \mathrm{otherwise}
    \end{cases}
    $$

  - 通常将单位脉冲绘制为一个箭头，它的高度表示曲线下面积
- `Using an Impulse Train to Represent a Signal` 信号可以表示为一连串缩放的脉冲

  $$
  x[n]=\sum_{k=-\infty}^{+\infty}x[k]\delta[n-k]
  $$
  $$
  x(t)=\int_{\tau=-\infty}^{+\infty}x(\tau)\delta(t-\tau)\mathrm{d}\tau
  $$

- `Convolution` 卷积。LTI 系统中单位脉冲的转化可以表示为 $\delta[n]\Rightarrow h[n]$，脉冲响应信号 $h[n]$ 完全描述这个系统。输入信号 $x[n]$ 对应的输出信号可以表示为以下称为卷积和的等式，操作符 * 表示卷积操作

  $$
  y[n]=x[n]*h[n]=\sum_{k=-\infty}^{+\infty}x[k]h[n-k]
  $$

  - 连续时间 LTI 系统的输入信号 $x(t)$ 对应的输出信号可以表示为以下称为卷积积分的等式

    $$
    y(t)=x(t)*h(t)=\int_{\tau=-\infty}^{+\infty}x(\tau)h(t-\tau)\mathrm{d}\tau
    $$

- `Visualizing Convolution` 对于某个特定的 $t$ 值，先绘制 $x(\tau)$，再绘制 $h(t-\tau)$，然后将这两个信号在整个 $\tau$ 轴上相乘，沿 $\tau$ 轴从 $-\infty$ 到 $+\infty$ 积分，得到结果曲线下面积就是 $y(t)$ 在这个特定 $t$ 值处的值。对于每个可能 $t$ 值重复该过程得到完整的输出信号 $y(t)$

  ![Convolution](Images/Convolution.PNG)

- `Some Properties of Convolution` 卷积的一些属性

  $$
  \mathrm{commutative:\ }x(t)*h(t)=h(t)*x(t) \\
  \mathrm{associative:\ }x(t)*(h_1(t)*h_2(t))=(x(t)*h_1(t))*h_2(t) \\
  \mathrm{distributive:\ }x(t)*(h_1(t)+h_2(t))=(x(t)*h_1(t))+(x(t)*h_2(t))
  $$

### The Frequency Domain and the Fourier Transform

- 可以将信号表示为正弦波的加权和，这种表示方法本质上将信号分解为频率分量（`frequency components`），允许我们使用一个强大的数学工具：傅里叶变换（`the Fourier transform`）
- `The Sinusoidal Signal` 当二维圆运动投影到单个轴时产生正弦信号，正弦形式的音频信号产生一个特定频率的“纯”音，实值正弦信号的最一般形式如下

  $$
  x(t)=A\cos(w_0t+\phi)\tag{1}
  $$

  - $A$ 为正弦波的振幅，$w_0$ 为角频率，$\phi$ 为相位偏移
- `The Complex Exponential Signal` 复指数信号，可以将正弦波表示为复数
  - 复数由实部和虚部组成，可以写为 $c=a+jb$，其中 $a$ 和 $b$ 是实数，$j=\sqrt{-1}$ 是虚数单位（`imaginary unit`），实部为 $a=\mathfrak{Re}(c)$，虚部为 $b=\mathfrak{Im}(c)$
  - 复数可以可视化为称为复数平面的二维空间中的向量 $[a,b]$，不过复数和向量是不可互换的，它们的数学行为完全不同
  - 可以定义 2D 向量的长度作为复数的大小（`magnitude`），$|c|=\sqrt{a^2+b^2}$，向量与实轴的夹角称为它的辐角（`argument`），$\arg c=\tan^{-1}(b/a)$，有时称为它的相位（`phase`）
  - 复数以代数方式相乘

    $$
    c_1c_2=(a_1+jb_1)(a_2+jb_2)=(a_1a_2-b_1b_2)+j(a_1b_2+a_2b_1)
    $$
    $$
    |c_1c_2|=|c_1||c_2|
    $$
    $$
    \arg (c_1c_2)=\arg c_1+\arg c_2
    $$
  
  - 复数相乘导致它们的辐角相加，即复数相乘产生旋转，这可以解释为什么单位四元数可以在三维空间中作为旋转，一个四元数本质上是一个有一个实部和三个虚部的四维复数
  - 对于任意复数 $c$，$|c|=1$，函数 $f(n)=c^n$，$n$ 在一个递增正实数序列上取值，则函数 $f(n)$ 将在复数平面上绘制一个圆形路径，这个圆形路径投影到单个轴上产生正弦波

    ![ComplexExponential](Images/ComplexExponential.PNG)

  - 对一个复数提高实数幂在复数平面中产生旋转，事实证明，同样的旋转效果可以通过提高一个实数的复数幂得到，因此等式 $(1)$ 可以写为如下复数形式

    $$
    e^{jw_0t}=\cos w_0t+j\sin w_0t,\ t\in\mathbb{R} \\
    \mathfrak{Re}(e^{jw_0t})=\cos w_0t \\
    \mathfrak{Im}(e^{jw_0t})=\sin w_0t
    $$

    - $e$ 为自然对数的底
    - 上式是所有数学中最重要的公式之一，称为欧拉公式（`Euler’s formula`）
- `The Fourier Series` 信号可以表示为正弦波的和，当信号是周期性的时这样做最容易，这种情况下可以将信号写为成谐波关系（`harmonically related`）的正弦波的和，如下

  $$
  x(t)=\sum_{k=-\infty}^{+\infty}a_ke^{j(kw_0)t}
  $$

  - 这称为信号的傅里叶级数（`Fourier series`）表示
  - 复指数函数 $e^{j(kw_0)t}$ 是构成信号的正弦波分量，这些分量是成谐波关系的，每个分量的频率是称为基频的 $w_0$ 的整数 $k$ 倍，系数 $a_k$ 表示每个谐波在信号 $x(t)$ 中出现的“量”
- `The Fourier Transform` 任何满足狄利克雷条件（`Dirichlet conditions`，一个信号存在傅里叶变换的充分不必要条件）的信号，甚至非周期性信号，可以表示为正弦波的一个线性组合。可以使用傅里叶变换从信号的时间域（`time domain`）表示得到信号的频率域（`frequency domain`）表示，反之亦然

  $$
  X(w)=\int_{-\infty}^{+\infty}x(t)e^{-jwt}\mathrm{d}t
  $$
  $$
  x(t)=\frac{1}{2\pi}\int_{-\infty}^{+\infty}X(w)e^{jwt}\mathrm{d}w
  $$

  - 可以使用连续函数 $X(w)$ 而不是离散的系数 $a_k$ 描述频率分量的“量”
- `Bode Plots` 通常实值信号的傅里叶变换是一个复值信号，$X(w)\in\mathbb{C}$。可视化傅里叶变换通常绘制两个图，可以绘制它的实部和虚部，或者可以绘制它的大小和辐角，后者称为 Bode 图
- `The Fast Fourier Transform (FFT)` 存在一组计算离散时间内的傅里叶变换的快速算法，这个算法家族称为快速傅里叶变换
- `Fourier Transforms and Convolution` 给定一个系统的脉冲响应 $h(t)$，输入信号 $x(t)$ 对应的输出信号 $y(t)$ 为

  $$
  y(t)=x(t)*h(t)
  $$

  - 在频率域，给定这个脉冲响应的傅里叶变换 $H(w)$ 和输入信号的傅里叶变换 $X(w)$，输出信号的傅里叶变换如下

    $$
    Y(w)=X(w)H(w)
    $$
  
  - 有时使用卷积形式方便，有时使用乘积方式方便
  - 事实证明 LTI 系统有对偶性（`duality`），可以扭转时间和频率的角色，几乎相同的数学规则继续适用，例如时间域中信号的调制（`modulation`，两个信号相乘）可以通过卷积频率轴上两个信号的傅里叶变换来理解
- `Filtering` 滤波器（`filter`）是一个 LTI 系统，衰减选定范围的输入频率，其他频率不变
  - `Low-pass filter` 低通滤波器，保持低频率，衰减高频率
  - `High-pass filter` 高通滤波器，衰减低频率，保持高频率
  - `Band-pass filter` 带通滤波器，衰减低频率和高频率，保持有限通带（`passband`）内的频率
  - `Notch filter` 陷波滤波器，保持低频率和高频率，衰减有限阻带（`stopband`）内的频率
  - 滤波器用于立体声系统的均衡器（`equalizer`），基于用户输入衰减或提高特定频率
  - 滤波器还可用于衰减噪声，如果噪声信号和所需信号的频谱占据频率轴的不同区域
  - 理想滤波器的频率响应 $H(w)$ 看起来像一个矩形盒，通带内值为 1，阻带内值为 0
  - 大多数真实世界的滤波器的频率响应在通带和阻带间逐渐下降，这有助于在所需频率与不需要的频率间没有单一明确的线的情况下进行滤波

## The Technology of Sound
