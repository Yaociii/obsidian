---
日期: 2026-08-05
tags:
  - cg
  - Games101
  - rendering
---
这两节主要介绍了MVP矩阵变换，其作用为将模型世界的三维坐标转换至一个$[-1,1]^3$的规范立方体（canonical cube）以便后续映射到屏幕上

# 要做哪些变换

- Model变换将模型自身的坐标转换到世界坐标，包含拉伸（scale）、旋转（rotate）、平移（translate）三个步骤
- View/Camera变换将相机坐标变换到世界原点，从相机进行观察
- Projection变换将相机看到的世界投影到最终的$[-1,1]^3$的规范立方体中，投影方式分为正交投影（Orthographic Projection）与透视投影（Perspective Projection）
注意，各种变换的本质都应是线性运算，应遵循$$x'=Ax$$
其中x'、x为向量，A为矩阵

# 齐次坐标（Homogeneous Coordinates） 

我们知道空间中的移动包含旋转与平移，旋转操作对应的是一个正交矩阵，是一种性质良好的线性变换，可平移呢？
以一个二维空间中的点举例，如果它经历了旋转与平移，我们很自然的知道可以这样表示
$$x'=Ax+t$$
其中t是表示平移量的二维向量
这种变换称为仿射变换（Affine Transformation）,是线性变换与平移的组合，很显然它不是我们前面讨论的线性变换

为了将仿射变换统一为一种线性变换，我们引入齐次坐标的概念，其思路是增加一个维度用于表示平移量
如原先二维空间中的点$x=\begin{bmatrix}a\\b\end{bmatrix}$在齐次坐标下表示为$x=\begin{bmatrix}a\\b\\1\end{bmatrix}$，而变换矩阵$A=\begin{bmatrix}a & c\\ b & d\end{bmatrix}$在齐次坐标下表示为$A=\begin{bmatrix}a&c&0\\b&d&0\\0&0&1\end{bmatrix}$
通过这样的升维，我们自然的发现仿射变换可以表示为这样一个线性变换
$$x'=A'x$$
其中A'=$\begin{bmatrix}a&c&t_x\\b&d&t_y\\0&0&1\end{bmatrix}$，表示平移的量也被统一与变换矩阵中  
同时，由此可知齐次坐标下，向量增加的维度的分量为0
$$\vec{v}=x_1-x_2=\begin{bmatrix}a_1\\b_1\\1\end{bmatrix}-\begin{bmatrix}a_2\\b_2\\1\end{bmatrix}=\begin{bmatrix}a_1-a_2\\{b_1-b_2}\\0\end{bmatrix}$$
那对于一个点来说，如果其增加维度的分量不为1意味着什么呢？
我们规定对于任何一个点$x=\begin{bmatrix}a\\b\\w\end{bmatrix}$,其实际表示的是$x=\begin{bmatrix}\dfrac{a}{w}\\\dfrac{b}{w}\\1\end{bmatrix}$
比如考虑这样一个点：
$$x^*=x_1+x_2=\begin{bmatrix}a_1\\b_1\\1\end{bmatrix}+\begin{bmatrix}a_2\\b_2\\1\end{bmatrix}=\begin{bmatrix}a_1+a_2\\b_1+b_2\\2\end{bmatrix}$$
我们将其w维度分量归一化，$x^*=\begin{bmatrix}\dfrac{a_1+a_2}{2}\\\dfrac{b_1+b_2}{2}\\1\end{bmatrix}$,显然其实际表示的是$x_1与x_2$的中点坐标
这种归一化的思想会在后续透视除法中发挥巨大作用


# 基础变换

### 缩放（scale）

缩放矩阵$S=\begin{bmatrix}S_{x} & 0 & 0 & 0 \\  0 &  S_{y} &0 & 0 \\  0 & 0 & S_{z} & 0 \\  0 & 0 & 0 & 1 \end{bmatrix}$ 
	其将x轴缩短为原来的$\frac{1}{S_{x}}$倍,即将x轴分量拉长到原来的$S_{x}$倍，其余反向与之相似  

