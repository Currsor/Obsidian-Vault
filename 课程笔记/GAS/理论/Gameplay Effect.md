# 使用
可以在Gameplay Ability中的cost设置Effect
![[Pasted image 20240311224943.png]]

也可以选择使用节点
![[Pasted image 20240317150816.png]]
___
# 持续时间
持续时间会影响效果改变值的类型
若为瞬间的话改变的是基础值,例如:加血10点就只是加了10点血
若为具有持续时间或为无限的改变的是当前值,例如:加了10点血,过了时间或者解除效果后加的这10点血会消失
若为周期性的话改变的也是基础值
![[Pasted image 20240317145403.png]]
___
# 移除效果
___
### 添加技能
我们可以在effect中为角色添加ability
并且可以将其设置为触发一次后删除
![[Pasted image 20240321154942.png]]
(这个是在5.2之前版本的画面)

5.2: 在页面的最后
![[Pasted image 20240321160711.png]]

Removal Policy: 
	1: Cancel Ability Immediately:赋予ability,在其被激活后立即删除abiilty
	2:Remove Ability on End: 赋予ability,在effect结束时删除ability( #待查明 激活一次会不会移除为实验)
	3:Do Nothing: 保持ability独立性,意思就是说单纯赋予一个ability


---
# EffectSpecHandle and EffectContextHandle
`EffectSpecHandle`是对`GameplayEffectSpec`的引用，它包含了游戏效果的所有数据，比如持续时间、影响的属性值、以及任何关联的标签或者元数据。它是一个轻量级的引用，可以用来安全地传递和存储指向特定`GameplayEffectSpec`的引用。

`EffectContextHandle`则是对`GameplayEffectContext`的引用，它包含了关于效果来源的信息，比如施加者、目标、以及任何相关的游戏对象。这个上下文信息对于确定效果如何应用以及它们的来源至关重要。

简而言之，`EffectSpecHandle`关注于效果的内容和规则，而`EffectContextHandle`关注于效果的来源和目标上下文。在设计游戏的技能和效果时，正确地使用这两者对于确保游戏逻辑的准确性和可维护性是非常重要的。


---
# Stacking
**Stacking Type**
	堆栈方法
- `None`

- `Aggregate by Source`:叠加上限取决于Effect来源,单个来源提供的Effect达到上限可以通过其他Actor提供该Effect

- `Aggregate by Targe`t:叠加上限取决于目标,目标身上Effect达到上限后不同Actor提供该Effect也无法附加在Target上


**Stack Limit Count**
	Stacking Type堆栈上限


**Stack Duration Refresh Policy**
	GameplayEffect被成功应用时，是否会刷新其持续时间，即使效果的层数（Stack Count）没有增加
- `Refresh on Successful Application`: 当这个效果再次被应用时，即使层数没有增加（例如，已经达到了堆叠上限），其持续时间也会被重置。这意味着效果会重新开始计时，玩家将获得完整的效果持续时间

- `Never Refresh`: 即使效果被再次应用，其持续时间也不会被刷新。效果将继续按照原来的计时器运行，直到持续时间结束


**Stack Period Reset Policy**
	GameplayEffect被成功应用时，是否会重置其周期时间（如果有的话）
	
- **Refresh on Successful Application**: 每次效果被应用时，其周期时间都会被重置。这对于那些需要定期执行某些操作的效果（例如，每秒恢复生命值）非常有用

- **Never Refresh**: 即使效果被再次应用，其周期时间也不会被重置。周期性操作将继续按照原来的计时器运行