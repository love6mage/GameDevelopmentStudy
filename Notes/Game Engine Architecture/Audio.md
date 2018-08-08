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

- 感知响度与声强（`acoustic intensity`）成正比，声强与 RMS 声压的平方成正比

  $$
  I\propto p_{rms}^2
  $$

- 人类可以感知的声压范围很大，为了管理这样大的动态范围，我们通常以分贝（`decibels / dB`）作为声强的单位。声强以分贝测量时称为声压级（`sound pressure level / SPL`），如下

  $$
  L_p=10\log_{10}(\frac{p_{rms}^2}{p_{ref}^2})\mathrm{dB}=20\log_{10}(\frac{p_{rms}}{p_{ref}})\mathrm{dB}
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
  L_p(r)=L_p(0)+10\log_{10}(\frac{1}{4\pi r^2})\mathrm{dB}=L_p(0)-10\log_{10}({4\pi r^2})\mathrm{dB}
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
  - `The head-related transfer function (HRTF)` 耳朵褶皱对来自不同方向的声音的微小影响的一个数学模型

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

### Analog Audio Technology

- 最早的音频硬件基于模拟电子设备
- `Microphones` 麦克风，将音频压缩波转化为电子信号的传感器，动圈麦克风（`dynamic microphone`）使用电磁感应，电容式麦克风（`condenser microphone`）利用电容的变换，其他类型的麦克风使用压电发生或光调制来产生电压信号
  - 不同麦克风有不同敏感度模式，称为极性模式（`polar patterns`），描述麦克风对围绕其中心轴的各个角度声音的敏感程度，全向麦克风（`omnidirectional mic`）在各个方向上敏感度相等，双向麦克风（`bidirectional mic`）有两个敏感度“波瓣”，形状为阿拉伯数字 8，心形麦克风（`cardioid mic`）有一个单向敏感度分布，形状类似心形
- `Speakers` 扬声器，将变化的输入电压信号转化为薄膜振动的传感器，薄膜振动会产生气压变化从而产生声压
- `Speaker Layouts: Stereo` 声音系统通常支持多个扬声器输出声道，一般至少有两个扬声器来支持左和右立体声声道；一些高保真立体声系统还拥有两个额外的高频扬声器（`tweeters`，能重现左右声道中最高频率声音的微型扬声器），允许两个主扬声器更大，因此更好地覆盖低音；一些立体声系统还支持低音炮或 LFE （`(low-frequency effects`）扬声器，这样的系统有时称为 2.1 系统，2 代表左和右，.1 代表 LFE 扬声器
  - `Headphones Versus Speakers` 房间中的立体声扬声器通常在听者前方向任一侧偏移，每个耳朵会同时接受来自左和右扬声器的声波，来自远处扬声器的相移声波往往会干涉来自近处扬声器的声波；耳机与耳朵直接接触，不会发生干涉，且由于耳机几乎直接将声音传递到耳道，耳朵形状的 HRTF 无法发挥作用，听者受到的空间信息较少
- `Speaker Layouts: Surround Sound` 家庭影院环绕音系统通常有两种风格：5.1 和 7.1。.1 指低音炮，5.1 系统的 5 个主扬声器为中、前左、前右、后左、后右，7.1 比 5.1 多了环绕左侧和环绕右侧两个主扬声器，杜比数字 AC-3（`Dolby Digital AC-3`）和 `DTS` 是两个流行的环绕音技术
- `Analog Signal Levels` 音频电压信号可以以各种电压电平发送。麦克风通常产生低振幅的电压信号，称为麦克风电平（`mic-level`）信号。对于组件之间的连接，使用更高电压的线路电平（`line-level`）信号。专业音频设备的线路电平与消费电子产品存在很大差异，连接音频设备时匹配输入和输出信号的电平十分重要，传入过高电压导致信号削波，传入过低电压导致音频听起来比它应该的更安静
- `Amplifiers` 放大器，模拟电子电路，在输出端产生几乎完全复制的输入信号，但信号的振幅显著增加。麦克风产生的电压很小，必须通过放大器来驱动扬声器产生可听见的声波。放大器系统的增益 $A$ 定义为输出功率与输入功率之比，单位通常为分贝

  $$
  A=10\log_{10}(\frac{P_{out}}{P_{in}})\mathrm{dB}
  $$

