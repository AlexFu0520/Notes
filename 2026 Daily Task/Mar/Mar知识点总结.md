# 电磁场与电磁波
## 第一章 矢量分析：
### 三种坐标系的坐标表示：
1. 直角坐标系：
- $\vec{A}=A_{x}\vec{e_{x}}+A_{y}\vec{e_{y}}+A_{z}\vec{e_{z}}$
- $dS_{x}=dydz~~,~~dS_{y}=dxdz~~,~~dS_{z}=dxdy$
- $dV=dxdydz$
2. 圆柱坐标系：
- $\vec{A}=A_{\rho}e_{\rho}+A_{\phi}e_{\phi}+A_{z}e_{z}$
- $dS_{\rho}=\rho d\phi dz~~,~~dS_{\phi}=d\rho dz~~,~~dS_{z}=\rho d\rho d\phi$
- $dV=\rho d\rho d\phi dz$
- 圆柱微分元矢量：$d \vec{r}=\vec{e_{\rho}d\rho}+\vec{e_{\phi}}\rho d\phi+\vec{e_{z}}dz$
- 度量系数：$d_{\rho}=1,h_{\phi}=\rho,h_{z}=1$
- 圆柱坐标系与直角坐标系的变换关系：$$\rho=\sqrt{ x^2+y^2},\phi=\arctan\left( \frac{y}{x} \right),z=z$$或$$x=\rho \cos \phi,y=\rho \sin \phi,z=z$$
3. 球坐标系：
- $\vec{A}=A_{r} \vec{e_{r}}+A_{\theta} \vec{e_{\theta}}+A_{\phi}\vec{e_{\phi}}$ 
- $dS_{r}=r^2\sin \theta d\theta d\phi,dS_{\theta}=r\sin \theta dr d{\phi},dS_{\phi}=r dr d\theta$
- $dV=r^2\sin \theta dr d\theta d \phi$
- 球坐标系微元矢量：$d\vec{r}= \vec{e_{r}}dr + \vec{e_{\theta}}r d\theta + \vec{e_{\theta}}r\sin \theta d\phi$
- 度量系数：$h_{r}=1,h_{\theta}=r,h_{\phi}=r\sin \theta$
- 球坐标系与直角坐标系的变换关系：$$x=r\sin \theta \cos \phi,y=r\sin \theta \sin \phi,z=r\cos \theta$$
### 方向导数与梯度：
1. 直角坐标系中方向导数：$$\frac{\partial u}{\partial l}=\frac{\partial u}{\partial x}+\frac{\partial u}{\partial y} +\frac{\partial u}{\partial z}$$
2. 哈密顿算符(**Hamiltonian**)：$$\nabla=\vec{e_{x}} \frac{\partial}{\partial x}+\vec{e_{x}} \frac{\partial}{\partial y}+\vec{e_{x}} \frac{\partial}{\partial z}$$
3. 三个坐标系中梯度计算公式：$$grad~u=\nabla u=(\vec{e_{x}} \frac{\partial}{\partial x}+\vec{e_{x}} \frac{\partial}{\partial y}+\vec{e_{x}} \frac{\partial}{\partial z})u$$ $$\nabla~u= \vec{e_{\rho}} \frac{\partial u}{\partial \rho}+\vec{e_{\phi}} \frac{\partial u}{\rho\partial \phi}+\vec{e_{z}} \frac{\partial u}{\partial z} $$ $$\nabla u=\vec{e_{r} }\frac{\partial u}{\partial r}+\vec{e_{\theta}} \frac{\partial u}{r\partial \theta}+\vec{e_{\phi}} \frac{\partial u}{r\sin \theta \partial \phi}$$
### 矢量场的通量与散度
1. 矢量场 $\vec{F}= \vec{F}(x,y,z,t)$
2. 通量:$$\phi=\int_{S}\vec{F} \cdot d \vec{S}$$
3. 散度：$$div \vec{F}=\nabla \cdot  \vec{F}$$
4. **高斯定理**:$$\int_{V}\nabla \cdot  \vec{F}dV=\oint_{S}\vec{F} \cdot d \vec{S}$$
### 矢量场的环流与旋度
1. 环流:$$\Gamma=\oint_{C} \vec{F} \cdot d \vec{l}$$
2. 旋度：$$rot \vec{F}=e_{x}\left(\frac{\partial F_{z}}{\partial y}-\frac{\partial F_{y}}{\partial z}\right) + \vec{e_{y}}\left( \frac{\partial F_{x}}{\partial z}-\frac{\partial F_{z}}{\partial x}\right) +\vec{e_{z}}\left( \frac{\partial F_{y}}{\partial x}-\frac{\partial F_{x}}{\partial y} \right)$$ $$rot \vec{F}=\nabla\times \vec{F}$$圆柱坐标系：$$\nabla \times \vec{F}=\frac{1}{\rho}\begin{vmatrix}\vec{e_{\rho}}&\rho \vec{e_{\phi}}& \vec{e_{z}}\\ \frac{\partial}{\partial \rho}& \frac{\partial}{\partial \phi}& \frac{\partial}{\partial z}\\ \vec{F_{\rho}}&\rho \vec{F_{\rho}}& \vec{F_{z}}\end{vmatrix}$$球坐标系：$$\nabla \times \vec{F}=\frac{1}{r^2\sin \theta}\begin{vmatrix}\vec{e_{r}}&r \vec{e_{\theta}}&r\sin \theta \vec{e_{\phi}}\\ \frac{\partial}{\partial r}& \frac{\partial}{\partial \theta}& \frac{\partial}{\partial \phi}\\ \vec{F_{r}}& r\vec{F_{\theta}}& r\sin \theta  \vec{F_{\phi}} \\\end{vmatrix}$$
### 斯托克斯定理：
$$\int_{S}\nabla\times  \vec{F}\cdot d \vec{S}=\oint_{C} \vec{F} \cdot d \vec{l}$$
### 标量场的重要性质：
$$\nabla \times (\nabla u)\equiv_{0}$$
### 无旋场的标量位：
如果$\nabla \times \vec{F}\equiv0$ ,存在标量函数u，使得$$\vec{F}=-\nabla u$$ 函数u为无旋场$\vec{F}$ 的标量位函数，简称标量位
### 矢量场的重要性质：
$$\nabla \cdot (\nabla \times \vec{A}) \equiv 0$$
### 无散场的矢量位：
如果$\nabla \cdot \vec{F}\equiv 0$ ，则存在矢量函数 $\vec{A}$ ,使得$$\vec{F}=\nabla \times  \vec{A}$$ 函数 $\vec{A}$ 称为无旋场 $\vec{F}$ 的矢量位函数，简称矢量位
### 格林函数：
1. 格林第一恒等式：$$\int_{V}(u\nabla^2v+\nabla u \cdot \nabla v)dV=\oint_{S}u \nabla v \cdot  \vec{e_{n}}dS$$
2. 格林第二恒等式：$$\int_{V}(u\nabla^2v-v\nabla^2u)dV=\oint_{S}\left( u \frac{\partial v}{\partial n}-v \frac{\partial u}{\partial n} \right)dS$$
### 亥姆霍兹定理：
由于 $\nabla\times [\nabla u( \vec{r})]\equiv 0$， $\nabla \cdot [\nabla \times \vec{A(\vec{r})}] \equiv 0$，因而一个矢量场可以表示为一个无旋场与无散场之和，即 $$\vec{F}= \vec{F_{l}} + \vec{F_{c}}$$

