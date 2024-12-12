---
banner: "![[hyeonsick-choi-aruana-sick-.jpg]]"
banner_y: 0.32
---
# 高光
高光的方向 -> 越光滑 越趋于镜面反射的方向

观察的方向越接近镜面反射的方向 越能看到高光

当观察方向接近镜面反射方向的时候 <=> 法线方向n 接近于 半程向量h
![[Pasted image 20241212134717.png]]

$$
h_{(半程向量)}=
\text{bisector}\left(\mathbf{v},\mathbf{l}\right)
=\frac{\mathbf{v}+\mathbf{l}}{\lVert \mathbf{v}+\mathbf{l} \rVert}
$$
$$L_{s}=
k_{s}\left(\frac{I}{r^{2}}\right)\max(0,\cos \alpha)^p=
k_{s}\left(\frac{I}{r^{2}}\right)\max(0,\boldsymbol{n}\cdot \boldsymbol{h})^p
$$
所以为了知道能否看到高光，布林冯模型只需要知道n和h是否接近
![[Pasted image 20241212135742.png]]
如果用视线方向v和高光方向R来判断能否看到高光，就是冯模型（冯模型计算量大）

向量之间的夹角余弦确实可以衡量两向量是否接近，但容忍度太大了
![[Pasted image 20241212135833.png]]
在一次幂的cos曲线下可看到，当夹角很大时，仍然有很大的值，这样生成的高光就会很大

正常情况下，我们认为高光都是很小很亮的

随着指数增加，能看到在大约0~30°之内才可以看到高光，这样就算一个合理的模型

在布林冯模型下，一般来说，指数选在100~200之间，高光角度大约在3~5°之间，算是比较真实的


![[Pasted image 20241212135908.png]]
纵向来看，反射系数Ks越大，高光越亮
横向来看，指数p越大，高光越小

# 环境光照
一个茶杯，在光源并没有直接照射的方向上也有一定的亮度，因为一个光线可以弹射很多次，从四面八方打到任何一个点，这些光照就算是环境光照

$$L_{a}=k_{a}I_{a}$$
由于环境光照非常复杂，这里我们假设一个点受到的环境光照永远都是相同的，强度称为$L_a$ 
任何一个点都有自己的颜色，$K_a$ 相当于环境光的系数

# Blinn-Phong Reflection Model
![[Pasted image 20241212140314.png]]
环境光（无论方向）（常数颜色） + 漫反射（无论观测方向）（光照/法线） + 高光
$$L=L_a+L_d+L_s$$
$$L=k_aI_a+k_d(I/r^2)\max(0,\mathbf{n}\cdot\mathbf{l})+k_s(I/r^2)\max(0,\mathbf{n}\cdot\mathbf{h})^p$$

# ShadingFrequencies 着色频率
![[Pasted image 20241212140354.png]]
三个球具有完全相同的空间信息，着色频率不同后表现不一样

# Flat Shading（面着色）
![[Pasted image 20241212140433.png]]
**Flat shading**
- Triangle face is flat — one normal vector
- Not good for smooth surfaces

# Gouraud Shading（顶点着色）
![[Pasted image 20241212140650.png]]
**Gouraud shading**
• Interpolate colors from vertices across triangle
• Each vertex has a normal vector (how?)

# Phong Shading (像素着色)
![[Pasted image 20241212140942.png]]
   
Phong shading  
- Interpolate normal vectors across each triangle  
- Compute full shading model at each pixel  
- Not the Blinn-Phong Reflectance Model

三种着色频率产生的效果也取决于模型本身

每一行的模型本身顶点数是一样的，越往下顶点数越多

当几何足够复杂时用Flat Shading得到的效果也很好

反过来说，当几何的面数大于像素数量时Flat Shading的性能也不会好于Phong Shading
![[Pasted image 20241212141106.png]]

# 顶点法线
![[Pasted image 20241212143523.png]]
使用加权的方式把顶点周围的三角形法线乘在一起后归一化
$$N_{v}=\frac{\sum_i N_i}{\|\sum_i N_i\|}$$

# Graphics (Real-time Rendering) 
![[Pasted image 20241212143756.png]]
- 顶点处理
将三维空间的点投影在平面上

- 三角形处理
将这些点连接形成三角形

- 光栅化
将三角形离散成为屏幕上的Fragment（未经处理的像素）

- 着色
给像素上色

- 后处理
深度缓冲-处理遮挡关系，MSAA等抗锯齿
（以上操作都在硬件中处理好了，也就是GPU工作流程）

## 顶点处理
![[Pasted image 20241212143949.png]]
处理模型，视图，投影变换

## 光栅化
![[Pasted image 20241212144044.png]]

## Z-Buffer 深度测试
![[Pasted image 20241212144101.png]]
## Shading---顶点火像素处理
![[Pasted image 20241212144200.png]]
如果用的是GouraudShading，那么进行的就是顶点处理

如果用的是PhongShading，那么进行的就是像素处理

# Shader
现代GPU中，这套渲染管线某些部分是可编程的，可以由开发者去定义顶点/像素如何着色
也就是用代码控制如何着色
这部分代码就叫Shader

Shader指定的是每一个像素/顶点如何着色，所以不能也不用去指定某一个像素如何着色
如果写的是顶点操作，这个shader就叫做VertexShader（顶点着色器）
如果写的是像素操作，这个shader就叫做FragmentShader（片段/片元着色器）/PixelShader（像素着色器）

# Texture Mapping
我们希望得到一个三角形，三角形里面映射了一张图片，怎么得到？
以球来说，我们发现不同位置有不同颜色，球整体其实公用同一个着色模型，唯一区别就是漫反射系数 $K_d$ [[Lecture 7#^2e7d1a|Kd]]不同

我们希望有一种方法，可以定义一个物体上任意一点的基本属性
3D物体的表面其实都是2D的，比如地球仪，将地球仪上的图撕下来，可以平铺成一张2D的图

物体的表面，通过这种方式可以和一张图有一一对应的关系，这张图就叫Texture

将这张图平铺/裁剪/拉伸到任何物体表面，就叫Texture Mapping
![[Pasted image 20241212144829.png]]
空间上模型的三角形怎么对应到纹理上的三角形？
由美术同学提供纹理上的坐标系通常以UV来表示
通常约定U和V的范围\[0,1]
![[Pasted image 20241212144939.png]]
当纹理不断重复贴到模型上，可以得到不错的效果，虽然看纹理效果可以看到两张纹理之间有很明显的变化，但是在场景中很自然的无缝衔接
![[Pasted image 20241212144954.png]]
说明这个纹理本身设计的好，这种纹理叫Tilable Textures

这种纹理的设计也是很值得研究的
___
# [[Lecture 9]]