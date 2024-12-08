# 3D旋转
![[Pasted image 20241206182558.png]]
Rotation around x-, y-, orz-axis
$$R_x（a）=\begin{pmatrix}1&0&0&0\\0&cosα&-sinα&0\\0&sinα&cosα&0\\0&0&0&1\end{pmatrix}$$
$$R_y（a）=\begin{pmatrix}cosα&0&sinα&0\\0&1&0&0\\-sinα&0&cosα&0\\0&0&0&1\end{pmatrix}$$
$$R_z（a）=\begin{pmatrix}cosα&-sinα&0&0\\sinα&cosα&0&0\\0&0&1&0\\0&0&0&1\end{pmatrix}$$
> **为什么Y轴的右上角和左下角符号是反的呢？**
> 	$\vec{x}\times \vec{y}= \vec{z}$   $\vec{y} \times \vec{z}=\vec{x}$
> 	但是$\vec{z} \times \vec{x}=\vec{y}$
> 	这也是为什么OpenGL之类的选择左手坐标系的原因之一

# **罗德里格斯旋转公式**
罗德里格斯旋转公式就是总结了绕任意过原点的轴旋转的公式

$$\text{R(n, }\alpha\text{)}=\cos(\alpha)\mathbf{I}+(1-\cos(\alpha))\mathbf{n}\mathbf{n}^{T}+\sin(\alpha)\begin{pmatrix}0&-n_{z}&n_{y}\\ n_{z}&0&-n_{x}\\ -n_{y}&n_{x}&0\end{pmatrix}$$

这个公式是一个三维空间中的旋转矩阵，通常称为罗德里格斯旋转公式（Rodrigues' rotation formula）。这个公式用于将一个向量绕着另一个向量（旋转轴）旋转一个特定的角度。