- `Volume/Gain Controls` 音量 / 增益控制，基本上是反向放大器，也称为衰减器（`attenuator`）

  $$
  A=10\log_{10}(\frac{P_{volume}}{P_{max}})\mathrm{dB},\ P_{volume}\lt P_{max}
  $$

- `Analog Wiring and Connectors` 模拟单声道音频电压信号可以由一对导线承载，立体声信号则需要三根导线，接线可以在设备内部，称为总线（`bus`），也可以在外部，用于连接不同设备。外部连接通常有直接的“夹子”，高端扬声器使用的螺柱连接器，或各种标准化连接器，如大多数耳机和 PC 声卡使用的 TRS mini-jack

### Digital Audio Technology

- CD 的引入标志着音频行业向数字音频存储和处理的转折点。模拟和数字音频技术的分别完全对应信号处理原理中连续时间和离散时间信号的分别
- `Analog-to-Digital Conversion: Pulse-Code Modulation` 脉冲编码调制，编码采样的模拟声音信号让它可以保存到电脑内存，通过数字电话网络传输或刻录到光盘的标准方法。电压测量以规则的时间间隔进行并以浮点数形式保存或量化后以固定位数的整数形式保存。测量模拟电压并转化为量化数字形式的处理称为模数转换（`analog-to-digital conversion` / `A/D conversion`），以规则的时间间隔重复此过程称为采样（`sampling.`），执行 A/D 转换或采样的硬件或软件组件称为 A/D 转换器或 ADC
  - 数学方面，给定连续时间音频信号 $p(t)$，可以构造采样版本的 $p[n]$，对于每个采样，$p[n]=p(nT_s)$，其中 $n$ 为用于采样索引的非负整数，$T_s$ 是每个采样的时间间隔，称为采样周期
  - PCM 采样的数字信号有两个重要属性
    - `Sampling rate` 采样率，采样的频率，根据香农-奈奎斯特采样定理，由于人类可以听到的声音是带限（`and-limited`，20 Hz - 20 kHz）的，所以可以使用略高于 40 kHz 的采样率忠实采样所有音频信号
    - `Bit depth` 位深，有时也称为分辨率（`resolution`），描述用于表示每个量化电压测量的数字位数。量化误差（`Quantization error`）是将测量电压值四舍五入到最近量化值引入的误差，相同情况下更大的位深导致更小的量化误差，产生更高质量的记录。未压缩音频数据格式中位深通常为 16
- `Digital-to-Analog Conversion: Demodulation` 解调，需要与模数转换相反的过程，称为数模转换（`digitalto-analog conversion` / `D/A conversion`），数模转换电路称为 DAC。用离散电压电平序列驱动模拟电压电路时通常会引入不必要的高频振荡，可以使用低通或带通滤波器消除这些不必要的振荡
- `Digital Audio Formats and Codecs` 数字音频格式和编解码器，一些音频格式用未压缩形式保持 PCM 数据；其他的使用各种数据压缩形式降低文件大小或传输带宽，一些压缩方案是有损的（`lossy`），其他压缩方式是无损的（`lossless`）
  - `Raw header-less PCM data` 原始无头 PCM 数据，有时用于信号的元信息如采样率和位深已知的情况
  - `Linear PCM (LPCM)` 线性 PCM，未压缩音频格式，振幅测量使用线性标度而不是对数标度
  - `WAV` 微软和 IBM 创建的未压缩文件格式，常见于微软操作系统，是资源交换文件格式（`resource interchange file format / RIFF`，TIFF 文件由块组成，每个块由四字符码定义块内容和大小）家族的一员，文件中的比特流符合 LPCM 格式。WAV 文件可以包含压缩音频，但最常用于保存未压缩音频数据
  - `WMA (Windows Media Audio)` 微软设计的专有音频压缩技术，可替代 MP3
  - `AIFF (audio interchange file format)` 音频交换文件格式，苹果开发的格式，与 WAV/RIFF 文件相似，AIFF 文件通常包含未压缩 PCM 数据，由块组成。AIFF-C 是 AIFF 的压缩变体
  - `MP3` 有损压缩音频文件格式，已成为大多数数字音频播放器事实上的标准。利用感知编码（`perceptual coding`），MP3 压缩可以使文件只有十分之一大小，但与原始未压缩音频的感知差异很小
  - `ATRAC (Adaptive Transform Acoustic Coding)` 自适应变换声学编码，索尼开发的专有音频压缩技术家族
  - `Ogg Vorbis` 提供有损压缩的开源文件格式，Ogg 指的是通常与 Vorbis 数据格式结合使用的“容器”格式
  - `Dolby Digital (AC-3)` 有损压缩格式，支持从单声道到 5.1 环绕声的声道格式
  - `DTS` DTS 开发的一系列影院音频技术
  - `VAG` PS3 开发者可使用的专有音频文件格式，利用了自适应差分 PCM（`adaptive differential PCM (ADPCM)`）。差分 PCM（`Differential PCM (DPCM)`）保存采样间的增量而不是采样的绝对值以允许信号更高效的压缩，自适应差分 PCM 动态改变采样率以进一步提高可实现的压缩比
  - `MPEG-4 SLS, MPEG-4 ALS and MPEG-4 DST` 提供无损压缩的格式
