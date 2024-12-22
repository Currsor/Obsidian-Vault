# 缩放
![](IMG/Pasted%20image%2020241206164332.png)
$$\left[\begin{array}{l}x^{\prime}\\y^{\prime}\end{array}\right]=\left[\begin{array}{ll}s_{x}&0\\0&s_{y}\end{array}\right]\left[\begin{array}{l}x\\y\end{array}\right]$$
# 错位
![](IMG/Pasted%20image%2020241206164440.png)
$$\left[\begin{array}{l}x'\\y'\end{array}\right]=\left[\begin{array}{ll}1&a\\0&1\end{array}\right]\left[\begin{array}{l}x\\y\end{array}\right]$$
# 旋转
![](IMG/Pasted%20image%2020241206164514.png)
$$\boldsymbol{R}_{\theta}=\left[\begin{array}{cc}\cos\theta&-\sin\theta\\\sin\theta&\cos\theta\end{array}\right]$$
# 2D变换的齐次坐标
Scale
	$$\mathbf{S}\left(s_{x}, s_{y}\right)=\left(\begin{array}{ccc}s_{x}&0&0\\0&s_{y}&0\\0&0&1\end{array}\right)$$
Rotation
	$$\mathbf{R}(\alpha)=\left(\begin{array}{ccc}\cos \alpha&-\sin \alpha&0\\\sin \alpha&\cos \alpha&0\\0&0&1\end{array}\right)$$
Translation
	$$\mathbf{T}\left(t_{x}, t_{y}\right)=\left(\begin{array}{ccc}1&0&t_{x}\\0&1&t_{y}\\0&0&1\end{array}\right)$$
先旋转在平移写作 （T·R·向量） 从右往左写
___
# [Lecture 4](Lecture%204.md)