公式的各个部分的解释：
- $\text{R(n, }\alpha\text{)}$：这是旋转矩阵，它表示绕向量 $\mathbf{n}$ 旋转角度 $\alpha$ 的变换。
- $\cos(\alpha)\mathbf{I}$：这是旋转矩阵的第一部分，其中 $\mathbf{I}$ 是单位矩阵，$\cos(\alpha)$ 是旋转角度 $\alpha$ 的余弦值。这一部分表示旋转过程中保持不变的分量。
- $(1-\cos(\alpha))\mathbf{n}\mathbf{n}^{T}$：这是旋转矩阵的第二部分，其中 $\mathbf{n}$ 是旋转轴的向量，$\mathbf{n}^{T}$ 是 $\mathbf{n}$ 的转置。这一部分表示旋转导致的向量在旋转轴上的投影的变化。
- $\sin(\alpha)$：这是旋转角度 $\alpha$ 的正弦值，它出现在第三部分，表示旋转导致的向量在垂直于旋转轴的平面上的分量。
- $\begin{pmatrix}0&-n_{z}&n_{y}\\ n_{z}&0&-n_{x}\\ -n_{y}&n_{x}&0\end{pmatrix}$：这是一个[[Lecture2#向量的叉乘|反对称矩阵]]，由旋转轴向量 $\mathbf{n}$ 的分量 $n_{x}$, $n_{y}$, $n_{z}$ 构成。这个矩阵与 $\sin(\alpha)$ 相乘，表示向量在垂直于旋转轴的平面上的旋转。
# Viewing transformation
在现实生活中如何照一张照片？
1. 找个好地方摆pose（Model变换）
2. 把相机放个好角度（View变换）
3. 按快门（Projection变换）

## View/Camera Transformation

1. 决定相机的位置
2. 决定相机看向的方向
3. 决定相机头朝上的方向

定义相机
1. 相机的位置 (Position)：$\vec{e}$
2. 相机看向的方向 (Look-at/ gaze direction)：$\hat{g}$
3. 相机头朝上的方向 (Up direction)：$\hat{t}$

==***规定相机永远在（0，0，0），沿着-Z看***==
==***变化的永远是其他物体***==



$$M_{view}=R_{view}T_{view}$$
$$T_{view}=\begin{bmatrix}1&0&0&-x_{e}\\0&1&0&-y_{e}\\0&0&1&-z_{e}\\0&0&0&1\end{bmatrix}$$
Rotate $g$ to $-z$ , $t$ to $Y$ , $(g\times{t})$ to $x$
Consider its inverse rotation: $x$ to $(g \times t)$, $y$ to $t$, $z$ to $-g$ 
$$
R_{view}^{-1} = \begin{bmatrix}
 x_{\hat{g}\times \hat{t}} & x_t & x_-g & 0\\
 y_{\hat{g}\times \hat{t}} & y_t & y_-g & 0\\
 z_{\hat{g}\times \hat{t}} & z_t & z_-g & 0\\
 0 & 0 & 0 & 1
\end{bmatrix} \rightarrow 
R_{view} = \begin{bmatrix}
 x_{\hat{g}\times \hat{t}} & y_{\hat{g}\times \hat{t}} & z_{\hat{g}\times \hat{t}} & 0\\
 x_t & y_t & z_t & 0\\
 x_-g & y_-g & z_-g & 0\\
 0 & 0 & 0 & 1
\end{bmatrix}
$$

将$M_{view}$应用到相机，相机归零，同时也需要将$M_{view}$应用到其他所有物体，让物体和相机的相对位置保持不变
# 投影
## 正交投影
对于二维投影来说，直接把Z轴坐标舍弃，就能得到物体在xy平面上的投影
要把得到的图像平移并且缩放到 $[-1,1]^2$ 中，方便之后的计算

对于正交投影来说，视口是个 $[l,r][b,t][f,n]$ 的长方体,想让他变成$[-1,1]^3$中的话只需要

- 先将立方体的中心平移到原点
- 在将立方体缩放到$[-1,1]^3$中

首先要找到立方体的中心点，也就是
$$
x = \left(\begin{matrix}
\frac{r+l}{2}\\
\frac{t+b}{2}\\
\frac{n+f}{2}\\
\end{matrix}\right)
$$
将边长r-l、t-b、n-f缩放到长度2
所以正交投影矩阵如下
$$M_{ortho}=\begin{bmatrix}\frac{2}{r-l}&0&0&0\\0&\frac{2}{t-b}&0&0\\0&0&\frac{2}{n-f}&0\\0&0&0&1\end{bmatrix}\begin{bmatrix}1&0&0&-\frac{r+l}{2}\\0&1&0&-\frac{t+b}{2}\\0&0&1&-\frac{n+f}{2}\\0&0&0&1\end{bmatrix}$$
>（此时物体肯定会被拉伸，在之后的视口操作中会恢复拉伸）

## 透视投影
先将Frustum远平面及远平面到近平面之间的所有平面挤压到近平面大小，
变成Cuboid的样子，然后做一次正交投影

### 实现
![[Pasted image 20241206193016.png]]
- 对于除近平面外的任意一个点，通过挤压后该点的高度 $y$ 要变成和近平面一样的 $y’$
- 从侧面看Frustum的话，如下图，可以形成两个相似三角形，即可得出$y’=(n/z)y$
- 同理$x’=(n/z)x$
$$M_{persp \to ortho}^{(4\times 4)}\begin{pmatrix}x\\y\\z\\1\end{pmatrix}=\begin{pmatrix}nx\\ny\\\text{unknown}\\z\end{pmatrix}$$
$$M_{persp\rightarrow ortho}=\begin{pmatrix}n&0&0&0\\0&n&0&0\\?&?&?&?\\0&0&1&0\end{pmatrix}$$
想补全这个矩阵，需要用到两条已知的性质
1. 近平面的点不会发生变化
2. 远平面的点z的值不会发生变化


**由性质1可得**
对于近平面上的点$(x,y,n,1)$经过矩阵变换后该点还为$(x,y,n,1)$，同时乘n后得$(nx,ny,n²,n)$
所以矩阵第三行乘以$(x,y,n,1)= n^2$

$$\begin{pmatrix}0&0&A&B\end{pmatrix}\begin{pmatrix}x\\y\\n\\1\end{pmatrix}=n^{2}$$
1. $An+B=n^2$


**由性质2可得**
选一远平面上的点$x=0，y=0$，即中间点$(0，0，f,1)$，经矩阵变化后还是中间点$(0,0,f,1)$，同时乘f后得$(0,0,f^2,f)$
可得

2. $Af+B=f^2$


$$M_{persp\rightarrow ortho}=\begin{pmatrix}n&0&0&0\\0&n&0&0\\0&0&{n+f}&{-nf}\\0&0&1&0\end{pmatrix}$$
$$M_{persp}=M_{ortho}M_{persp\rightarrow ortho}$$
___
# [[Lecture5|Next]]