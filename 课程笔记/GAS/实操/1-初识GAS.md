# 组成部分
~~#名词解释 GAS (Gameplay ability system)分为四个部分~~
	~~1：Gameplay ability （游戏玩法）~~
	~~2：Attributes and Attribute Sets（属性与属性集）~~
	~~3：[[Gameplay Effect]]（游戏效果）~~
	~~4：Gameplay Cue（游戏提示）~~

详见[[GAS_Learn|GAS_Learn]]

---
# 实践
### 创建项目
创建一个第三人称模板，启用GAS插件
![[Pasted image 20240310172833.png]]

创建Gameplay ability默认模板
![[Pasted image 20240310172940.png]]![[Pasted image 20240310173001.png]]

### 创建插槽
进入玩家骨骼，添加插槽
使用预览调整位置
![[Pasted image 20240311205625.png]]

### 创建接口
![[Pasted image 20240311205915.png]]
创建新的输入Actor类“WeaponActor”

然后在角色蓝图的类设置中实现接口
![[Pasted image 20240311210229.png]]

### 装备武器
通过接口获取到武器actor然后将其附加在插槽内
![[Pasted image 20240311210816.png]]

记得关闭武器的碰撞，不然有可能将角色挤压的飞起来

### 可拾取物品
我希望建立的这个蓝图是大部分物品都能使用的，所以命名为BP_Collectable

Rotating Movement组件：可以使物品旋转

取消武器碰撞

实现重叠后拾取逻辑
![[Pasted image 20240311215348.png]]
也可以
![[Pasted image 20240311215715.png]]
后一种将ability曝露在变量中，方便更改

### 添加标签
为放着反复捡取，可以使用标签阻止，在类默认值
![[Pasted image 20240311221351.png]]
Activation Blocked Tags如果激活的参与者/组件具有这些标记中的任何一个，则此功能将被阻止

![[Pasted image 20240311221513.png]]
命名用“."隔开则可以设置标签的层级

~~将武器拾取后使用”add gameplay tag"添加标签来阻止反复拾取武器~~
我们使用gameplay effect类来实现技能的效果
创建一个gameplay effect类，在蓝图内查询![[Pasted image 20240311224710.png]]
这个承载了技能触发的效果的功能，给对象添加阻碍标签
![[Pasted image 20240312085920.png]]
==将持续时间更改为无限或者一段时间==,否则它只会生效一瞬间
![[Pasted image 20240312091421.png]]

在ability的类默认值中设置其的effect![[Pasted image 20240311224943.png]]
Costs:这个游戏效果代表了技能的消耗(魔法，耐力等)。它将在能力交付时生效。

在效果触发事件加入节点判断该技能是否可以触发
![[Pasted image 20240312090454.png]]

### 创建提示
创建蓝图类GameplayCueNotify
提示可以使用声音和niagara
填入声音和niagara
![[Pasted image 20240317130528.png]]

打上标签,这个标签用于激活提示
![[Pasted image 20240317130635.png]]

使用节点在技能中触发提示
![[Pasted image 20240317131048.png]]