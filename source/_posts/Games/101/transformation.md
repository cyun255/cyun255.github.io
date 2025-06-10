---
title: 计算机图形学入门 | 变换
date: 2024-08-29
comments: false
categories: [计算机图形学, 入门]
tags:
    - 图形学
    - 线性变换
    - 正交矩阵
description: |
---

## 2D线性变换

我们可以使用一个 $2\times2$ 的矩阵乘以一个二维向量，得到一个新的二维向量

$$
\begin{gathered}
\begin{bmatrix}
a_{11} & a_{12} \\\\
a_{21} & a_{22}
\end{bmatrix}
\begin{bmatrix}
x \\\\ y
\end{bmatrix}=
\begin{bmatrix}
a_{11}x + a_{12}y \\\\
a_{21}x + a_{22}y
\end{bmatrix}
\end{gathered}
$$

通过这种简单的公式可以实现许多实用的变换，包括缩放、切变、旋转、镜像等


### 缩放

缩放是最简单的变换操作，只需要沿着坐标轴乘以指定的倍数即可，构造一个 $2\times2$ 的方阵，即：

$$
\begin{gathered}
scale(s_x, s_y) =
\begin{bmatrix}
s_x & 0 \\\\
0 & s_y
\end{bmatrix}
\end{gathered}
$$

通过这个矩阵就能将指定坐标缩放至任意倍大小

$$
\begin{gathered}
\begin{bmatrix}
s_x & 0 \\\\
0 & s_y
\end{bmatrix}
\begin{bmatrix}
x \\\\ y
\end{bmatrix}
=
\begin{bmatrix}
s_xx \\\\ s_yy
\end{bmatrix}
\end{gathered}
$$

例如，将图片 $x$ 轴与 $y$ 轴统一缩小一倍得到：