---
## 第二章 电磁场的基本规律：
### 电荷密度：
#### 电荷体密度:
$$\rho(\vec{r},t)= \frac{\mathrm{d}q(\vec{r},t)}{\mathrm{d}V}$$
#### 电荷面密度：
$$\rho_{s}= \frac{\mathrm{d}q(\vec{r},t)}{\mathrm{d}S}$$
#### 电荷线密度：
$$\rho_{l}= \frac{\mathrm{d}q(\vec{r},t)}{\mathrm{d}l}$$
#### 点电荷：
位于坐标原点的点电荷 $q(t)$ 的电荷体密度可用 $\delta(\vec{r})$ 函数表示为：$$\rho(\vec{r},t)=q(t) \delta(\vec{r}) $$
式中的 $\vec{r}$ 是位置矢量，而 $$\delta(\vec{r})=\begin{cases}0, \vec{r}\neq 0 \\ \infty, \vec{r}=0\end{cases}$$
且：$$\int_{V} \delta(\vec{r})\mathrm{d}V= \begin{cases}0,积分区域不包含 \vec{r}=0的点\\1,积分区域包含 \vec{r}=0的点\end{cases}$$
### 电流密度：
#### 体电流密度矢量 $J(\vec{r},t)$ 
$$J(\vec{r},t)=\vec{e_{n}} \frac{\mathrm{d}i(\vec{r},t)}{\mathrm{d}S}$$单位为 $A / m ^2$