### 旋转（rotate）
旋转矩阵$R=R_{z}R_{y}R_{x}$,是分别绕三个轴的旋转矩阵的乘积
我们知道，在平面直角坐标系下绕原点逆时针旋转$\alpha$对应的矩阵为
$$
	R=\begin{bmatrix}
	\cos\alpha & -\cos\alpha \\
	\sin\alpha & \cos\alpha
	\end{bmatrix}
$$
我们规定$\alpha$是以绕轴的右手螺旋方向为正对应的旋转角度，由此可得
$$
	R_{x}=\begin{bmatrix}
	1 & 0 & 0 & 0 \\
	0 & \cos\alpha & -\sin\alpha & 0 \\
	0 & \sin\alpha & \cos\alpha & 0 \\
	0 & 0 & 0 & 1
	\end{bmatrix}
	\quad
	R_{y}=\begin{bmatrix}
	\cos\alpha & 0 & \sin\alpha & 0 \\
	0 & 1 & 0 & 0 \\
	-\sin\alpha & 0 & \cos\alpha & 0 \\
	0 & 0 & 0 & 1
	\end{bmatrix}
	\quad
	R_{z}=\begin{bmatrix}
	\cos\alpha & -\sin\alpha & 0 & 0 \\
	\sin\alpha & \cos\alpha & 0 & 0 \\
	0 & 0 & 1 & 0 \\
	0 & 0 & 0 & 1
	\end{bmatrix}
$$
需要强调的是，在不涉及旋转的分量上应该置1保证不改变其值

#### 旋转矩阵的理解
我们从矩阵乘法的两种视角出发分别解释旋转矩阵
我们所做的变换为$x'=Ax$,记$A=\begin{bmatrix}a & d & g & 0 \\  b & e & h & 0 \\  c & f & i & 0 \\  0 & 0 & 0 & 1\end{bmatrix}$


从方程组的角度，即行的角度来看，这个变换的含义是
$$
\begin{cases}
x'_{x}=ax_{x}+dx_{y}+gx_{z} \\
x'_{y}=bx_{x}+ex_{y}+hx_{z} \\
x'_{z}=cx_{x}+fx_{x}+ix_{z}
\end{cases}
$$
我们找一组已知的点并进行旋转，就可以用待定系数法求出矩阵A的各个参数（这种待定系数法的思想在后续推导中也很重要）



从线性组合（坐标轴）的角度，即列的角度来看，矩阵的每一列表示的是空间中坐标轴正方向的同向向量，其模长表示的是该轴被压缩的倍数
以单位矩阵I为例，$x=Ix$其实可以理解为x轴对应分量为$\vec{x}=\begin{bmatrix}1 \\  0 \\  0\end{bmatrix}$,故变换后$x_{x}=x_{x}$
类比缩放矩阵，就可以知道其相当于x轴对应分量为$\vec{x}=\begin{bmatrix}S_{x} \\  0 \\  0\end{bmatrix}$,故x轴缩短为原来的$\frac{1}{S_{x}}$(等价于x分量放大为原来的$S_{x}$倍)
由此出发，旋转矩阵可以理解为将原先的坐标轴逆时针旋转$\alpha$后得到新坐标轴，$x'$是坐标系整体旋转（x旋转为x'）后在原坐标系下的坐标，从这个角度来讲，x是在变换得到的新坐标系下的坐标，而x'才是变换前旧坐标系下的坐标，变换公式可以写成
$$
	x_{旧}=Ax_{新}
$$


### 平移（translate）

得益于齐次坐标的引入，我们可以很方便的写出平移矩阵
$$
	T=\begin{bmatrix}
	1 & 0 & 0 & t_{x} \\
	0 & 1 & 0 & t_{y} \\
	0 & 0 & 1 & t_{z} \\
	0 & 0 & 0 & 1
	\end{bmatrix}