![](https://images.rescld.cn/image-20240827142322852.png)

当 $x$ 轴 与 $y$ 轴缩放比例不同时：

![](https://images.rescld.cn/image-20240827143018066.png)

### 切变

切变是一种特殊的平移，即其中一条边平行于坐标轴进行平移（将矩形变为的平行四边形）。随着远离原点，偏移量也会随之增大。水平或垂直切变矩阵：

$$
\begin{gathered}
shear-x(s)=
\begin{bmatrix}
1 & s \\\\
0 & 1
\end{bmatrix},\quad
shear-y(s)=
\begin{bmatrix}
1 & 0 \\\\
s & 1
\end{bmatrix}
\end{gathered}
$$


切变也可以理解成只考虑一个坐标轴的旋转，通过旋转角度的 $tan$ 值来确定 $s$ ：

$$
\begin{gathered}
\begin{bmatrix}
1 & \tan\phi \\\\
0 & 1
\end{bmatrix}
\quad or \quad
\begin{bmatrix}
1 & 0 \\\\
\tan\phi & 1
\end{bmatrix}
\end{gathered}
$$

例如，沿着水平方向向右切变 $45^\circ$ ，构造矩阵：

$$
\begin{gathered}
shear-x(s)=
\begin{bmatrix}
1 & 1 \\\\
0 & 1
\end{bmatrix}
\end{gathered}
$$

![](https://images.rescld.cn/image-20240827155805160.png)

沿着垂直方向向上切变 $45^\circ$ 也是同理：

$$
\begin{gathered}
shear-y(s)=
\begin{bmatrix}
1 & 0 \\\\
1 & 1
\end{bmatrix}
\end{gathered}
$$

![](https://images.rescld.cn/image-20240827160041627.png)

### 旋转

假设，我们要将向量 $a$ 旋转到向量 $b$ 的位置，已知向量 $a$ 与 $x$ 轴的夹角为 $\alpha$ ，向量长度为 $r = \sqrt{x_a^2 + y_a^2}$ ，得到各个点的位置信息：

$$
\begin{gathered}
x_a = r\cos\alpha \\\\
y_a = r\sin\alpha
\end{gathered}
$$

![](https://images.rescld.cn/image-20240827164209478.png)

因为向量 $b$ 是由向量 $a$ 旋转角度 $\phi$ 得到，所以向量大小不变。使用三角恒等式展开得到：

$$
\begin{gathered}
x_b = r\cos(\alpha + \phi) = r\cos\alpha\cos\phi-r\sin\alpha\sin\phi \\\\
y_b = r\sin(\alpha + \phi) = r\sin\alpha\cos\phi+r\cos\alpha\sin\phi
\end{gathered}
$$

再通过 $x_a$ 与 $y_a$ 将公式简化一下：

$$
\begin{gathered}
x_b = x_a\cos\phi - y_a\sin\phi \\\\
y_b = y_a\cos\phi + x_a\sin\phi
\end{gathered}
$$

构造二维线性变换矩阵：

$$
\begin{gathered}
rotate(\phi)=
\begin{bmatrix}
\cos\phi & -\sin\phi \\\\
\sin\phi & \phantom{-}\cos\phi
\end{bmatrix}
\end{gathered}
$$

例如，使用将一个图像旋转 $45^\circ$ ，需要构造矩阵：

$$
\begin{gathered}
\begin{bmatrix}
\cos\frac{\pi}{4} & -\sin\frac{pi}{4} \\\\
\sin\frac{\pi}{4} & \phantom{-}\cos\frac{pi}{4}
\end{bmatrix}
=
\begin{bmatrix}
0.707 & -0.707 \\\\
0.707 & \phantom{-}0.707
\end{bmatrix}
\end{gathered}
$$

![](https://images.rescld.cn/image-20240827171035618.png)

### 镜像

将图像以坐标轴为对称轴进行反射，只需要将除对称轴之外的其他坐标全都取相反数即可

$$
\begin{gathered}
reflect-y=
\begin{bmatrix}
-1 & 0 \\\\
\phantom{-}0 & 1
\end{bmatrix}
,\quad
reflect-x=
\begin{bmatrix}
1 & \phantom{-}0 \\\\
0 & -1
\end{bmatrix}
\end{gathered}
$$

![](https://images.rescld.cn/image-20240827184405328.png)

### 复合运算

通常我们会需要对一个图像进行多次变换，例如，先将图像缩放一次，再进行旋转一次

$$
\begin{gathered}
first & v_2 = Sv_1, \\\\
then & v_3 = Rv_2
\end{gathered}
$$

通过乘法结合律，可以将结果合并为：

$$
v3 = R(Sv_1) = (RS)v_1
$$
由此可知，我们能将两个操作合并为一个最终的变换矩阵：
$$
M = RS
$$

假设，我们需要将一个图像在垂直方向上缩小一倍，然后再旋转 $45^\circ$，计算得出最终的变换矩阵：

$$
\begin{gathered}
\begin{bmatrix}
0.707 & -0.707 \\\\
0.707 & \phantom{-}0.707
\end{bmatrix}
\begin{bmatrix}
1 & 0 \\\\
0 & 0.5
\end{bmatrix}
=
\begin{bmatrix}
0.707 & -0.353 \\\\
0.707 & \phantom{-}0.353
\end{bmatrix}
\end{gathered}
$$

![](https://images.rescld.cn/image-20240827213038031.png)

> **特别注意**：矩阵 $M=RS$ 的计算顺序是*右侧优先*，即先计算缩放 $S$，再计算旋转 $R$。这个顺序非常重要，因为矩阵并不满足交换律，不同的运算顺序会产生不同的结果！！

$$
\begin{gathered}
\begin{bmatrix}
1 & 0 \\\\
0 & 0.5
\end{bmatrix}
\begin{bmatrix}
0.707 & -0.707 \\\\
0.707 & \phantom{-}0.707
\end{bmatrix}
=\begin{bmatrix}
0.707 & -0.707 \\\\
0.353 & \phantom{-}0.353
\end{bmatrix}
\end{gathered}
$$

![](https://images.rescld.cn/image-20240827214152373.png)

## 3D线性变换

3D变换只是在2D的基础上拓展一个 $Z$ 轴，例如，缩放矩阵多一个维度：

$$
\begin{gathered}
scale(s_x, s_y, s_z) = 
\begin{bmatrix}
s_x & 0 & 0 \\\\
0 & s_y & 0 \\\\
0 & 0 & s_z
\end{bmatrix}
\end{gathered}
$$
3D旋转会比2D复杂得多，但是我们依然可以在2D的基础上加上一个 $Z$ 实现简单的轴对称旋转：
$$
\begin{gathered}
rotate-z=\begin{bmatrix}
\cos\phi & -\sin\phi & 0 \\\\
\sin\phi & \phantom{-}\cos\phi & 0 \\\\
0 & 0 & 1
\end{bmatrix}
\end{gathered}
$$

对于 $X$ 轴与 $Y$ 轴也是相同的做法：

$$
\begin{gathered}
rotate-x=\begin{bmatrix}
1 & 0 & 0 \\\\
0 & \cos\phi & -\sin\phi \\\\
0 & \sin\phi & \phantom{-}\cos\phi \\\\
\end{bmatrix},\quad
rotate-y=\begin{bmatrix}
\phantom{-}\cos\phi & 0 & \sin\phi \\\\
0 & 1 & 0 \\\\
-\sin\phi & 0 & \cos\phi \\\\
\end{bmatrix}
\end{gathered}
$$

3D切变与2D类似，沿着其中一条轴线平移即可：

$$
\begin{gathered}
shear-x(d_y, d_z)=
\begin{bmatrix}
1 & d_y & d_z \\\\
0 & 1 & 0 \\\\
0 & 0 & 1
\end{bmatrix}
\end{gathered}
$$

### 任意向量旋转

与2D旋转相同，3D旋转矩阵也是一个正交矩阵，这意味矩阵的三行分别是三个相互正交的单位向量：

$$
\begin{gathered}
R_{uvw}=
\begin{bmatrix}
x_u & y_u & z_u \\\\
x_v & y_v & z_v \\\\
x_w & y_w & z_w
\end{bmatrix}
\end{gathered}
$$

假设，三个单位矩阵分别为 $\textbf{u, v}$ 和 $\textbf{w}$，由于相互正交：

$$
\begin{gathered}
\textbf{u}\cdot \textbf{u} = \textbf{v}\cdot \textbf{v} = \textbf{w}\cdot \textbf{w} = 1 \\\\
\textbf{u}\cdot \textbf{v} = \textbf{v}\cdot \textbf{w} = \textbf{w}\cdot \textbf{u} = 0
\end{gathered}
$$

若我们将旋转矩阵左乘单位矩阵 $\textbf{u}$，将得到：

$$
\begin{gathered}
R_{uvw}\textbf{u}=
\begin{bmatrix}
x_u & y_u & z_u \\\\
x_v & y_v & z_v \\\\
x_w & y_w & z_w
\end{bmatrix}
\begin{bmatrix}
x_u \\\\ y_u \\\\ z_u
\end{bmatrix}
=\begin{bmatrix}
x_ux_u + y_uy_u + z_uz_u \\\\
x_vx_u + y_vy_u + z_vz_u \\\\
x_wx_u + y_wy_u + z_wz_u
\end{bmatrix}
\end{gathered}
$$

观察结果矩阵，发现其与单位阵之间的关系：

$$
\begin{gathered}
R_{uvw}\textbf{u}=
\begin{bmatrix}
\textbf{u} \cdot \textbf{u} \\\\
\textbf{v} \cdot \textbf{v} \\\\
\textbf{w} \cdot \textbf{z}
\end{bmatrix}
=
\begin{bmatrix}
1 \\\\ 0 \\\\ 0
\end{bmatrix}
= \textbf{x}
\end{gathered}
$$

相似的，我们还能得到 $R_{uvw}\textbf{v}=\textbf{y}$，以及 $R_{uvw}\textbf{w}=\textbf{z}$ 

因为旋转矩阵 $R_{uvw}$ 是正交矩阵，所以它的逆矩阵就是 $R_{uvw}^T$ ，通过该逆矩阵也能实现反操作

$$
\begin{gathered}
R_{uvw}^T\textbf{y}=
\begin{bmatrix}
x_u & x_v & x_w \\\\
y_u & y_v & z_w \\\\
z_u & z_v & z_w
\end{bmatrix}
\begin{bmatrix}
0 \\\\ 1 \\\\ 0
\end{bmatrix}
=
\begin{bmatrix}
x_v \\\\ y_v \\\\ z_v
\end{bmatrix}
= \textbf{v}
\end{gathered}
$$

对于一个任意向量 $\textbf{a}$ ，我们可以假设该向量位于 $\textbf{w}$ 轴上（即 $\textbf{w} = \textbf{a}$ ），然后将其旋转到标准 $\textbf{xyz}$ 坐标系，在对其进行指定的旋转操作，最后再使用逆矩阵将其旋转回 $\textbf{uvw}$ 坐标系

$$
\begin{gathered}
\begin{bmatrix}
x_u & x_v & x_w \\\\
y_u & y_v & z_w \\\\
z_u & z_v & z_w
\end{bmatrix}
\begin{bmatrix}
\cos\phi & -\sin\phi & 0 \\\\
\sin\phi & \phantom{-}\cos\phi & 0 \\\\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
x_u & y_u & z_u \\\\
x_v & y_v & z_v \\\\
x_w & y_w & z_w
\end{bmatrix}
\end{gathered}
$$

## 平移

我们前面都是通过一个矩阵 $M$ 左乘向量，实现该向量的线性变换，以二维平面为例：

$$
\begin{gathered}
x' = m_{11}x + m_{12}y \\\\
y' = m_{21}x + m_{22}y
\end{gathered}
$$

通过这种方式，我们只能够实现缩放和旋转，却无法进行平移操作。其实，想要实现平移操作只需要给每个点增加一个偏移量即可：

$$
\begin{gathered}
x' = x + x_t \\\\
y' = y + y_t
\end{gathered}
$$

单独定义一个这样的平移操作确实可以实现效果，但我们更希望能够与前面一样只使用一个矩阵 $M$ 进行线性变换，既能实现缩放与旋转，也能兼顾平移操作

这里需要引入齐次坐标的概念，将点 $(x, y)$ 拓展一个维度，写成新的三维向量 $[x, y, 1]^T$ ，矩阵 $M$ 也拓展一个维度：

$$
\begin{gathered}
\begin{bmatrix}
m_{11} & m_{12} & x_t \\\\
m_{21} & m_{22} & y_t \\\\
0 & 0 & 1
\end{bmatrix}
\end{gathered}
$$

使用修改过后的三维矩阵，就能够实现二维平面的线性变化（包括缩放、旋转和平移）

$$
\begin{gathered}
\begin{bmatrix}
x' \\\\ y' \\\\ 1
\end{bmatrix}
=
\begin{bmatrix}
m_{11} & m_{12} & x_t \\\\
m_{21} & m_{22} & y_t \\\\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
x \\\\ y \\\\ 1
\end{bmatrix}
=
\begin{bmatrix}
m_{11}x + m_{12}y + x_t \\\\
m_{21}x + m_{22}y + y_t \\\\
1
\end{bmatrix}
\end{gathered}
$$

如果在进行线性变换时，不希望图像产生平移，只需要将向量的拓展维度设置为 $0$ 即可：

$$
\begin{gathered}
\begin{bmatrix}
1 & 0 & x_t \\\\
0 & 1 & y_t \\\\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
x \\\\ y \\\\ 0
\end{bmatrix}
=
\begin{bmatrix}
x \\\\ y \\\\ 0
\end{bmatrix}
\end{gathered}
$$

这样左上角 $2\times 2$ 的矩阵依然会对向量进行缩放或旋转，由于向量拓展维度 $0$ 的存在将平移的操作忽略了

由于齐次坐标的拓展维度只会是 $0$ 或 $1$ ，我们可以通过该值判断位置坐标或方向向量

$$
\begin{gathered}
2D\ point=
\begin{bmatrix}
x \\\\ y \\\\ 1
\end{bmatrix}
\quad
2D\ vector=
\begin{bmatrix}
x \\\\ y \\\\ 0
\end{bmatrix}
\end{gathered}
$$

在三维空间实现平移操作也是一样的使用齐次坐标，拓展一个维度即可：

$$
\begin{gathered}
\begin{bmatrix}
1 & 0 & 0 & x_t \\\\
0 & 1 & 0 & y_t \\\\
0 & 0 & 1 & z_t \\\\
0 & 0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
x \\\\ y \\\\ z \\\\ 1
\end{bmatrix}
=
\begin{bmatrix}
x + x_t \\\\
y + y_t \\\\
z + z_t \\\\
1
\end{bmatrix}
\end{gathered}
$$

## 参考资料

- [齐次坐标](https://zh.wikipedia.org/wiki/%E9%BD%90%E6%AC%A1%E5%9D%90%E6%A0%87)
- 《Fundamentals of Computer Graphics》 Chapter 6 Transaction Matrices