- `Parallel and Interleaved Audio Data` 并行和交错音频数据，多声道音频数据可以使用单独的缓冲区保存每个单声道的采样，或将这些数据按预定义顺序交错保存在单个缓冲区中
- `Digital Wiring and Connectors` 数字接线和连接器
  - `S/PDIF (Sony/Philips Digital Interconnect Format)` 索尼 / 飞利浦数字互连格式，以数字方式传输音频信号的互连技术。S/PDIF 标准物理上通过同轴电缆连接（也称为 S/PDIF）或光纤连接（称为 `TOSLINK`）实现，S/PDIF 传输协议仅限于 LPCM 未压缩音频，相同的物理接口还可用于比特流编码音频如杜比数字或 DTS 有损压缩数据
  - `HDMI (high-definition multimedia interface)` 高清多媒体接口，未压缩多声道 LPCM 在消费类音频设备上只能通过 HDMI 连接发送，HDMI 连接器用于传输未压缩数字视频和压缩或未压缩数字音频信号
  - `USB connections` 有时用 USB 连接发送音频信号，大多数游戏主机上 USB 输出仅用于驱动耳机
  - `Wireless audio connections` 无限音频连接，蓝牙标准是最常用的音频信号无线传输方法

## Rendering Audio in 3D

- 任意发生在虚拟三维世界的游戏需要某种 3D 音频渲染引擎（`3D audio rendering engine`），高质量的 3D 音频系统应该给玩家提供一个丰富、沉浸和可信的音景，匹配 3D 世界中正在发生的声音，同时支持故事和保持对游戏音调设计的忠诚
  - 这个系统的输入为来自整个游戏世界的无数 3D 声音（`3D sounds`），如脚步声、碰撞声、炮火声、风或雨这样的环境声音等
  - 这个系统的输出为一些声道，在扬声器中播放时尽可能可信地重现玩家如果在虚拟世界中会真正听到什么
- 游戏音频引擎还负责播放不是来源于虚拟世界的声音，如音乐曲目、游戏菜单系统发出的声音、叙述者的画外音、可能某些环境声音等，称为 2D 声音（`2D sounds`），这些声音设计为与 3D 空间化引擎的输出混合后“直接”在扬声器中播放

### Overview of 3D Sound Rendering

