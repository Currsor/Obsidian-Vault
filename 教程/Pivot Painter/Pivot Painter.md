# 用处

Pivot Painter 本身并非特殊技术，而是很久之前就有的东西。

Pivot Painter常用于草，树木等的风动效果，由于使用的是依靠枢轴（pivot）的变换实现的，且计算是在GPU中并行执行的，所以对性能十分友好。
做树木风动效果如果不知道 “PivotPainter” 的概念，较为自然的想法是使用骨骼。但是对于类似植被的物体，会有大量的枝干，而每个枝干都会需要一根骨骼，这导致了大量的骨骼。其性能会相对较差。

和VAT不同的是，Pivot Painter 并不制作动画本身。
___
# 原理介绍
Pivot Painter 将模型的枢轴和旋转信息存储在模型的顶点数据以及额外的 UV 通道（PP1）中，以及纹理（PP2）中。这些信息可以在 Unreal 的着色器系统中使用，以创建交互式效果。该工具专门设计用于与 Epic Games 在 Unreal Engine 中提供的材质函数协同工作。然而，由于它使用的是通用的属性和纹理，因此也可以在其他引擎（如 Unity）中使用这种编码信息。
___
# 麻烦
需要使用PivotPainter的模型都需要使用Maya或Houdini等（Blender可使用插件实现）进行处理。
___
# 1.0与2.0
UE的PivotPainter现在有两版，区别在于2.0版使用贴图来存储信息，而1.0版使用顶点信息包括额外的UV通道。

UE中提供了两个版本各自的材质函数
![[Pasted image 20241014205440.png]]
___
## 1.0的数据
我使用的是Houdini，所以这边介绍的是Houdini的节点
![[Pasted image 20241014205601.png]]
> Important pivot attributes are: N and name for PP1. N and hierarchy for PP2.
>Important geometry attributes are: uv and name for PP1. uv and hierarchy for PP2.
>
> **pivot点**所需要的属性，对于1.0版是`N`和`name`，对于2.0版是`N`和`hierarchy` 
> **几何体**所需的属性，对于1.0版是`uv`和`name`，对于2.0版是`uv`和`hierarchy`

> ==在一些情况下，比如输出 “Slection Order” 时，`selectionIndex`属性也是需要的。==
> 具体需要输入什么需要深入节点研究（我没这个能力，没法告诉你们了）

细节模板中的Mode可以调整PivotalPainter的版本
![[Pasted image 20241014210347.png]]
___
在1.0版本中你能看到其实都是与UE中`PivotPainter_PerObjectData`一一对应的
![[Pasted image 20241014210451.png]]
> ==这里可以观察到`Forward Axis`使用的是顶点色传递，所以导入时需将顶点色导入设置为替换==
> ___
![[Pasted image 20241014210610.png]]
与 `Per Object` 相对的是 `Hierarchical`：
![[Pasted image 20241014211005.png]]
二者应该是互斥的(我不很清楚，建议进节点研究)

