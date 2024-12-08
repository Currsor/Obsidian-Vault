UE4通过蓝图去控制材质的方法大概有两种，一是通过MPC去控制，二是通过CreateDymaticMaterialInstance（创建动态材质）去控制
## MPC
在资产窗口右键创建MPC材质
![[Pasted image 20231103092605.png]]
在MPC材质当中，会有两个数据类型一个是Scalar标量和向量Vector，这些就是我们可以自定义的参数
![[Pasted image 20231103092654.png]]
然后我们将MPC直接拖入到我们的材质编辑器当中，并且去指定我们自定义的名称，那么这里就形成了一个材质的输入口
![[Pasted image 20231103093210.png]]
然后我们通过蓝图SetScalarParameterValue来进行设置数据，将我们的MPC作为Targrt然后指定参数的名字，就可以设置参数了
![[Pasted image 20231103093330.png]]
## 创建动态材质
指定我们需要创建材质的部分，然后将这个材质，设置成一个变量，然后将把这个Set为这个物体的材质，因为创建后的动态材质，和原来的材质不是一种类型，所以需要创建过后的动态材质重新set为Mesh的material![[Pasted image 20231103094306.png]]
然后在Event的材质当中通过setScalaar再进行设置，需要注意的是最后直接通过你的变量创建，不然有时候连接不上这个节点