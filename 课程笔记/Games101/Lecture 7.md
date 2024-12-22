# Z-Buffer
没办法判断三角形整体的深度，那么就判断每个像素的深度

像素内记录像素深度最浅的几何

对于深度来说，越小越近，越大越远

在渲染时不光要存渲染的图，也要存一张深度的图![](IMG/Pasted%20image%2020241208193810.png)

既然是覆盖关系则不必纠结先后顺序
对单个像素来说，逐步记录深度

如先画地板，先记录地板深度
物品来了后比对物品的深度和记录的深度
发现物品深度小于记录的地板深度，说明物品要遮挡住地板
```cpp
for (each triangle T) 
	for (each sample (x,y,z) in T) 
		if (z < zbuffer[x,y]) 
			framebuffer[x,y] = rgb;// closest sample so far
			 zbuffer[x,y] = z;// update color
		else 
		; // do nothing, this sample is occluded
```
![](IMG/Pasted%20image%2020241208194338.png)
# Shading(着色)
物体产生的颜色和光照、材质有关
## Blinn-Phong Reflectance Model（布林·冯反射模型）
![](IMG/Pasted%20image%2020241208194520.png)
### Shading is Local(局部阴影)
计算特定着色点反射向摄像机的光线

输入：
- Viewer direction, v
- Surface normal, n
- Light direction, l
	(for each of many lights)
- Surface parameters
	(color, shininess, …)
![](IMG/Pasted%20image%2020241208194756.png)
### Diffuse Reflection(漫反射)
光线均匀的向所有方向散射
- 表面颜色在所有观察方向上都是相同的
![](IMG/Pasted%20image%2020241208195028.png)

物体表面法向量n，和光源方向I，的夹角θ，决定了明暗强度
$$\cos\theta={I}\cdot\mathbf{n}$$
![](IMG/Pasted%20image%20IMG/Pasted image 20241208200446.png)
可以把光当成能量，吸收的越多越亮
### 能量守恒
光的能量都集中在一个球壳上，一开始球壳的表面积很小，考虑到能量守恒的话，那么单位面积上光的能量就很多，光越向外扩散，单位面积的能量就越小
通过球面公式可以计算出，距离光源为r的球壳上，单位面积上能量为
$$E=I/r^2$$
### Lambertian (Diffuse) Shading
![](IMG/Pasted%20image%2020241208194756.png)
$$L_d = k_d \left(\frac{I}{r^2}\right)\max(0,\mathbf{n} \cdot \mathbf{l})$$
这是一个计算漫反射光照的公式，通常在计算机图形学和3D渲染中使用。让我们逐步解释这个公式的各个部分：
1. $L_d$: 这是我们要计算的漫反射光强度（Diffuse Light Intensity）。
2. $k_d$: 这是材质的漫反射系数（Diffuse Coefficient），表示材质表面如何反射光线。它的值介于0到1之间，其中0表示完全不反射，1表示完全反射。如果用RGB三个通道表示 $K_d$ ，那么 $K_d$ 就是Color ^2e7d1a
3. $I$: 这是光源的光强（Light Intensity），即光源发出的总能量。
4. $r^2$: 这是光源到物体表面的距离平方（Distance from the light source to the surface squared）。随着距离的增加，光会扩散，因此光强与距离的平方成反比。
5. $\max(0, \mathbf{n} \cdot \mathbf{l})$: 这一部分称为漫反射项（Diffuse Term），它取决于入射光的夹角。这里$\mathbf{n}$是物体的表面法线向量（Surface Normal Vector），而$\mathbf{l}$是从物体指向光源的单位向量（Unit Vector pointing towards the light source）。点积$\mathbf{n} \cdot \mathbf{l}$表示这两个向量的夹角的余弦值。当两个向量垂直时，点积为0；当它们平行时，点积为1。由于我们只关心正向照明的情况，所以取最大值为0或正的点积结果。
___
# [Lecture 8](Lecture%208.md)