当体密度为 $\rho$ 当电荷以速度 $\vec{v}$ 运动时，可得电流密度：$$\vec{J}=\rho  \vec{v}$$

若存在多种不同的带电粒子，该点的电流密度：$$\vec{J}=\sum_{i} \vec{J_{i}}=\sum_{i} \rho_{i}  \vec{v_{i}}$$
#### 面电流密度: 
$$J_{s}(\vec{r},t)= \vec{e_{n}} \frac{\mathrm{d}i(\vec{r},t)}{\mathrm{d}l}$$

当面密度为 $\rho$ 的电荷在曲面 $S$ 上以速度 $\vec{v}$ 运动时，其面电流密度矢量为$$\vec{J_{s}}=\rho_{s} \vec{v}$$
#### 线电流
线电荷密度为 $\rho_{l}$ 的电荷以速度 $\vec{v}$ 运动时形成的线电流为 $$i=\rho_{l}v$$
### 电流连续性方程
在电流密度为 $\vec{J}$ 、电荷密度为 $\rho$ 的空间中，任取一个闭合面 $S$ 所限定的体积 $V$ 。在单位时间内，通过闭合面 $S$ 流出的电荷量为 $\oint_{S} \vec{J} \cdot \mathrm{d} \vec{S}$ ，体积 内的电荷减少量则为 $- \frac{\mathrm{d}}{\mathrm{d}t}\int_{V}\rho \mathrm{d}V$

根据电荷守恒定律得出电流连续性方程的积分形式：$$\oint_{S}\vec{J} \mathrm{d}  \vec{S}=- \frac{\mathrm{d}q}{\mathrm{d}t}=- \frac{\mathrm{d}}{\mathrm{d}t} \int_{V}\rho \mathrm{d}V$$

根据散度（高斯）定理，$\oint_{S}\vec{J} \cdot \mathrm{d} \vec{S}=\int_{V} \nabla \cdot  \vec{J} \mathrm{d}V$ 可得 $$\int_{V}\left( \nabla \cdot  \vec{J}+ \frac{\partial \rho}{\partial t} \right)\mathrm{d}V=0$$
可得电流连续性方程的微分形式： $$\nabla \cdot \vec{J} + \frac{\partial \rho}{\partial t}=0$$