$$
需要注意主对角线上元素都应置1以保证其他属性不受影响


### 拓展部分

#### 1.绕任一点旋转

先将该点移动到原点，再绕原点旋转，最后把该点移回原处即可
即$x'=T(c)\cdot S \cdot T (-c)\cdot x$
注意矩阵运算不可交换，实际的操作顺序应当从右往左看


#### 2.罗德里格斯旋转公式

该公式为绕任意过原点的轴$\vec{n}$以右手螺旋方向旋转$\alpha$度的旋转公式，$\vec{n}$应为单位向量
对应的旋转矩阵为
$$
	R(\vec{n},\alpha)=I\cdot\cos\alpha+(1-\cos\alpha)\vec{n}{n^{T}}+N\cdot\sin\alpha
$$
其中，$I$为单位矩阵，$N$为向量的对偶矩阵，有$\vec{n}\times \vec{x}=N\cdot \vec{x}$,即$N=\begin{bmatrix}0 & -n_{z} & n_{y} \\  n_{z} &0 & -n_{x} \\  -n_{y} & n_{x} & 0 \end{bmatrix}$
其推导的基本思路为将被旋转向量分解为平行转轴与垂直转轴两个方向，平行方向不变，而垂直方向绕轴旋转$\alpha$
![[de5e0c3e7c6e5a364b15e377ca977761.jpg]]
其中$\vec{v}为被旋转向量，\vec{k}为所选转轴$


综合拓展1与拓展2,我们可以轻松得到绕空间中过任意点的任意转轴旋转任意角度的旋转公式



# Model变换

简记Model变换矩阵为M（区别于矩阵Matrix缩写的M）,$$M=T\cdot R\cdot S$$
T，R，S即前面所提的平移、旋转、拉伸变换，注意三者顺序不可改变，从右往左，先拉伸，再旋转，最后平移


# View/Camera变换

#### 相机的定义
相机是观察世界的眼睛，先定义相机的位置$\vec{e}$(即相对原点的位移)，方向$\vec{g}$(Look_at/target/gaze，有很多不同的称呼方式，实际指的是相机的正对方向)，上方向$\vec{t}$(以人眼类比，上方向即头顶所指方向。想象一个相机，你可以在不改变$\vec{g}$的前提下在垂直$\vec{g}$的平面内360度旋转$\vec{t}$)
此外还要知道相机的观察方式（正交投影or透视投影，这点让我联想到三体里云天明针眼画师的童话）与对应的参数（能看多近，能看多远，能看多宽多高）


#### 将相机移到世界原点
我们的目标是从相机的角度观察世界，但所有的物体坐标都是相对原点的，为解决这个问题，我们参照拓展1的思路，将相机移动到原点，同时将上方向$\vec{t}$对准y轴正方向，将$\vec{g}$对准z轴负方向（**注意是负方向！！！**），而由$\vec{g}\times \vec{t}$确定的轴对准x轴正方向
这个变换可以理解为对相机的Model变换$M=T\cdot R$（相机本身不会拉伸，即$S=I$）的逆变换,记该变换为V,即
$$
	V=M^{-1}=(T\cdot R)^{-1}=R^{-1}\cdot T^{-1}
$$
因为R是正交矩阵，故$R^{-1}=R^T$,即
$$
	由R=\begin{bmatrix}
	{\vec{g}\times \vec{t}}_{x} & t_{x} & -g_{x} & 0 \\
	{\vec{g}\times \vec{t}}_{y} & t_{y} & -g_{y} & 0 \\ 
	{\vec{g}\times \vec{t}}_{z} & t_{z} & -g_{z} & 0 \\ \\
	0 & 0 & 0 & 1
	\end{bmatrix}
	可得\quad
	R^{-1}=\begin{bmatrix}
	
	\end{bmatrix}
$$
