## 属性
模拟目标（SimTarget）中我们能够选择CPU还是GPU来计算粒子，其中GPU粒子必须设置FixedBounds。

## 发射器生成

## 发射器更新
### Emitter State
Scalability: 给粒子系统当前的Emitter配置哪些渲染等级。可以针对每个模块独立设置渲染等级，可以更方便地适配不同性能的各个平台。

## 事件处理器
点击加号可以添加多个EventHandler顺次执行，这些是当前粒子发生器对于某个事件的相应规则。

## 渲染
用来添加我们的粒子对应的显示实体的，可以为空，也可以为多个。为空的情况意味着所有的计算不需要任何前端的显示，比如我们用粒子系统做一个纯粹的ComputeShader。

## [[SimulationStage]]
SimulationStage是Niagara推出的一个新功能，大幅度增加了Niagara的能力。