## 2.0的数据
![[Pasted image 20241014205635.png]]
切换至2.0时节点输出的是两张贴图
RGB，A分别代表了些数据
![[Pasted image 20241014211255.png]]
贴图有 `16-bit` 和 `8-bit` 之分，前者将会存储为[EXR](https://www.openexr.com/)格式，后者则是常规的TGA格式。
两个也会影响A通道的选择

## 总结
PivotPainter数据被Houdini的节点导出成对应格式的文件，再由UE中的材质函数解析成正确的数据。而这些数据将会由物体真正的材质使用，在材质中可以利用PivotPainter数据实现比如风力这样的效果。
___
# 上手实操
这里我使用了2.0来操作，可以使用1.0，但需要注意传入的数据。
## 旋转
### Houdini
首先我们来一个简单的三块砖
![[Pasted image 20241014212615.png]]
![[Pasted image 20241014213737.png]]
![[https___qiita-image-store.s3.ap-northeast-1.amazonaws.com_0_314518_8be686c6-891a-cf56-3cd0-7ae67f5898eb.webp]]

![[https___qiita-image-store.s3.ap-northeast-1.amazonaws.com_0_314518_b4cff944-3b7d-9edb-c1cf-dac526ad4a33.webp]]
>左の入力：描画モデル（ポリゴンメッシュ） 
>左侧输入：绘制模型（多边形网格）  

>右の入力：ピボット（単なるポイントデータ） 
>右侧输入：枢轴（单纯的点数据）

多边形网格与哪个枢轴相关联？
为确定这一点，我们准备了名为 @hierarchy 的属性。
==需要在网格和枢轴双方进行设置。==
![[https___qiita-image-store.s3.ap-northeast-1.amazonaws.com_0_314518_1c699992-ca7e-1fb6-1240-99f01fbbfdd6.webp]]
Pivot Painter SOP 中设置的 UV 使用的是==第二个 UV==。在调用Texture时需使用第二个UV
![[Pasted image 20241014215551.png]]
「Pivot Position」在 UE 中用于“回转的中心位置”（使用@P 的值）
「Selection Order」用于调整旋转时机，作为数值（使用点数编号）
「XVector」用于 UE 中的“旋转轴方向”（使用@N 的值）
@hierarchy 本身作为数据不会输出，而是转换为像素位置或 UV 值。
### UE
首先我们需要将纹理导入，==导入后需要进行设置==
- 共通の設定 共同设定  
	无Mip贴图
	sRGB：关闭  
	Filter : Nearest 筛选：最近
- 16bit画像の設定  
	压缩设置：HDR已压缩（这样贴图的数据才不会被压缩而丢失）
- 8bit画像の設定 8 位图像设置  
	圧縮設定 : VectorDisplacementmap
#### 总览
![[Pasted image 20241014215803.png]]
#### GetData
如前文所说使用快捷键按住U后左键点出UV节点，使用第二张UV调用贴图（UE中UV是从0开始的）
![[Pasted image 20241014220144.png]]
#### Rotate
介绍两个两个节点`RotateAboutAxis`和`FixRotateAboutAxisNormals`
![[https___qiita-image-store.s3.ap-northeast-1.amazonaws.com_0_314518_ddedff77-32b8-1729-f6e5-164734ffe933.webp]]
	『回転軸の向き』    『旋转轴的朝向』
	『回転角度』           『旋转角度』
	『ピボットの位置』『枢轴位置』
这里使用`FixRotateAboutAxisNormals`是因为使用`RotateAboutAxis`旋转不会旋转法线，这个函数是用来旋转法线的

![[Pasted image 20241014220651.png]]
结尾那个节点是重命名节点（不知道叫啥，我自己给他取的）输入`name`就能搜出来
![[Pasted image 20241014220847.png]]
![[Pasted image 20241014220916.png]]
#### 结果
![[https___qiita-image-store.s3.ap-northeast-1.amazonaws.com_0_314518_128ff1f3-e188-377c-8041-0b248796cda7.webp]]
# 参考文章
[Pivot Painter の解説 AdventCalendar2022 - Qiita](https://qiita.com/Muteriku/items/857e02ac746b091a73f3)

["虚幻引擎 5.4 中的枢轴绘图工具 | 虚幻引擎 5.4 官方文档 | Epic 开发者社区" --- Pivot Painter Tool in Unreal Engine | Unreal Engine 5.4 Documentation | Epic Developer Community (epicgames.com)](https://dev.epicgames.com/documentation/en-us/unreal-engine/pivot-painter-tool-in-unreal-engine?application_version=5.4)

[观察与梳理Houdini的虚幻引擎PivotPainter工具-CSDN博客](https://blog.csdn.net/u013412391/article/details/121198267)

[实验室 Unreal 旋转画家 --- Labs Unreal Pivot Painter (sidefx.com)](https://www.sidefx.com/docs/houdini/nodes/sop/labs--unreal_pivotpainter-1.1.html)

[SideFX 实验室 - 实验室枢轴画家 --- SideFX Labs - Labs Pivot Painter (artstation.com)](https://sidefxlabs.artstation.com/projects/wJmoEg)