- 3D 音频引擎的主要任务
  - `Sound synthesis` 声音合成，生成游戏世界中发生的事件对应的声音信号，可以播放预先录制的声音片段（`clip`）或运行时程序生成
  - `Spatialization` 空间化，从听者角度看，产生每个 3D 声音来自游戏世界中正确位置的幻觉。通过以以下两种方式控制声波的振幅来实现
    - `Distance-based attenuation` 基于距离衰减，控制声音的整体音量来指示其与听者的径向距离
    - `Pan` 平移，控制每个可用扬声器中声音的相对音量来指示声音到达方向
  - `Acoustical modeling` 声学建模，通过模拟表征聆听空间的早期反射和后期混响，以及考虑存在部分或完全阻塞声源和听者间路径的障碍物，来增强渲染音景的真实性。一些声音引擎还建模大气吸收的频率依赖效应和 HRTF 效应
  - `Doppler shifting` 多普勒频移，用于说明声源和听者间的相对运动
  - `Mixing` 混合，控制游戏中所有 2D 和 3D 声音的相对音量，部分由物理驱动，部分由游戏声音设计师的美学选择驱动

### Modeling the Audio World

- 音频世界模型有以下几个元素
  - `3D sound sources` 3D 声源。游戏世界中每个 3D 声音由特定位置发出的单声道音频信号组成，同时还需要提供它的速度、辐射模式（`radiation pattern`，全方向、锥、平面）和范围（超过后声音是听不见的）
  - `Listener` 听者，位于游戏世界的“虚拟麦克风”，通过位置、速度和方向定义
  - `Environmental model` 环境模型，描述虚拟世界中存在的物体和表面的几何和属性，或者描述游戏发生的聆听空间的声学属性
- 声源和听者的位置以及声源的辐射模式用于基于距离衰减的计算
- 听者的方向定义一个参考帧，在其中计算声音的角位置，角度反过来决定平移（`pan`）
- 应用多普勒频移时会用到声源和听者的相对速度
- 环境模型用于建模聆听空间的声学，说明声音路径的部分或完全阻塞

### Distance-Based Attenuation

- `Fall-Off Min and Max` 每个声源通常用衰减（`fall-off / FO`）参数注释，衰减最小值（`fall-off min / FO min`）为一个最小半径，表示为 $r_{min}$，该半径内声源完全不衰减，衰减最大值（`fall-off max / FO max`）为一个最大半径，表示为 $r_{max}$，超过该半径声源衰减为零，可以忽略。我们需要在半径 FO min 和 FO max 间从全音量平滑混合到零
- `Blending to Zero` 混合最大音量到零
  - 可以使用 FO min 和 FO max 间的线性混合
  - 增益与声压的振幅成正比，衰减为 $1/r$，所以可以使用 $1/r$ 曲线从全音量到零混合声音增益。由于 $1/r$ 曲线是渐进的，永远不为零，可以稍微向下移动曲线让它与 $r$ 轴交于 $r_{max}$ 或者简单将所有 $r\gt r_{max}$ 的声强夹为零
- `Bending the Rules` 顽皮狗的最后生还者中使用了复杂的衰减曲线允许对话在很长的距离内可以被听见，而且衰减曲线会根据隐形状态和战斗状态运行时动态调整。有时确保玩家能清楚听见对话比真实的衰减建模更重要，可以选择启用”甜味剂“混响允许角色声音在拐角处散开，即使直接路径完全是阻塞的。3D 音频模型设计有很多种”作弊“方式，最重要的是要满足游戏的需求，不用担心物理定律被冒犯
- `Atmospheric Attenuation` 大气衰减，一些游戏，包括顽皮狗的最后生还者，将这种现象通过为每个 3D 声音应用低通滤波器建模，随着声源和听者距离的增加，通带向更低的频率滑动

### Pan

- 通过控制每个可用扬声器的音量，可以产生声音在三维空间中的幻像。这种平移方法称为振幅平移（`amplitude panning`），有时也称为 IDD 平移，因为它依赖于耳间强度差的感知效果产生声音的幻像
- 设想听者位于一个圆的圆心，扬声器位于圆周上各点，这个圆可以称为扬声器圈（`speaker circle`），它的半径近似于听者与任一扬声器间的平均距离。为了平移一个声音，先判断它的方位角 $\theta_s$，然后找到圆周上与它相邻的两个扬声器的方位角，计算如下百分比 $\beta$，用于决定每个扬声器的增益

  $$
  \beta=\frac{\theta_s-\theta_1}{\theta_2-\theta_1}
  $$

  ![Pan](Images/Pan.PNG)

