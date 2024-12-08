`TArray` 类型由两大属性定义：**元素类型**和可选**分配器**。

单个 `TArray` 中==不能存储不同类型的元素==。

`Tarray` 为数值类型。其设计时==未考虑扩展问题==，因此 在实际操作中勿使用 `新建（new）` 和 `删除（delete）` 创建或销毁 `TArray` 实例。元素也为数值类型，为容器所拥有。

`TArray` 被销毁时其中的元素也将被销毁。若在另一TArray中创建TArray变量，其元素将复制到新变量中，且不会共享状态。
___
# 创建和填充数组
```cpp
TArray<int32> IntArray;
```
无指定分配器，因此 `TArray` 将采用基于堆的默认分配器。此时尚未分配内存。

有多种填充 `Tarray` 的方法。
- `Init`：将大量元素副本填入数组。
	`IntArray.Init(10, 5);`
	`// IntArray == [10,10,10,10,10]`
- `Add`：在数组末尾新建对象。
- `Emplace`：原地构造函数，比add更高效。[[Add and Emplace|详情]]
- `Insert`：将在给定索引处添加单个元素或元素数组的副本。
- `Append`：可一次性添加其他 `TArray` 中的多个元素，或者指向常规C数组的指针及该数组的大小。
- `AddUnique`：仅在不存在等值时添加。
- `SetNum`：函数可直接设置数组元素的数量。
___
# 迭代
有多种方法可迭代数组的元素，建议使用C++的范围（[[ranged-for]]）功能
```cpp
FString JoinedStr;
for (auto& Str :StrArr)
{
	JoinedStr += Str;
	JoinedStr += TEXT(" ");
}
// JoinedStr == "Hello Brave World of Tomorrow !"
```
同时也可使用基于索引的常规迭代

还可通过数组迭代器类型控制迭代。函数 [[CreateIterator And CreateConstIterator]] 可分别用于元素的读写和只读访问
___
# 排序
虚幻引擎中有三个排序方法，`Sort`,`HeapSort`,`StableSort`
1. **Sort**:
	 - **基本介绍**：在虚幻引擎中，`Sort`通常指的是一个高效的排序函数，它内部实现是快速排序（QuickSort）。快速排序因其平均时间复杂度为O(n log n)而被广泛使用。
	 
	字符串按长度排序。注意：与之前相比，数组中长度相同的字符串的相对排序发生了变化。这是因为 `Sort` 不稳定，等值元素（因为只比较长度，所以此处字符串为等值）的相对排序无法保证。
2. **HeapSort**:
    
    - **基本介绍**：堆排序（HeapSort）是一种基于比较的排序算法，它使用了二叉堆（通常是最大堆或最小堆）的数据结构。堆排序的过程首先将待排序的元素构建成一个堆，然后重复执行移除堆顶元素并恢复堆的性质的操作。
    - **用途**：虽然堆排序在实践中不如快速排序常用，但它在某些特定情况下（如需要频繁地找到并删除最大或最小元素）非常有效。
    - **特点**：时间复杂度为O(n log n)，不是稳定的排序算法。
3. **StableSort**:
    
    - **基本介绍**：稳定排序指的是在排序过程中保持相等元素的相对顺序不变的排序算法。虚幻引擎中的`StableSort`使用的是归并排序（MergeSort）算法，因为归并排序是稳定的。==区分大小写==，小写排在前面。
    - **用途**：当排序的元素包含多个键，且需要保持相同键元素的相对顺序时，稳定排序是必要的。
    - **特点**：保证稳定性，时间复杂度通常是O(n log n)。
___
# 查询
- `Num` 函数可查询数组保存的元素数量

- `GetData` 函数将返回指向数组元素的指针。若数组变动可能地址发生改变，指针指向可能失效
	还可使用 `Last` 函数从数组末端反向索引。索引默认为零。`Top` 函数是 `Last` 的同义词，唯一区别是其不接受索引
	```cpp
	FString ElemEnd  = StrArr.Last();
	FString ElemEnd0 = StrArr.Last(0);
	FString ElemEnd1 = StrArr.Last(1);
	FString ElemTop  = StrArr.Top();
	// ElemEnd  == "Tomorrow"
	// ElemEnd0 == "Tomorrow"
	// ElemEnd1 == "World"
	// ElemTop  == "Tomorrow"
	```
	
- `Contains`询问数组是否包含特定元素
	```cpp
	bool bHello   = StrArr.Contains(TEXT("Hello"));
	bool bGoodbye = StrArr.Contains(TEXT("Goodbye"));
	// bHello   == true
	// bGoodbye == false
	```

- `ContainsByPredicate`询问数组是否包含与特定谓词匹配的元素
	```cpp
	bool bLen5 = StrArr.ContainsByPredicate([](const FString& Str){
		return Str.Len() == 5;
	});
	bool bLen6 = StrArr.ContainsByPredicate([](const FString& Str){
		return Str.Len() == 6;
	});
	// bLen5 == true
	// bLen6 == false
	```

