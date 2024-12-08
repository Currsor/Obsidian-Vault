[Niagara概览 | 虚幻引擎文档 (unrealengine.com)](https://docs.unrealengine.com/4.26/zh-CN/RenderingAndGraphics/Niagara/Overview/)
## Niagara的组成概述
![[Pasted image 20231022210254.png]]
![[Pasted image 20231022210318.png]]
### [[Niagara发射器(Emitter)]]
一个粒子系统可以由多个粒子发射器组成，而我们在外面创建的粒子发射器资产，则是能够在多个粒子系统中进行**复用**的。创建一个粒子发射器时，我们也可以分别选择三种模式：使用一个已有模板、继承一个已有模板、使用一个已有发射器。

一个发射器是否使用模板可以在资源选项查看.![[Pasted image 20231022210633.png]]
库可视性可以设置是否会被粒子系统索引到

继承关系意味着对父Emitter的修改会同时修改子Emitter，而子Emitter无法删除从父Emitter中继承得到的内容或调整它们的顺序，直接使用则是单纯的复制粘贴过程，二者不会产生任何依赖。
### [[Niagara系统]]
Niagara系统包含多个发射器，结合后可产生一种效果。
例如制作烟花效果，可能需要多次爆发。为此需创建多个发射器，并放置在名为Firework的Niagara系统中。 
在Niagara系统编辑器中，可在系统中修改或覆盖发射器或模块内的任何内容。系统编辑器中的 **时间轴（Timeline）** 面板将显示系统包含的发射器，并可用于管理此类发射器。 发射器编辑器和系统编辑器大致相同。
### **Niagara Module Script**
是Emitter下的一个基本执行单元，根据情况可以被放在任何一个执行部分中。

#### 模块使用位掩码
![[Pasted image 20231022211606.png]]
这个设置决定了我们的Module Script能够被Emitter的哪个执行过程中使用。
在这里改变类型并不会真的改变我们资源的类型，以及它们在各个区域的可见性及使用，我们的资源类型是在创建类型时就决定好了的。

#### 提供的依赖性与所需的依赖性
提供的依赖性(Provided Dependencies)与所需的依赖性(Required Dependencies)是我们对依赖关系的配置，一般情况下用不到，除非想要设计一些框架内的基础组件。依赖关系在我们的粒子系统中是可以被进一步拆分的，即我们需要手动配置它们的依赖属性，而不仅仅是指定某模块依赖于某模块。
模块之间的依赖关系可能是**网状**的，一个模块满足某个条件时才能被正常使用，但是能够给予这个条件的模块可能不止有一个。同时一个模块也可能给予多个让其它模块依赖的条件。因此我们的模块依赖关系使用纯粹的FName进行配置，Provided Dependencies只给定一系列的该模块提供给其他模块的可被依赖的内容名称即可，Required Dependencies则还需要对每个依赖额外配置依赖模块间的执行顺序、是否位于同一个执行逻辑内等。

#### 输入参数与输出参数
InputMap与OutputModule，这个中间整个是使用一条白色的类似蓝图流程线的粗线贯穿的，然而它与蓝图真正意义上的流程线不同，实际上也是一个变量传递的线，传递的变量叫做Parameter Map，即所有当前可访问变量的总表，因此也是可以连出多条线，但是只能连入条线。我们可以定义多个Parameter Map输入以及多个Parameter Map输出，同时Module Script也能给定多个非一般类型的输入变量。这些输入与输出引脚的追加可以通过左边栏详情下面的输入输出栏进行：![[Pasted image 20231022213235.png]]
其中Common是追加Parameter Map引脚，Data Interface是追加特殊数据引脚，Output是无法追加数据引脚的。

#### 映射Get与映射集
Map Get与Map Set。不同于一般的蓝图，我们获取与设置粒子系统内任何变量都必须通过这两个节点来完成。这两个节点可以被创建很多个，即我们可以通过多个Map Set的连接来指定某些有依赖关系的变量的赋值顺序，同时我们的Map Get不同于Attribute Reader，始终获取的是当前最新的变量值。我们可以通过这两个节点上面的加号来添加需要获取或设置的变量，又或者，我们可以通过左边栏的Parameter列表中添加我们要使用的变量，然后将其拖到这两个节点上来使用。
这两种添加方式的区别是，在节点上只能找到我们粒子系统默认赋予Emitter、Particle等的默认变量，以及创建新的模块输入。而在左边栏我们则还可以直接给Emitter、Particle等创建具有新的名称的变量，然后给Map Get或Map Set使用。
我们的ParameterMap不会在执行过程中进行多份复制，而是在用指针索引到每个具体变量，因此每次通过指针找到该变量获取的都是最新值
Module Inputs跟我们刚刚的红色输入节点的不同。这些被创建的输入变量会在Emitter中选中该模块时显示。我们可以在此处为我们定义的这些输入变量赋值。红色输入节点定义的参数则不会出现在此处，而只能在我们从另一个Module以脚本节点的方式调用当前节点时，才会作为输入引脚出现，如图：![[Pasted image 20231022221443.png]]
粒子系统无论是实际粒子的运算，还是SimulationStage对其他数据类型的操作，本质上都是数据集的批量操作。上一个Module计算得到的ParameterMap传入当前模块，经过计算再输出给下一个模块是最正常的逻辑。然而如果我们制定了两个输入或输出的参数集的位置，此时我们给定的执行逻辑是模糊的。对粒子系统来讲任意时刻只有且只需要一套参数集，输入的双参数集分支会破坏这种唯一性。
### **Niagara Function Script**
Function不能在Emitter中被直接添加作为执行逻辑的一环，只能作为其他Module或Function中的一个函数节点使用。也正因如此，我们可以为输入与输出添加诸如float、Vector等基本数据类型。我们能以Module中类似的方式添加更多的输入，甚至加入ParameterMap的输入与输出节点，并使用MapGet与MapSet。
### **Niagara Dynamic Input Script**
与Module、Function同类型同详情页的组成成分，但是有完全不同的用法
默认的连接反映了，Dynamic Input的输入可选类型与Module相同，输出可选类型与Function相同，但是不能包含ParameterMap。
Dynamic Input的使用需要结合Module Input来进行。我们知道Module Input需要在Emitter的界面上选中Module后指定，这个指定可以有很多不同的形式：一是我们直接书写默认值上去，二是我们拖到某个同类型的变量到上面去进行关联赋值，三是我们书写一行HLSL的计算式来赋值，四是我们使用Dynamic Input来赋值。这也就意味着，我们的输出只需要一个具体的数据，不需要ParameterMap，而我们的输入能利用我们能利用的任何内容。
逻辑上讲，Dynamic Input需要关联输出给某个具体的输入值上，因此输出只能有一个类型，并且必须严格匹配才能索引到，不能隐性进行类型转换。但是实际上你可以添加多个输出参数。添加多个输出类型后，你就不能在变量设置中选择Dynamic Input时索引到这个Dynamic Input。然而当我们合理设置了Dynamic Input后，再添加Output的数量，不会影响到Emitter的编译。
### Niagara参数集
Niagara Parameter Collection（NPC）与Material Parameter Colloction（MPC）类似，是一种全局的数据参数集。
最简单的使用方法就是在指定ModuleInput的值时，我们能直接在下拉菜单中的Link Inputs-NPC中找到所有存在的NPC的值。如果我们想在Script中使用，则需要在左侧Parameter页面中，点击右上角的小眼睛查看高级信息才能看到添加的入口，此处我们也能添加任何NPC中存在的值，之后拖到Script的MapGet节点上使用。
NPC的值的改变除了手动设置外，也可以通过蓝图进行配置，因此也是一个与逻辑层进行通信的方便的工具。
同MPC有Instance意义，NPC也有Instance，对应某个具体的全局设置，若不同粒子系统共享同一套参数，但是需要不同的数值，应该使用Instance类进行配置。
粒子系统中使用NPC的参数是不会区分Instance的，我们需要在粒子系统的层级上指定我们要使用的Instance，在SystemProperties中进行如下设置![[Pasted image 20231022223331.png]]
### **Niagara效果类型**
这就是一组配置粒子系统的Scalability层级的参数集。假如我们的粒子系统有很多，且为了适应不同的性能的平台需要进行统一的不同的配置，就可以使用这个组件来完成统一的设置与修改。
该组件的使用通过粒子系统中的SystemProperties的设置来完成
![[Pasted image 20231022223751.png]]