- `Constant Gain Panning` 恒定增益平移，给定未平移声音的增益 $A$，在每个扬声器中播放的声音的增益如下

  $$
  A_1=(1-\beta)A \\
  A_2=\beta A
  $$

  - 净增益 $A=A_1+A_2$ 是恒定的
  - 这个方法最主要的问题是不产生恒定感知响度，因为感知响度与声压的平方成正比
- `The Constant Power Pan Law` 恒定功率平移法，保持恒定的感知响度

  $$
  A_1=\sin(\frac{\pi}{2}\beta)A
  $$
  $$
  A_2=\cos(\frac{\pi}{2}\beta)A
  $$
  当 $\beta=\frac{1}{2}$ 时
  $$
  A_1^2+A_2^2=2(\frac{1}{\sqrt{2}}A)^2=A^2
  $$
  电压增益（或振幅增益）
  $$
  20\log_{10}(\frac{A_{out}}{A_{in}})=20\log_{10}(\frac{1}{\sqrt{2}})\approx -3\ \mathrm{dB}
  $$

  - “3 dB rule”：如果声音平均混合到两个扬声器，每个扬声器的增益应该相对于声音只在一个扬声器中播放的增益减少 3 dB
- `Headroom` 净空，减小最大音量范围，保留空间，防止高音量声音播放超过扬声器最大功率
- `To Center or Not To Center?` 电影院中，中声道用于演讲，只有声音效果会被平移到房间周围其他扬声器，因为观众期望从前方中心听到电影中角色的对话，而且声音效果不会耗尽可用净空。3D 游戏中场景不同，玩家摆动相机时听到的对话应该来自不同角度，也需要被平移，但是这让我们又回到了净空问题。顽皮狗通过分裂差异（`splitting the difference`）解决该问题，总是在中声道播放一些对话，其他一些与声音效果一起平移到其他扬声器
- `Focus` 声源在进入扬声器圈后，无法再作为通过单个角度表示的点源准确建模，这种情况下需要将声源建模为扬声器圈上的弧而不是点。弧的角度称为聚焦角度（`focus angle`），表示为 $\alpha$。为了渲染有非零聚焦角度的声音，可以将声音强度或功率分到弧上和与弧相邻的扬声器，产生延伸穿过弧的幻像的感知。分法有很多种，例如可以让弧上的扬声器收到相等的最大功率，然后分摊少许声音到与弧相邻的扬声器来产生衰减，这个过程要遵循恒定功率平移法

  ![FocusAngle](Images/FocusAngle.PNG)

- `Dealing with Verticality` 处理垂直度。上述的扬声器圈是平面扬声器布置，为了建模一个真正的全向声系统（`periphonic`）声场，需要使用球形扬声器布置。鲜为人知的 Ambisonic 技术可以适应平面和球形扬声器布置，但是目前不支持游戏主机。
  - 可以利用聚焦的概念一定程度上模拟垂直度。简单将所有声音投影到水平面，对于所有投影在扬声器圈内的声音使用非零聚焦角度；远处的不同高度的声音几乎以相同的方式渲染，但是高处的声音从头顶过去时，将它混合到多个扬声器，产生一个扬声器圈内的幻像，再结合基于距离的衰减和频率依赖的大气吸收，可以提供听者足够的线索让声音似乎位于听者上方或下方

### Propagation, Reverb and Acoustics

- `Sound propagation modeling` 声音传播建模，该术语可用于任何设计为考虑声波在在空间中传播方式的技术。研究和交互式媒体与游戏中使用了多种不同方法，可以分为以下三种基本分类
  - `Geometric analysis` 几何分析，试图建模声波的实际路径
  - `Perceptually based models` 基于感知的模型，专注于使用聆听空间声学的 LTI 系统模型再现耳朵感知的内容
  - `Ad hoc methods` 专有方法，采用各种近似方法，以最小的数据和处理带宽产生合理准确的声学效果