- `IndexOfByKey`使用于键值对中
> 和Contains一样有ByPredicate对应的`IndexOfByPredicate`

- `FindByKey`与`IndexOfByKey`相似将元素和任意对象进行对比，但返回指向所找到元素的指针。如未找到元素，则返回`nullptr`。
> `FindByPredicate` 的使用方式和 `IndexOfByPredicate` 相似，不同点是返回指针而非索引

- `FilterByPredicate` 函数可获取与特定谓词匹配的元素数组

- `Find`可查找元素。可使用Find确定元素是否存在并返回其索引
	`if (StrArr.Find(TEXT("Hello"), Index)){}`
	如存在重复元素而希望找到最末元素的索引，则使用 `FindLast` 函数
	两个函数均会返回布尔，指出是否已找到元素，同时在找到元素索引时将其写入变量。
	如未找到元素，将返回特殊 `INDEX_NONE` 值

- `GetTypeSize`返回单元素大小

- `GetAllocatedSize`返回数组实际分配的内存大小

- `IsValidIndex` 函数询问容器，可确定特定索引是否有效
___
# 移除
- `Remove` 函数族用于移除数组中的元素。`Remove` 函数将根据元素类型的 `运算符==` 函数==移除所有==与提供元素等值的元素。

- `RemoveSingle` 用于擦除数组中的首个匹配元素。

- `RemoveAt` 函数也可用于按照从零开始的索引移除元素。可使用 `IsValidIndex` 确定数组中的元素是否使用计划提供的索引，将无效索引传递给此函数会导致运行时错误

- `RemoveAll` 也可用于函数移除与谓词匹配的元素。
	```cpp
	ValArr.RemoveAll([](int32 Val) {
		return Val % 3 == 0;
	});
	// ValArr == [10,5,25]
```

- 如果不需要保持集合中剩余元素的顺序，可以使用`RemoveSwap`、`RemoveAtSwap`和`RemoveAllSwap`这类函数来减少操作的开销。这些函数通过交换元素的方式来避免不必要的元素移动，从而提高效率。与它们的非交换变种相比，这些函数在移除元素后不保证其他元素的相对顺序，但正因为如此，它们可以更快地完成任务，适用于对元素顺序不敏感的场景。

- `Empty` 函数移除数组中所有元素
___
# 运算符
- 作为 `Append` 函数的替代，可使用 `运算符+=` 对数组进行串联

- 使用 `MoveTemp` 函数可调用这些语义。移动后，源数组必定为空
	```cpp
	ValArr3 = MoveTemp(ValArr4);
	// ValArr3 == [5,2,3,1,2,3]
	// ValArr4 == []
```

- 使用 `运算符==` 和 `运算符!=` 可对数组进行比较。元素的排序很重要：只有元素的顺序和数量相同时，两个数组才被视为相同。
___
# 堆
`TArray` 拥有支持二叉堆数据结构的函数。堆是一种二叉树，其中父节点的排序等于或高于其子节点。作为数组实现时，树的根节点位于元素0，索引N处节点的左右子节点的指数分别为2N+1和2N+2。子节点彼此间不存在特定排序。

调用 `Heapify` 函数可将现有数组转换为堆。此会重载为是否接受谓词，无谓词的版本将使用元素类型的 `运算符<` 确定排序

HeapPush函数可将新元素添加到堆，对其他节点进行重新排序，以对堆进行维护

`HeapPop` 和 `HeapPopDiscard` 函数用于移除堆的顶部节点。这两个函数的区别在于前者引用元素的类型来返回顶部元素的副本，而后者只是简单地移除顶部节点，不进行任何形式的返回。两个函数得出的数组变更一致，重新正确排序其他元素可对堆进行维护
> `HeapTop` 检查堆的顶部节点，无需变更数组`int32 Top = HeapArr.HeapTop();`

`HeapRemoveAt` 将删除数组中给定索引处的元素，然后重新排列元素，对堆进行维护
___
# Slack
数组分配器预分配额外内存以减少重复分配，导致存在未使用的预分配存储槽（Slack元素），数组实际元素与分配内存之间的差值即为Slack量。

使用 `GetSlack` 函数可找出数组中的Slack量。通过 `Max` 函数可获取容器重新分配前数组可保存的最大元素数量。`GetSlack` 等同 `Max` 和 `Num` 间的差值
```cpp
	TArray<int32> SlackArray;
	// SlackArray.GetSlack() == 0
	// SlackArray.Num()      == 0
	// SlackArray.Max()      == 0

	SlackArray.Add(1);
	// SlackArray.GetSlack() == 3
	// SlackArray.Num()      == 1
	// SlackArray.Max()      == 4

	SlackArray.Add(2);
	SlackArray.Add(3);
	SlackArray.Add(4);
	SlackArray.Add(5);
	// SlackArray.GetSlack() == 17
	// SlackArray.Num()      == 5
	// SlackArray.Max()      == 22
```

