接口类有助于确保一组（可能）不相关的类实现一组通用函数。

接口继承的直接就是UInterface而不是UClass
使用UINTERFACE宏而不是UCLASS宏
```cpp
UINTERFACE([specifier, specifier, ...], [meta(key=value, key=value, ...)])
class UClassName : public UInterface
{
	GENERATED_BODY()
};
```
>其次，UINTERFACE类不是实际的接口；==它是一个空白类，它的存在只是为了向虚幻引擎反射系统确保可见性。==将由其他类继承的实际接口必须具有相同的类名，但是开头字母"U"必须改为"I"。

```cpp
UINTERFACE(MinimalAPI, Blueprintable)
class UReactToTriggerInterface : public UInterface
{
	GENERATED_BODY()
};

class IReactToTriggerInterface
{
	GENERATED_BODY()

public:
	/** 在此处添加接口函数声明 */
};
```
>如果你想要让蓝图实现此接口，则需要"Blueprintable"说明符。蓝图类要覆盖的每个接口函数都必须是"BlueprintNativeEvent"或"BlueprintImplementableEvent"。标记为"BlueprintCallable"的函数仍然可以被调用，但不能被覆盖。你将无法从蓝图访问所有其他函数。

### 仅C++的接口函数
可以在接口的头文件中声明一个不带 `UFUNCTION` 说明的虚拟C++函数。这些函数必须为虚拟的，以便在实现接口的类中覆盖它们。
可提供一个默认实现。
当在一个Actor类中实现接口后，可以创建并实现一个针对该类的覆盖。
### 蓝图可调用接口函数
要创建蓝图可调用的接口函数，必须在带 `BlueprintCallable` 说明符的函数声明中提供一个 `UFUNCTION` 宏。还必须使用 `BlueprintImplementableEvent` 或 `BlueprintNativeEvent` 说明，==而且函数不能为虚拟的==。
>当使用`BlueprintImplementableEvent`时C++中不能实现这个函数，必须在蓝图中编写逻辑。

>在C++中，可通过覆盖一个同名函数来实现使用 `BlueprintNativeEvent` 的函数，但要在末尾添加上后缀 `_Implementation`。

```cpp
//Trap.h
public:
	bool ReactToTrigger_Implementation() override;

//Trap.cpp
bool ATrap::ReactToTrigger_Implementation() const
	{
		return false;
	}
```
### 确定类是否实现了接口
为了与实现接口的C++和蓝图类兼容，可以使用以下任意函数：
```cpp
bool bIsImplemented = OriginalObject->GetClass()->ImplementsInterface(UReactToTriggerInterface::StaticClass()); // 如果OriginalObject实现了UReactToTriggerInterface，则bisimplemated将为true。

bIsImplemented = OriginalObject->Implements<UReactToTriggerInterface>(); // 如果OriginalObject实现了UReactToTrigger，bIsImplemented将为true。

IReactToTriggerInterface* ReactingObject = Cast<IReactToTriggerInterface>(OriginalObject); // 如果OriginalObject实现了UReactToTriggerInterface，则ReactingObject将为非空。
```
>如果"StaticClass"函数在"前缀为I（I-prefixed）"的类中没有实现，尝试在"前缀为U（U-prefixed）"的类上使用"转换（Cast）"将失败，代码将无法编译。