- `Modeling Propagation Effects with an LTI System` 可以使用 LTI 系统建模传播效果，理论上，对于每对给定的代表声源和听者的点，可以计算一个脉冲响应，然后使用该脉冲响应卷积干燥声

  $$
  p_{wet}(t)=p_{dry}(t)*h(t)
  $$

  - 虚拟空间中脉冲响应计算需要执行复杂和耗时的模拟，且要为很多对声源和听者的点计算脉冲响应，并且卷积的计算也很耗时。随着游戏硬件的发展，基于卷积的方法变得更可行，但是大多数游戏仍然不使用它，而是依赖各种专有方法
- `Reverb Regions` 建模游戏世界的湿特性的一个常见方法是用手动放置的区域注释游戏世界，每个区域使用适当的混响设置如预延迟、衰减、密度和扩散进行标记。听者穿越游戏空间时，可以在混响设置间平滑地交叉混合
- `Obstruction, Occlusion and Exclusion` 物体和表面影响声波传播的三种方式
  - `Occlusion` 闭塞，声源和听者间的直接和间接路径都被阻断。声音的干燥和湿成分都被衰减或完全消失
  - `Obstruction` 阻塞，声源和听者间的直接路径被阻断，间接路径可用。声音的干燥成分衰减或完全消失，湿成分可能也被改变，考虑声波以更长更反射的路径到听者
  - `Exclusion` 排斥，声源和听者间的间接路径被阻断，直接路径可用。声音的干燥成分不变，湿成分被衰减或完全消失

    ![OcclusionObstructionExclusion](Images/OcclusionObstructionExclusion.PNG)

  - `Analyzing the Direct Path` 可以投射从声源到听者的射线，对每个接触点查询表面材质属性判断多少声音能量被吸收。如果所有声音能量都被吸收，则声音不会被听见；如果没有全部被吸收，可以使用对应的量来衰减声音干燥成分的增益，模拟声音的传输
  - `Analyzing the Indirect Path` 游戏中不使用精确的路径跟踪方法，而是近似建模间接路径。例如可以利用混响区域判断间接路径是否存在，如果声源和听者在同一区域，假设间接路径存在；如果声源和听者在不同区域，假设间接路径被阻断
  - `Accounting for Diffraction` 由于衍射，声音可以在拐角周围听到，就像存在一个直接路径一样。可以通过“曲线”投射判断是否存在衍射，由于大多数碰撞引擎不支持曲线路径跟踪，我们可以使用多个射线投射模拟曲线投射，即使用曲线路径的分段线性近似
  - `Applying the Model Using Reverb and Gain` 渲染声音的一个简单方法是基于直接路径和间接路径是否完全或部分阻断，分别衰减声音的干燥和湿成分，还可以基于判断声音路径时收集的启发信息给声音的每个成分应用或多或少的混响。每个游戏的需求不同，需要大量试验与错误
  - `Blending Obstructed Sounds` 声源从自由、闭塞、阻塞、排斥四个状态间变换时声音的音调和响度似乎会“跳跃”，有很多方法平滑这些过渡。可以应用些许滞后，延迟声音系统对声源状态变换的响应，然后使用这个短延迟窗口平滑交叉混合两组混响设置。这个延迟可能会引人注意，所以不是一个理想解决方案。顽皮狗的高级声音程序员发明了一个称为随机传播建模（`stocastic propagation modeling`）的专有系统，这个系统投射一组射线到每个声源，有些是直接的，有些是间接的，在多个帧上累积这些命中或未命中结果，通过这个数据生成一个每个声源的干燥和湿成分经历的遮挡程度的概率模型，允许声音从完全阻塞到完全自由的平滑过渡
- `Sound Portals in The Last of Us` 使用互连区域的网络，区域有两种：房间（`rooms`）和门户（`portals`）。对于每个声源，使用声音设计师布置区域时提供的连接信息找到听者到声音的路径。如果声源和听者在同一个房间，使用神秘海域系列中使用的经过验证的方法执行闭塞/阻塞/排斥分析；如果声源在与听者房间通过一个门户直接连接的房间中，则播放声音，好像它们位于门户区域中

  ![SoundPortal](Images/SoundPortal.PNG)

### Doppler Shift