管理Slack可优化数组操作，预先保留空间以应对大批元素添加，避免重复内存分配。`Empty`函数支持可选Slack参数以实现此功能。
```cpp
	SlackArray.Empty();
	// SlackArray.GetSlack() == 0
	// SlackArray.Num()      == 0
	// SlackArray.Max()      == 0
	SlackArray.Empty(3);
	// SlackArray.GetSlack() == 3
	// SlackArray.Num()      == 0
	// SlackArray.Max()      == 3
	SlackArray.Add(1);
	SlackArray.Add(2);
	SlackArray.Add(3);
	// SlackArray.GetSlack() == 0
	// SlackArray.Num()      == 3
	// SlackArray.Max()      == 3
```

`Reset`函数与`Empty`函数在处理内存分配上有所不同，`Reset`在当前内存分配满足请求的Slack时不会释放内存，而在请求的Slack较大时则会分配更多内存。
```cpp
	SlackArray.Reset(0);
	// SlackArray.GetSlack() == 3
	// SlackArray.Num()      == 0
	// SlackArray.Max()      == 3
	SlackArray.Reset(10);
	// SlackArray.GetSlack() == 10
	// SlackArray.Num()      == 0
	// SlackArray.Max()      == 10
```

`Shrink` 函数可移除所有Slack。此才做将把内存分配调整为保存当前元素所需的最小内存。`Shrink` 不会对数组中的元素产生影响。
___
# 原始内存
==本质上而言，`TArray` 只是分配内存周围的包装器。==
TArray 是一个内存分配的包装器，可便捷地修改字节和创建元素。尽管它会充分利用现有信息执行操作，但有时需降低操作级别以实现功能。

`AddUninitialized` 和 `InsertUninitialized` 函数可将未初始化的空间添加到数组。两者工作方式分别与 `Add` 和 `Insert` 函数相同，只是不调用元素类型的构造函数。若要避免调用构造函数，建议使用此类函数。类似以下范例的情况中建议使用此类函数，其中计划用 `Memcpy` 调用完全覆盖结构体：
```cpp
	int32 SrcInts[] = { 2, 3, 5, 7 };
	TArray<int32> UninitInts;
	UninitInts.AddUninitialized(4);
	FMemory::Memcpy(UninitInts.GetData(), SrcInts, 4*sizeof(int32));
	// UninitInts == [2,3,5,7]
```
``FMemory::Memcpy``用于将一定数量的字节从源内存地址复制到目标内存地址。这个函数是线程安全的，并且在内部对齐和性能优化上进行了特殊处理，以适应游戏引擎的高性能要求。

`AddZeroed` 和 `InsertZeroed` 的工作方式相似，不同点是会将添加/插入的空间字节清零
```cpp
	struct S
	{
		S(int32 InInt, void* InPtr, float InFlt)
			:Int(InInt)
			, Ptr(InPtr)
			, Flt(InFlt)
		{
		}
		int32 Int;
		void* Ptr;
		float Flt;
	};
	TArray<S> SArr;
	SArr.AddZeroed();
	// SArr == [{ Int:0, Ptr: nullptr, Flt:0.0f }]
```

`SetNumUninitialized` 和 `SetNumZeroed` 函数的工作方式与 `SetNum` 类似，不同之处在于新数量大于当前数量时，将保留新元素的空间为未初始化或按位归零。与 `AddUninitialized` 和 `InsertUninitialized` 函数相同，必要时需将新元素正确构建到新空间中
```cpp
	SArr.SetNumUninitialized(3);
	new ((void*)(SArr.GetData() + 1)) S(5, (void*)0x12345678, 3.14);
	new ((void*)(SArr.GetData() + 2)) S(2, (void*)0x87654321, 2.72);
	// SArr == [
	//   { Int:0, Ptr: nullptr,    Flt:0.0f  },
	//   { Int:5, Ptr:0x12345678, Flt:3.14f },
	//   { Int:2, Ptr:0x87654321, Flt:2.72f }
	// ]

	SArr.SetNumZeroed(5);
	// SArr == [
	//   { Int:0, Ptr: nullptr,    Flt:0.0f  },
	//   { Int:5, Ptr:0x12345678, Flt:3.14f },
	//   { Int:2, Ptr:0x87654321, Flt:2.72f },
	//   { Int:0, Ptr: nullptr,    Flt:0.0f  },
	//   { Int:0, Ptr: nullptr,    Flt:0.0f  }
	// ]
```
>   应谨慎使用"Uninitialized"和"Zeroed"函数族。如函数类型包含要构建的成员或未处于有效按位清零状态的成员，可导致数组元素无效和未知行为。此类函数适用于固定的数组类型，例如FMatrix和FVector。