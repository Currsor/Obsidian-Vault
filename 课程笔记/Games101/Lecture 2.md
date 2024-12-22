# 向量的点乘
可用于判定向量夹角大小A
若大于90度则为负
## 方向判断
右手螺旋定则

# 矩阵
## 矩阵的乘积
判定两矩阵是否可相乘
`(M * N) (N * P) = (M * P)`
第一个矩阵的列 == 第二个矩阵的行

### 计算
第一个矩阵a行和第二个矩阵b列做点积运算

## 矩阵性质
矩阵具有结合律，而==**不具有交换律**==
### 矩阵乘向量
将向量转为竖向向量而后当为矩阵处理
### 矩阵的转置
$$\left(\begin{array}{cc}1&2\\3&4\\5&6\end{array}\right)^{T}=\left(\begin{array}{ccc}1&3&5\\2&4&6\end{array}\right)$$
$$(AB)^{T}=B^{T}A^{T}$$
### 单位矩阵
$$I_{3\times 3}=\left(\begin{matrix}1&0&0\\0&1&0\\0&0&1\end{matrix}\right)$$
### 矩阵的逆
$$AA^{-1}=A^{-1}A=I$$
$$(AB)^{-1}=B^{-1}A^{-1}$$
### 向量的叉乘
$$\begin{aligned}\vec{a}\times\vec{b}=A^{*}b=\left(\begin{array}{ccc}0&-z_{a}&y_{a}\\z_{a}&0&-x_{a}\\-y_{a}&x_{a}&0\end{array}\right)\left(\begin{array}{l}x_{b}\\y_{b}\\z_{b}\end{array}\right)\end{aligned}$$
使用了另一种方式来表达叉乘：通过一个 3 $\times$ 3 的矩阵 $A^* b$，其中 $A^*$ 是由向量 $\vec{a}$ 构造出来的双重矩阵（也叫做反对称矩阵）。这个双重矩阵 $A^*$ 定义如下：
$$A^*=\left(\begin{array}{ccc}0&-z_{a}&y_{a}\\z_{a}&0&-x_{a}\\-y_{a}&x_{a}&0\end{array}\right)$$
（Lecture4的罗德里格斯旋转公式会用到这个性质）
___

# [Lecture 3](Lecture%203.md)