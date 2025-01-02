# 显式 
## Point Cloud 点云
最简单的表示：点列表（x,y,z）
经常转换为多边形网格
难以在欠采样区域绘制
![](IMG/Pasted%20image%2020250102145359.png)
## Polygon Mesh 多边形网格
存储顶点和多边形（通常是三角形或四边形）更容易进行处理/模拟，自适应采样可能更复杂的数据结构图形中可能最常见的表示
![](IMG/Pasted%20image%2020250102145505.png)

# Curves
## Bézier Curves 贝塞尔曲线
定义带有切线的三次贝塞尔曲线
![](IMG/Pasted%20image%2020250102145619.png)
## Evaluating Bézier Curves(de Casteljau Algorithm)
![](IMG/Pasted%20image%2020250102150155.png)
通过寻找事件 t 时所在位置连线形成曲线，类似递归的感觉

![](IMG/Pasted%20image%2020250102150432.png)
![](IMG/Pasted%20image%2020250102165330.png)
## 贝塞尔代数公式
![](IMG/Pasted%20image%2020250102165412.png)

由三个点构成的二次贝塞尔曲线
$$\mathbf{b}_{0}^{1}(t)=(1-t)\mathbf{b}_{0}+t\mathbf{b}_{1}$$
$$\mathbf{b}_{1}^{1}(t)=(1-t)\mathbf{b}_{1}+t\mathbf{b}_{2}$$  
$$\mathbf{b}_{0}^{2}(t)=(1-t)\mathbf{b}_{0}^{1}+t\mathbf{b}_{1}^{1}$$
$$b_{0}^{2}(t)=(1-t)^{2}b_{0}+2t(1-t)b_{1}+t^{2}b_{2}$$


贝塞尔曲线 n 阶的伯恩斯坦形式：
$$b^{n}(t)=b_{0}^{n}(t)=\sum_{j=0}^{n}b_{j}B_{j}^{n}(t)$$
$$B_{i}^{n}(t)=\left(\begin{array}{l}n\\i\end{array}\right)t^{i}(1-t)^{n-i}$$
![](IMG/Pasted%20image%2020250102165930.png)
贝塞尔曲线阶数 n
$b_j$ 贝塞尔控制点   (vector in $R^N$ )

## 分段贝塞尔曲线
链式连接多个低阶贝塞尔曲线 分段三次贝塞尔是最常见的技巧
![](IMG/Pasted%20image%2020250102170057.png)
使用四个控制点控制一段曲线，PS中的钢笔工具就是这个。


![](IMG/Pasted%20image%2020250102170340.png)
$a:[k,k+1]\rightarrow R^N$
$b:[k+1,k+2]\rightarrow R^N$

$C^0$ 连续性：$a_n=b_0$
![](IMG/Pasted%20image%2020250102170453.png)
$C^1$ 连续性：$\begin{aligned}\mathbf{a}_{n}=\mathbf{b}_{0}&=\frac12(\mathbf{a}_{n-1}+\mathbf{b}_{1})\end{aligned}$
## 其他类型的样条曲线
- 样条
	一条连续曲线，构造使其通过一组给定的点，并具有一定数量的连续导数。
	![](IMG/Pasted%20image%2020250102170551.png)
- B-splines

# 贝塞尔曲面
贝塞尔曲面和 4x4 控制点阵列
![](IMG/Pasted%20image%2020250102170659.png)

![](IMG/Pasted%20image%2020250102170715.png)

4x4 控制点输出由(u,v)在\[0,1]范围内参数化的二维曲面
首先生成灰色的贝塞尔曲线，然后将 t 相同的连起来再进行绘制贝塞尔曲线
所以使用UV就能获取这个曲面上所有点的位置
![](IMG/Pasted%20image%2020250102170806.png)![](IMG/Pasted%20image%2020250102170934.png)