恒定电流的连续性方程： $$\nabla \cdot \vec{J}=0$$
### 库仑定律与电场强度
#### 库仑定律
真空中两个静止点电荷 $q_1$ 与 $q_2$ 之间的作用力：
$$\vec{F}_{12} = \frac{1}{4\pi\varepsilon_0} \frac{q_1 q_2}{R^2} \vec{e}_{12}$$
#### 电场强度 
$$E(\vec{r}) = \frac{q}{4\pi \varepsilon_{0}R^3} \vec{R}$$
点电荷的位置矢量为 $\vec{r'}$ ，点电荷的电场强度可用三个电荷密度表示为 $$E(\vec{r})=\frac{1}{4\pi \varepsilon_{0}}\int_{V} \frac{\rho(\vec{r'})\vec{R}}{R^3} \mathrm{d}V'$$$$E(\vec{r})=\frac{1}{4\pi \varepsilon_{0}}\int_{S} \frac{\vec{R}}{R^3} \rho_{S}(\vec{r'})\mathrm{d}S'$$$$E(\vec{r})=\frac{1}{4\pi \varepsilon_{0}}\int_{l} \frac{\vec{R}}{R^3} \rho_{l}(\vec{r'})\mathrm{d}l'$$
式中 $\vec{R}= \vec{r}-\vec{r'},R=|\vec{R}|$

### 静电场的散度与旋度
#### 静电场的散度：
$$\nabla \cdot  \vec{E}= \frac{\rho}{\varepsilon_{0}}$$
#### 静电场的高斯定理的积分形式：
$$\oint_S \vec{E} \cdot d\vec{S} = \frac{1}{\varepsilon_0} \int_V \rho dV$$
#### 静电场的旋度：
$$\nabla \times  \vec{E}=0$$
表明静电场是无旋场

### 安培力定律与磁感应强度
#### 安培力定律：
$$\mathrm{d} \vec{F}=I \mathrm{d}\vec{l} \times \vec{B}$$
#### 磁感应强度：
$$d \vec{B}= \frac{\mu_{0}}{4\pi} \frac{I\mathrm{d}\vec{l}\times\vec{R}}{R^3}$$
积分形式为：$$B(\vec{r})=\frac{\mu_{0}}{4\pi} \oint_{c} \frac{I\mathrm{d}\vec{l}\times\vec{R}}{R^3}$$
用两个电流密度可写为：$$B(\vec{r})= \frac{\mu_{0}}{4\pi}\int_{V} \frac{J(\vec{r'})\times\vec{R}}{R^3}\mathrm{d}V$$$$B(\vec{r})= \frac{\mu_{0}}{4\pi}\int_{S} \frac{J_{S}(\vec{r'})\times\vec{R}}{R^3}\mathrm{d}S'$$
#### 恒定磁场的散度：
$$\nabla \cdot B(\vec{r})=0$$
磁通连续性定理：$$\oint_{S}B(\vec{r})\cdot \mathrm{d}S=\int_{V}\nabla \cdot B(\vec{r})=0$$
#### 恒定磁场的旋度：
$$\nabla\times B(\vec{r})=\mu_{0}J(\vec{r})$$
安培环路定理：$$\oint_{C}B(\vec{r})\cdot \mathrm{d} \vec{l}=\mu_{0}I$$
### 媒质的电磁特性
#### 极化强度矢量：
$$\vec{P}=\lim_{ \Delta V \to 0 } \frac{\sum_{i}\vec{p_{i}}}{\Delta V} $$
式中的 $\vec{p_{i}}=q_{i}\vec{l_{i}}$ 为体积 $\Delta V$ 中的第 $i$ 个分子的电偶极矩

从闭合面 $S$ 穿出的极化电荷为$$\oint_{S}\vec{P}\cdot \mathrm{d}\vec{S}$$
则留在闭合面 $S$ 中的极化电荷量为：$$q_{p}=-\oint_{S}\vec{P}\cdot \mathrm{d} \vec{S}=-\int_{v} \nabla \cdot \vec{P}\mathrm{d} V$$
故 $S$ 限定的体积 $V$ 内的极化电荷体密度为：$$\rho_{p}=-\nabla \cdot \vec{P}$$
电介质表面上的极化电荷面密度为：$$\rho_{sp}=\vec{P}\cdot \vec{e_{n}}$$
#### 电介质中静电场的基本方程：
真空中的高斯定理：$$\oint_{S} \vec{E} \cdot \mathrm{d} \vec{S}= \frac{1}{\varepsilon_{0}}(q+q_{p})$$
电位移矢量： $$\vec{D}=\varepsilon_{0} \vec{E}+\vec{P}$$
电介质中的高斯定理的积分形式（与极化电荷无关）： $$\oint_{S} \vec{D}\cdot \mathrm{d} \vec{S}=q$$
电介质中的高斯定理的微分形式：$$\nabla \cdot  \vec{D}=\rho$$
式中 $\rho$ 为自由电荷体密度

#### 电介质中静电场基本方程汇总：
$$\begin{cases}\oint_{S}\vec{D}\cdot \mathrm{d} \vec{S}=q  \\
\\\oint_{C} \vec{E}\cdot \mathrm{d}\vec{l}=0\end{cases}$$
微分形式为： $$\begin{cases}\nabla \cdot  \vec{D}=\rho  
\\\nabla\times \vec{E}=0\end{cases}$$
#### 电介质的本构关系
$$\vec{P}= \varepsilon_{0} \chi_{e} \vec{E}$$
式中 $\chi_{e}$ 称为电介质的电极化率

#### 对于各向同性介质
 $$\vec{D}=\varepsilon_{0} \vec{E}+\chi_{e} \varepsilon_{0} \vec{E}=(1+\chi_{e})\varepsilon_{0} \vec{E} =\varepsilon_{r}\varepsilon_{0} \vec{E}=\varepsilon \vec{E}$$
#### 磁介质的磁化
$$\vec{J_{M}}=\nabla \times \vec{M}$$
式中 $J_{M}$ 表示的是磁化电流密度， $M$ 表示为磁化强度

磁化面电流密度矢量：$$\vec{J_{SM}}= \vec{M} \times  \vec{e_{n}}$$
#### 磁介质中恒定磁场的基本方程
磁介质中安培环路定理的积分形式：$$\oint \vec{H} \cdot \mathrm{d}  \vec{l}=I$$
微分形式为($J$ 为该点的传导电流密度): $$\nabla \times  \vec{H}= \vec{J}$$
恒定磁场基本方程的积分形式为：$$\begin{cases}\oint \vec{B}\cdot \mathrm{d}  \vec{S}=0 \\ \oint \vec{H} \cdot \mathrm{d} \vec{l} =I\end{cases}$$
微分形式为： $$\begin{cases}\nabla \cdot  \vec{B}=0\\\nabla \times  \vec{H} = \vec{J}\end{cases}$$
#### 磁介质的本构关系：
磁化强度 $M$ 与磁场强度 $H$ 成正比，表示为： $$\vec{M}= \chi_{m}  \vec{H}$$
各向同性磁介质的本构关系：$$\vec{B}=(1 + \chi_{m}) \mu_{0}  \vec{H}= \mu_{r} \mu_{0}  \vec{H}=\mu \vec{H}$$
#### 导电媒质的传导特性
在线性和各向同性的导电媒质内，任意一点的电流密度矢量 $J$ 与该点的电场强度 $E$ 成正比，表示为:$$\vec{J}= \sigma  \vec{E}$$
式中 $\sigma$ 为媒质的电导率

单位体积的损耗功率(即损耗功率密度)为:$$p_{L}= \vec{J} \cdot  \vec{E}$$
整个体积 $V$ 中的导电媒质消耗的功率为：$$P_{L}=\int_{V} \sigma E^2 \mathrm{d}V$$
### 电磁感应定律和位移电流
#### 法拉第电磁感应定律
感应电动势为：$$\xi_{in}=- \frac{\mathrm{d}}{\mathrm{d}t} \int_{S} \vec{B} \cdot \mathrm{d}  \vec{S}$$
积分形式为： $$\oint_{C} \vec{E} \cdot \mathrm{d}  \vec{l}=- \int_{S} \frac{\partial\vec{B}}{\partial t} \cdot \mathrm{d} \vec{S}$$
微分形式为：$$\nabla \times  \vec{E}=- \frac{\partial\vec{B}}{\partial t}$$
当回路在时变磁场中以速度 $v$ 运动时，回路中的感应电动势为：$$\oint \vec{E}\cdot \mathrm{d} \vec{l}=-\int_{S} \frac{\partial\vec{B}}{\partial t} \cdot \mathrm{d}  \vec{S} + \oint_{C} ( \vec{v} \times \vec{B})\cdot \mathrm{d} \vec{l}$$
相对应的微分形式为:$$\nabla \times  \vec{E}=- \frac{\partial\vec{B}}{\partial t}+\nabla \times (\vec{v} \times \vec{B})$$
#### 位移电流
用 $J_{d}$ 表示位移电流密度： $$\vec{J_{d}}= \frac{\partial\vec{D}}{\partial t}$$
时变电磁场中安培环路定理的积分形式为：$$\oint_{C} \vec{H}\cdot \mathrm{d}  \vec{l}=\int_{S}\left( \vec{J}+ \frac{\partial\vec{D}}{\partial t}\right)\cdot \mathrm{d} \vec{S}$$
### 麦克斯韦方程组
#### 积分形式
$$\oint_{C} \vec{H} \cdot \mathrm{d}  \vec{l}=\int_{S} \vec{J} \cdot \mathrm{d}  \vec{S}+\int_{S} \frac{\partial\vec{D}}{\partial t} \cdot \mathrm{d} \vec{S}$$$$\oint_{C} \vec{E} \cdot \mathrm{d}  \vec{l}=-\int_{S} \frac{\partial\vec{B}}{\partial t} \cdot \mathrm{d} \vec{S}$$$$\oint_{S} \vec{B} \cdot \mathrm{d} \vec{S}=0$$$$\oint_{S} \vec{D} \mathrm{d} \vec{S}=\int_{V} \rho \mathrm{d} V$$
#### 微分形式
$$\nabla \times  \vec{H}=\vec{J}+ \frac{\partial\vec{D}}{\partial t} \tag{1}$$$$\nabla \times  \vec{E}=- \frac{\partial\vec{B}}{\partial t} \tag{2}$$$$\nabla \cdot  \vec{B}=0 \tag{3}$$$$\nabla \cdot  \vec{D}=\rho \tag{4}$$
式(1) 表明传导电流和时变电场要产生磁场，都是磁场的涡旋源

式(2) 表明时变电场要产生电场，是电场的涡旋源

式(3) 表明磁场是无散场，磁感应线都是闭合线

式(4) 表明电荷要产生电场，是电场的散度源

#### 媒质的本构关系
在线性，各向同性的导电媒质中，本构关系为：$$\vec{D}= \sigma  \vec{E}$$$$\vec{B}=\mu  \vec{H}$$$$\vec{J}=\sigma  \vec{E}$$
### 电磁场的边界条件
#### 磁场强度矢量$\vec{H}$的边界条件
$$\vec{e_{n}}\times ( \vec{H}_{1}-  \vec{H_{2}})=0 或 H_{1t} - H_{2t}=0$$
这表明，在电导率为有限值的两种媒质分界面上，磁场强度 $\vec{H}$ 的切向分量是连续的
#### 电场强度矢量 $\vec{E}$ 的边界条件
$$\vec{e_{n}}\times (\vec{E_{1}}-  \vec{E_{2}})=0$$
由此可见，无论分界面上是否存在面分布电荷，两侧的电场强度矢量 $\vec{E}$ 的切向分量总是连续的
#### 磁感应强度矢量 $B$ 的边界条件
$$\vec{e_{n}}\cdot (\vec{B_{1}}-  \vec{B_{2}})=0 或 B_{1n}=B_{2n}$$
由此可见，无论分界面上是否存在面电流，磁感应强度的法向向量在分解面上总是连续的
#### 电位移矢量 $\vec{D}$ 的边界条件
$$\vec{e_{n}}\cdot(\vec{D_{1}}-  \vec{D_{2}})=\rho_{S} 或 D_{1n}-D_{2n}=\rho_{S}$$
由此可见，分界面上存在自由电荷面密度时，电位移矢量的法向向量在分界面上是不连续的
#### 理想介质分界面上的边界条件
将电导率很低的介质称为理想介质，边界条件为：$$\vec{e_{n}}\times ( \vec{H}_{1}-  \vec{H_{2}})=0 $$$$\vec{e_{n}}\times (\vec{E_{1}}-  \vec{E_{2}})=0$$$$\vec{e_{n}}\cdot (\vec{B_{1}}-  \vec{B_{2}})=0 $$$$\vec{e_{n}}\cdot(\vec{D_{1}}-  \vec{D_{2}})=0$$
# 量子力学
## 第一章 绪论
### 普朗克黑体辐射公式
$$\rho_{\nu}\mathrm{d}\nu=\frac{8\pi h\nu^3}{c^3}\cdot \frac{1}{e^{h\nu/k_{B}T}-1}\mathrm{d}\nu$$
式中, $\rho_{\nu }$ 是黑体内频率在 $\nu$ 到 $\nu +\mathrm{d}\nu$ 之间的辐射能量密度, $c$ 是光速 ,$k_{B}$ 是玻尔兹曼常数，$T$ 是黑体的热力学温度
### 光电效应方程
$$\frac{1}{2}m_{e}\nu_{m}^2=h\nu - W_{0}$$
### 康普顿效应
$$\Delta \lambda =\lambda'-\lambda=\frac{4\pi\hbar}{m_{0}c}\sin^2 \frac{\theta}{2}=\frac{h}{m_{0}c}(1-\cos \theta)=\lambda_{c}(1-\cos \theta)$$
式中 $\lambda_{c}$ 为康普顿波长
### 巴耳末公式
$$\nu=R_{\infty}c\left( \frac{1}{n'^2}-\frac{1}{n^2} \right)$$
并且得出 $R_{\infty}=\frac{m_{e}e^4}{8 \varepsilon_{0}^2h^3c}$
### 玻尔-索末菲量子化条件
$$\oint p \mathrm{d} q=\left( n + \frac{1}{2} \right)h$$
q是电子的一个广义坐标，p对应的是广义动量，n为量子数
### 德布罗意关系
$$E=h\nu=\hbar \omega$$$$\vec{p}=\frac{h}{\nu} \vec{e_{k}}=\hbar  \vec{k}$$
### 德布罗意波长公式
$$\lambda=\frac{h}{p}=\frac{h}{\sqrt{ 2mE }}$$
## 第二章  波函数与薛定谔方程
### 波函数的统计解释
波函数在空间中某一点的强度(振幅绝对值的平方)和在该点找到粒子的概率成比例
#### 波函数的归一化条件
$$\int_{\infty}|\Psi(\vec{r},t)|^2 \mathrm{d}V=\int_{\infty}\Psi(\vec{r},t)\cdot \Psi^\ast(\vec{r},t)\mathrm{d}V=1$$
#### 自由粒子的波函数
$$\Psi_{p}(\vec{r},t)=A e^{\mathrm{i}/\hbar(\vec{p}\cdot \vec{r}-Et)}$$
### 态叠加原理
#### 证明任何一个波函数 $\vec{\Psi}(\vec{r},t)$ 都可以看成各种不同动量的平面波的叠加，换句话说，任何波函数 $\vec{\Psi})(\vec{r},t)$ 都可以写成如下形式：$$\Psi(\vec{r},t)=\iiint_{-\infty}^{\infty}c(\vec{p},t)\Psi_{p}(\vec{r})\mathrm{d}p_{x}\mathrm{d}p_{y}\mathrm{d}p_{z}$$式中：$$\Psi_{p}(\vec{r})\equiv\frac{1}{(2\pi \hbar)^{3/2}}e^{\frac{\mathrm{i}}{\hbar}  \vec{p}\cdot  \vec{r}}$$
$$\int\Psi_{p'}^{\ast}\Psi(\vec{r},t)\mathrm{d} \vec{r}=\int_{-\infty}^{\infty}c(\vec{p},t)\left[ \int_{-\infty}^{\infty} \Psi_{p'}^{\ast}\Psi(\vec{r},t)\mathrm{d} \vec{r}\right]\mathrm{d} \vec{p} $$
$$=\int_{-\infty}^{\infty}c(\vec{p},t)\left[ \frac{1}{(2\pi \hbar)^3} \int_{-\infty}^{\infty}e^{\frac{\mathrm{i}}{\hbar}(\vec{p}- \vec{p'})\cdot  \vec{r}}\mathrm{d}  \vec{r} \right] \mathrm{d}  \vec{p}$$
$$=\int_{-\infty}^{\infty}c(\vec{p},t) \delta(\vec{p}-\vec{p'})\mathrm{d} \vec{p}=c(\vec{p'},t)$$
### 薛定谔方程
$$\mathrm{i}\hbar \frac{\partial\Psi}{\partial t}=- \frac{\hbar^2}{2m} \nabla^2 \Psi+U(\vec{r})\Psi$$
式中 $U(\vec{r})$ 为粒子在力场中的势能
### 粒子流密度和粒子数守恒定律
$\vec{J}$ 为概率流密度矢量，令 $J\equiv \frac{\mathrm{i}\hbar}{2m}(\Psi \nabla\Psi^{\ast}-\Psi^{\ast} \nabla\Psi)$，则粒子数守恒定律的微分形式为：$$\frac{\partial\omega}{\partial t}+\nabla \cdot  \vec{J}=0$$
#### 波函数的标准化条件
有限性，连续性和导致可测量的单值性

### 定态薛定谔方程
定态薛定谔方程：$$- \frac{\hbar^2}{2m}\nabla^2\Psi+U(\vec{r})\Psi=E\Psi$$
定态波函数可表示为$$\Psi(\vec{r},t)=\Psi(\vec{r})e^{-\frac{\mathrm{i}E}{\hbar}t}$$
经典力学中哈密顿函数为：$$E=\frac{p^2}{2m}+U(\vec{r})$$
则哈密顿算符可表示为$$\hat{H}=- \frac{\hbar^2}{2m}\nabla^2 + U(\vec{r})$$
则定态薛定谔方程可写为：$$\hat{H}\Psi=E\Psi$$
这种类型的方程称为本征值方程，$E$ 称为算符 $\hat{H}$ 的本征值，$\Psi$ 称为算符 $\hat{H}$ 属于本征值 $E$ 的本征函数
### 一维无限深方势阱
考虑在一维空间中运动的粒子，它的势能在一定区域内(-a<x<a)为0，而在此区域外的势能为无限大，即$$\begin{cases}U(x)=0,|x|<a\\U(x)=\infty,|x|>a\end{cases}$$
这种势称为一维无限深方势阱

一维无限深方势阱中粒子的定态波函数为：$$\Psi_{n}(x,t)=\Psi_{n}(x)e^{- \frac{\mathrm{i}}{\hbar}E_{n}t}=A'\sin \frac{n\pi}{2a}(x+a)e^{- \frac{\mathrm{i}}{\hbar}E_{n}t}$$
### 线性谐振子
如果在一维空间内运动的粒子的势能为抛物线形式:$U(x)=ax^2(a>0)$ ，则这种体系就称为线性谐振子

本征值:$$E_{n}=\hbar \omega\left( n+\frac{1}{2} \right)$$
本征函数：$$\psi_{n}(x)=\left( \frac{\alpha}{\pi^{\frac{1}{2}}2^n n!} \right)^{\frac{1}{2}}e^{-\frac{1}{2}\alpha^2x^2}H_{n}(\alpha x)$$$$\alpha = \sqrt{ m\omega }$$

