# 纹理的应用
在现代 GPU 中，纹理 = 内存 + 范围查询（过滤）

## Environment Map 环境贴图
![](IMG/Pasted%20image%2020241228205707.png)

将环境光写在球面上形成了球面环境贴图(Spherical Environment Map)
![](IMG/Pasted%20image%2020241228205855.png)

这样会导致一个问题，顶部和底部会产生扭曲
![](IMG/Pasted%20image%2020241228205941.png)

这时候我们可以将一个向量映射到该方向上的立方点。立方体用 6 个正方形纹理贴图进行纹理化。
![](IMG/Pasted%20image%2020241228210023.png)

在虚幻引擎中有使用八面体参数化球面映射

## Octahedral map八面体球面映射
[八面体参数化球面映射方式及实现 - 知乎](https://zhuanlan.zhihu.com/p/408898601)
![](IMG/Pasted%20image%2020241228210728.png)
球面参数化映射方式有很多种，两种比较常见的是 cube mapping 和 spherical mapping。

cube mapping 的缺点是需要存储六张正方形图片表示球面。而 spherical mapping 虽然只需要一张图片，但是由于 spherical mapping 是通过经纬来映射球面的，处在经纬球的南北两个极点附近的图像虽然只占据了一小部分立体角，仍然需要一整行像素来表示，导致图像变形严重。

八面体映射（octahedral map）是一种把一张正方形图片映射到八面体，然后再映射到球面的参数方式。相对于 spherical mapping， 八面体映射下的每个像素对应的立体角相对均匀，不会出现太大的扭曲，而且比起 cube map 需要存储 6 张图，八面体映射只需要 1 张图即可。

但是实际上这仍然不是等面积映射，即每个像素对应的立体角大小并不相同。
![](IMG/Pasted%20image%2020241228210619.jpg)

### Concentric Octahedral map
![](IMG/Pasted%20image%2020241228210143.png)
这种方法也是先把四个角对折，形成两个小的正方形。分别代表两个半球面。

在前面的八面体映射中，我们是将这两个小正方形映射到八面体上。而这里我们不这么做了，而是把正方形用 Concentric mapping 映射到同心圆上然后再映射到半球面。注意，这里实际上已经和八面体无关了。

对 Concentric Octahedral map 进行均匀采样，结果也是均匀的（至少看起来是）
![](IMG/Pasted%20image%2020241228210943.jpg)

## 凹凸贴图
贴图不一定存储的都是颜色，可以是法线/高度图。

Bump / normal mapping：凹凸/法线贴图

可以用于做出假的细节，利用光影让人产生立体感
![](IMG/Pasted%20image%2020241228211429.png)

在不增加更多三角形的情况下添加表面细节
- 扭曲每个像素的法线（仅用于阴影计算）
- 每个纹理定义的“高度移动”
### 2D如何修改法向量？
![](IMG/Pasted%20image%2020241228211533.png)
原始表面法线 $n(p)=\begin{bmatrix} 0 \\ 1 \end{bmatrix}$

导数在 p 处的值 $\frac{dh}{dp} \Bigg|_{p} \approx \frac{h(p+1) - h(p)}{1} = c \cdot [h(p+1) - h(p)]$

扰动后的正交基为 $n(p) = (-dp, 1).normalized()$
### 3D如何修改法向量？
原始表面法线 $n(p)=\begin{bmatrix} 0 \\ 0 \\ 1 \end{bmatrix}$
偏导数在 p 处
- $\frac{dp}{du} = c_1 \cdot \left[ h(u + 1) - h(u) \right]$
- $\frac{dp}{du} = c_2 \cdot \left[ h(v + 1) - h(v) \right]$

扰动正常向量是 $n = (-\frac{dp}{du}, -\frac{dp}{dv}, 1).normalized()$
> 这里使用的是切线空间（Tangent Space）

## Displacement mapping 位移贴图
它不是调整法线形成光影了，它实际上是真的移动了顶点
![](IMG/Pasted%20image%2020241228213615.png)

## 3D贴图
贴图不仅仅可以是2D的也可以是3D。通过该点的世界坐标进行计算得出该点的颜色，例如Perlin noise

## 存储预计算着色
纹理可以用于存储运算的结果，如AO
![](IMG/Pasted%20image%2020241228214514.png)

## 3D 纹理和体积渲染
![](IMG/Pasted%20image%2020241228214622.png)

# 几何学

许多方法来表示几何

隐式
- 代数曲面
- 水平集
- 距离函数

显式
- 点云
- 多边形网格体
- subdivision，NURBS

## 隐式
点满足某些指定关系
例如球面：3D 空间中所有满足 x+y+z=1 的点。
f(x,y,z) = 0
![](IMG/Pasted%20image%2020241228215232.png)

隐式来判断这个函数代表的形状是什么是困难的
但是用于判定是否在面上是简单的

## 显式
除了直接描述所以点的位置，还能用参数映射的方式了表达
![](IMG/Pasted%20image%2020241228215740.png)

布尔运算
![](IMG/Pasted%20image%2020250102142419.png)

距离函数
![](IMG/Pasted%20image%2020250102142438.png)
SDF
___
# [[Lectue 11|Next]]