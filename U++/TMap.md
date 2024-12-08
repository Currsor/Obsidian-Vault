映射有两种类型：`TMap` 和 `TMultiMap`。两者之间的不同点是，`TMap` 中的键是==唯一==的，而`TMultiMap` 可存储==多个相同==的键。在 `TMap` 中添加新的键值时，若所用的键与原有的对相同，新对将替换原有的对。在 `TMultiMap` 中，容器可以同时存储新对和原有的对。

元素类型实际上是 `TPair<KeyType, ElementType>`，但很少需要直接引用 `TPair` 类型。

`TMap` 是散列容器，这意味着键类型必须支持 `GetTypeHash` 函数，并提供 `运算符==` 来比较各个键是否等值。
`KeyFuncs` 为 TMap 模板参数，定义了从元素类型获取键、键的比较及散列计算的方法。默认情况下，它们只会返回对键的引用，使用 `运算符==` 确定相等性，并调用非成员 `GetTypeHash` 函数进行散列计算。若键类型已支持这些操作，无需自定义 `KeyFuncs`。

TMap 使用可选的集合分配器来管理内存，这些分配器与UE4的标准分配器不同，它们允许自定义散列桶数量和存储散列及元素所用的标准UE4分配器。

TMap的元素排序==不可靠且可能不连续==，迭代顺序可能与添加顺序不同，其数据结构为支持空位的稀疏数组，元素移除后可能留下空位，新元素添加可填补这些空位。但即使不重新排序，存储器重新分配可能导致指向元素的指针失效。
___
# 创建和填充映射
`TMap` 的创建
```cpp
	TMap<int32, FString> FruitMap;
```

`FruitMap` 现在是一个字符串的空 `TMap`，该字符串由整数键标识。我们既没有指定分配器，也没有指定 `KeyFuncs`，所以映射将执行标准的堆分配，使用 `运算符==` 对键进行对比（`int32` 类型），并使用 `GetTypeHash` 进行散列运算。此时没有分配任何内存。

填充映射的标准方法是调用带一个键和值的 `Add` 函数
```cpp
	FruitMap.Add(5, TEXT("Banana"));
	FruitMap.Add(2, TEXT("Grapefruit"));
	FruitMap.Add(7, TEXT("Pineapple"));
	// FruitMap == [
	// 	{ Key:5, Value:"Banana"     },
	// 	{ Key:2, Value:"Grapefruit" },
	// 	{ Key:7, Value:"Pineapple"  }
	// ]
```
> 此处的元素按插入顺序排列，但不保证这些元素在内存中实际保留此排序。如果是新的映射，可能会保留插入排序，但插入和删除的次数越多，新元素不出现在末尾的可能性就越大。

`Add` 函数可接受不带值的键。调用此重载后的 `Add` 时，值将被默认构建
```cpp
	FruitMap.Add(4);
	// FruitMap == [
	// 	{ Key:5, Value:"Banana"    },
	// 	{ Key:2, Value:"Pear"      },
	// 	{ Key:7, Value:"Pineapple" },
	// 	{ Key:4, Value:""          }
	// ]
```

和 `TArray` 一样，还可使用 `Emplace` 代替 `Add`，防止插入映射时创建临时文件
此处直接将键和值传递给了各自的构造函数。这对 `int32` 键实际上没有影响，但避免了为该值创建临时 `FString`。

可使用 `Append` 函数合并映射，将一个映射的所有元素移至另一个映射
在将 B 的元素添加到 A 中时，B 的元素会覆盖与之键相同的 A 中元素，且添加完成后 B 将被清空。
___
# 迭代
`TMaps` 的迭代类似于[[TArray#迭代| TArrays迭代]]。可使用C++的设置范围功能，注意元素类型是 `TPair`

也可以用 `CreateIterator` 和 `CreateConstIterators` 函数来创建迭代器。[[CreateIterator And CreateConstIterator]]
```cpp
	for (auto It = FruitMap.CreateConstIterator(); It; ++It)
	{
		FPlatformMisc::LocalPrint(
			*FString::Printf(
				TEXT("(%d, \"%s\")\n"),
				It.Key(),   // same as It->Key
				*It.Value() // same as *It->Value
			)
		);
	}
```
___
# 查询
`Num` 函数即可查询映射中保存的元素数量

要确定映射是否包含特定键，可按下方所示调用 `Contains` 函数
```cpp
	bool bHas7 = FruitMap.Contains(7);
	bool bHas8 = FruitMap.Contains(8);
	// bHas7 == true
	// bHas8 == false
```

可使用 `运算符[]` 查找相应值，将键用作索引。使用非常量映射执行该操作将返回非常量引用，使用常量映射将返回常量引用。
> 在使用 `运算符[]` 前，应检查映射中是否存在该键。如果映射中键不存在，将触发断言。

如果不确定映射中是否包含某个键，可使用 `Contains` 函数和 `运算符[]` 进行检查。但这并非理想的方法，因为同一键需要进行两次查找才能获取成功。使用 `Find` 函数查找一次即可完成这些行为。如果映射包含该键，`Find` 将返回指向元素数值的指针。如果映射不包含该键，则返回null。在常量映射上调用 `Find`，返回的指针也将为常量。
```cpp
	FString* Ptr7 = FruitMap.Find(7);
	FString* Ptr8 = FruitMap.Find(8);
	// *Ptr7 == "Pineapple"
	//  Ptr8 == nullptr
```

`FindOrAdd` 和 `FindRef` 是用于处理映射（map）中元素查找和添加的函数。`FindOrAdd` 在键不存在时添加新元素并返回引用，`FindRef` 则返回键对应的值副本或默认值。两者都简化了查找流程，无需预先检查键是否存在。
```CPP
	FString& Ref7 = FruitMap.FindOrAdd(7);
	// Ref7     == "Pineapple"
	// FruitMap == [
	// 	{ Key:5, Value:"Mango"     },
	// 	{ Key:2, Value:"Pear"      },
	// 	{ Key:7, Value:"Pineapple" },
	// 	{ Key:4, Value:"Kiwi"      },
	// 	{ Key:3, Value:"Orange"    },
	// 	{ Key:9, Value:"Melon"     }
	// ]
	FString& Ref8 = FruitMap.FindOrAdd(8);
	// Ref8     == ""
	// FruitMap == [
	// 	{ Key:5, Value:"Mango"     },
	// 	{ Key:2, Value:"Pear"      },
	// 	{ Key:7, Value:"Pineapple" },
	// 	{ Key:4, Value:"Kiwi"      },
	// 	{ Key:3, Value:"Orange"    },
	// 	{ Key:9, Value:"Melon"     },
	// 	{ Key:8, Value:""          }
	// ]

	FString Val7 = FruitMap.FindRef(7);
	FString Val6 = FruitMap.FindRef(6);
	// Val7     == "Pineapple"
	// Val6     == ""
	// FruitMap == [
	// 	{ Key:5, Value:"Mango"     },
	// 	{ Key:2, Value:"Pear"      },
	// 	{ Key:7, Value:"Pineapple" },
	// 	{ Key:4, Value:"Kiwi"      },
	// 	{ Key:3, Value:"Orange"    },
	// 	{ Key:9, Value:"Melon"     },
	// 	{ Key:8, Value:""          }
	// ]
```

`FindKey` 函数执行逆向查找，这意味着提供的值与键匹配，并返回指向与所提供值配对的第一个键的指针。搜索映射中不存在的值将返回空键。
```cpp
	const int32* KeyMangoPtr   = FruitMap.FindKey(TEXT("Mango"));
	const int32* KeyKumquatPtr = FruitMap.FindKey(TEXT("Kumquat"));
	// *KeyMangoPtr   == 5
	//  KeyKumquatPtr == nullptr
```
> 按值查找比按键查找慢（线性时间）。这是因为映射是根据键而不是值进行哈希。此外，如果映射有多个具有相同值的键，`FindKey` 可返回其中任一键。

`GenerateKeyArray` 和 `GenerateValueArray` 分别使用所有键和值的副本来填充 `TArray`。在这两种情况下，都会在填充前==清空==所传递的数组，因此产生的元素数量始终等于映射中的元素数量。
```cpp
	TArray<int32>   FruitKeys;
	TArray<FString> FruitValues;
	FruitKeys.Add(999);
	FruitKeys.Add(123);
	FruitMap.GenerateKeyArray  (FruitKeys);
	FruitMap.GenerateValueArray(FruitValues);
	// FruitKeys   == [ 5,2,7,4,3,9,8 ]
	// FruitValues == [ "Mango","Pear","Pineapple","Kiwi","Orange",
	//                  "Melon","" ]
```
___
# 移除
从映射中移除元素的方法是使用 `Remove` 函数并提供要移除元素的键。返回值是被移除元素的数量。如果映射不包含与键匹配的元素，则返回值可为零。

`FindAndRemoveChecked` 函数可用于从映射移除元素并返回其值。名称的"已检查"部分表示若键不存在，映射将调用 `check`（UE4中等同于 `assert`）。
```cpp
	FString Removed7 = FruitMap.FindAndRemoveChecked(7);
	// Removed7 == "Pineapple"
```

`RemoveAndCopyValue` 函数的作用与 `Remove` 相似，不同点是会将已移除元素的值复制到引用参数。如果映射中不存在指定的键，则输出参数将保持不变，函数将返回 `false`。
```cpp
	FString Removed;
	bool bFound2 = FruitMap.RemoveAndCopyValue(2, Removed);
	// bFound2  == true
	// Removed  == "Pear"
	// FruitMap == [
	// 	{ Key: 5, Value: "Mango"  },
	// 	{ Key: 4, Value: "Kiwi"   },
	// 	{ Key: 3, Value: "Orange" },
	// 	{ Key: 9, Value: "Melon"  }
	// ]
	bool bFound8 = FruitMap.RemoveAndCopyValue(8, Removed);
	// bFound8  == false
	// Removed  == "Pear", i.e. unchanged
	// FruitMap == [
	// 	{ Key: 5, Value: "Mango"  },
	// 	{ Key: 4, Value: "Kiwi"   },
	// 	{ Key: 3, Value: "Orange" },
	// 	{ Key: 9, Value: "Melon"  }
	// ]
```

使用 `Empty` 或 `Reset` 函数可将映射中的所有元素移除。
>`Empty` 和 `Reset` 相似，但 `Empty` 可采用参数指示映射中保留的slack量，而 `Reset` 则是尽可能多地留出slack量。
___
# 排序
`TMap` 可以进行排序。排序后，迭代映射会以排序的顺序显示元素，但下次修改映射时，排序可能会发生变化。排序是不稳定的，因此等值元素在MultiMap中可能以任何顺序出现。

使用 `KeySort` 或 `ValueSort` 函数可分别按键和值进行排序。两个函数均使用二元谓词来进行排序：
```cpp
	FruitMap.KeySort([](int32 A, int32 B) {
		return A > B; // sort keys in reverse
	});
	// FruitMap == [
	// 	{ Key: 9, Value: "Melon"  },
	// 	{ Key: 5, Value: "Mango"  },
	// 	{ Key: 4, Value: "Kiwi"   },
	// 	{ Key: 3, Value: "Orange" }
	// ]

	FruitMap.ValueSort([](const FString& A, const FString& B) {
		return A.Len() < B.Len(); // sort strings by length
	});
	// FruitMap == [
	// 	{ Key: 4, Value: "Kiwi"   },
	// 	{ Key: 5, Value: "Mango"  },
	// 	{ Key: 9, Value: "Melon"  },
	// 	{ Key: 3, Value: "Orange" }
	// ]
```
___
# 运算符
和 `TArray` 一样，`TMap` 是常规值类型，可通过标准复制构造函数或赋值运算符进行复制。因为映射严格拥有其元素，复制映射的操作是深层的，所以新的映射将拥有其自己的元素副本。

`TMap` 支持移动语义，使用 `MoveTemp` 函数可调用这些语义。在移动后，源映射必定为空：
```cpp
	FruitMap = MoveTemp(NewMap);
	// FruitMap == [
	// 	{ Key: 4, Value: "Kiwi"  },
	// 	{ Key: 5, Value: "Apple" },
	// 	{ Key: 9, Value: "Melon" }
	// ]
	// NewMap == []
```
___
# Slack
`TMap` 不像 `TArray` 中的 `Max` 函数那样可以检查预分配元素的数量。

使用 `Collapse` 和 `Shrink` 函数可移除 `TMap` 中的全部slack。`Shrink` 将从容器的末端移除所有slack，但这会在中间或开始处留下空白元素。
```cpp
	for (int32 i = 0; i < 10; i += 2)
	{
		FruitMap.Remove(i);
	}
	// FruitMap == [
	// 	{ Key: 9, Value: "Fruit9" },
	// 	<invalid>,
	// 	{ Key: 7, Value: "Fruit7" },
	// 	<invalid>,
	// 	{ Key: 5, Value: "Fruit5" },
	// 	<invalid>,
	// 	{ Key: 3, Value: "Fruit3" },
	// 	<invalid>,
	// 	{ Key: 1, Value: "Fruit1" },
	// 	<invalid>
	// ]
	FruitMap.Shrink();
	// FruitMap == [
	// 	{ Key: 9, Value: "Fruit9" },
	// 	<invalid>,
	// 	{ Key: 7, Value: "Fruit7" },
	// 	<invalid>,
	// 	{ Key: 5, Value: "Fruit5" },
	// 	<invalid>,
	// 	{ Key: 3, Value: "Fruit3" },
	// 	<invalid>,
	// 	{ Key: 1, Value: "Fruit1" }
	// ]
```
在上述代码中，`Shrink` 只删除了一个无效元素，因为末端只有一个空元素。要移除所有slack，首先应调用 `Compact` 函数，将空白空间组合在一起，为调用 `Shrink` 做好准备。
```cpp
	FruitMap.Compact();
	// FruitMap == [
	// 	{ Key: 9, Value: "Fruit9" },
	// 	{ Key: 7, Value: "Fruit7" },
	// 	{ Key: 5, Value: "Fruit5" },
	// 	{ Key: 3, Value: "Fruit3" },
	// 	{ Key: 1, Value: "Fruit1" },
	// 	<invalid>,
	// 	<invalid>,
	// 	<invalid>,
	// 	<invalid>
	// ]
	FruitMap.Shrink();
	// FruitMap == [
	// 	{ Key: 9, Value: "Fruit9" },
	// 	{ Key: 7, Value: "Fruit7" },
	// 	{ Key: 5, Value: "Fruit5" },
	// 	{ Key: 3, Value: "Fruit3" },
	// 	{ Key: 1, Value: "Fruit1" }
	// ]
```
___
# KeyFuncs
只要类型具有 `运算符==` 和非成员 `GetTypeHash` 重载，就可用作 `TMap` 的键类型，不需要任何更改。但是，您可能需要将类型用作键，而不重载这些函数。在这些情况下，可对 `KeyFuncs` 进行自定义。为键类型创建 `KeyFuncs`，必须定义两个typedef和三个静态函数，如下所示：

- `KeyInitType` —— 用于传递键的类型。
- `ElementInitType` —— 用于传递元素的类型。
- `KeyInitType GetSetKey(ElementInitType Element)`——返回元素的键。
- `bool Matches(KeyInitType A, KeyInitType B)` —— 如果 `A` 和 `B` 等值将返回 `true`，否则返回 `false`。
- `uint32 GetKeyHash(KeyInitType Key)` —— 返回 `Key` 的散列值。

`KeyInitType` 和 `ElementInitType` 是键类型和值类型的常规传递约定的typedef。它们通常为浅显类型的一个值，和非浅显类型的一个常量引用。请记住，映射的元素类型是 `TPair`。

```cpp
// 定义一个结构体FMyStruct，用于存储具有唯一标识符和一些状态的数据
struct FMyStruct
{
    // FString类型的字段，用于存储唯一标识符
    FString UniqueID;

    // float类型的字段，用于存储一些不影响结构体身份的状态
    float SomeFloat;

    // 显式构造函数，接收一个float类型的参数，用于初始化SomeFloat字段
    // 同时生成一个唯一的GUID字符串，并转换为FString赋值给UniqueID
    explicit FMyStruct(float InFloat)
        : UniqueID(FGuid::NewGuid().ToString())  // 初始化UniqueID为新的GUID字符串
        , SomeFloat(InFloat)                     // 初始化SomeFloat为传入的参数值
    {
    }
};

// 定义一个模板结构体TMyStructMapKeyFuncs，用于提供键函数，以便在映射中使用FMyStruct作为键
template <typename ValueType>
struct TMyStructMapKeyFuncs :
    BaseKeyFuncs<
        TPair<FMyStruct, ValueType>,  // 元素类型是FMyStruct和ValueType的键值对
        FString                       // 键类型是FString
    >
{
private:
    // 使用typedef为基类BaseKeyFuncs的类型定义别名，简化后续代码的书写
    typedef BaseKeyFuncs<
        TPair<FMyStruct, ValueType>,
        FString
    > Super;

public:
    // 定义元素初始化类型和键初始化类型，分别对应于元素和键的初始化类型
    typedef typename Super::ElementInitType ElementInitType;
    typedef typename Super::KeyInitType     KeyInitType;

    // 静态成员函数，用于从元素中提取键
    static KeyInitType GetSetKey(ElementInitType Element)
    {
        // 返回元素中FMyStruct的UniqueID作为键
        return Element.Key.UniqueID;
    }

    // 静态成员函数，用于比较两个键是否匹配
    static bool Matches(KeyInitType A, KeyInitType B)
    {
        // 使用ESearchCase::CaseSensitive进行区分大小写的比较，如果相等则返回true
        return A.Compare(B, ESearchCase::CaseSensitive) == 0;
    }

    // 静态成员函数，用于获取键的哈希值
    static uint32 GetKeyHash(KeyInitType Key)
    {
        // 使用FCrc::StrCrc32计算字符串的CRC32哈希值
        return FCrc::StrCrc32(*Key);
    }
};

```

`FMyStruct` 具有唯一标识符，以及一些与身份无关的其他数据。`GetTypeHash` 和 `运算符==` 不适用于此，因为 `运算符==` 为实现通用目的不应忽略任何类型的数据，但同时又需要如此才能与 `GetTypeHash` 的行为保持一致，后者只关注 `UniqueID` 字段。以下步骤有助于为 `FMyStruct` 创建自定义 `KeyFuncs`：

1. 首先，继承 `BaseKeyFuncs`，因为它可以帮助定义某些类型，包括 `KeyInitType` 和 `ElementInitType`。
    
    `BaseKeyFuncs` 使用两个模板参数：映射的元素类型和键类型。和所有映射一样，元素类型是 `TPair`，使用 `FMyStruct` 作为其 `KeyType`，`TMyStructMapKeyFuncs` 的模板参数作为其 `ValueType`。将备用 `KeyFuncs` 用作模板，可为每个映射指定 `ValueType`，因此每次要在 `FMyStruct` 上创建键控 `TMap` 时不必定义新的 `KeyFuncs`。第二个 `BaseKeyFuncs` 参数是键类型，不要与元素存储的键区（`TPair` 的 `KeyType`）混淆。因为此映射应使用 `UniqueID`（来自 `FMyStruct`）作为键，所以此处使用 `FString`。
    
2. 然后，定义三个必需的 `KeyFuncs` 静态函数。第一个是 `GetSetKey`，该函数返回给定元素类型的键。由于元素类型是 `TPair`，而键是 `UniqueID`，所以该函数可直接返回 `UniqueID`。
    
    第二个静态函数是 `Matches`，该函数接受两个元素的键（由 `GetSetKey` 获取），然后比较它们是否相等。在 `FString` 中，标准的等效测试（`运算符==`）不区分大小写；要替换为区分大小写的搜索，请用相应的大小写对比选项使用 `Compare` 函数。
    
3. 最后，`GetKeyHash` 静态函数接受提取的键并返回其散列值。由于 `Matches` 函数区分大小写，`GetKeyHash` 也必须区分大小写。区分大小写的 `FCrc` 函数将计算键字符串的散列值。
    
4. 现在结构已满足 `TMap` 要求的行为，可创建它的实例。
> 本例指定了默认的集合分配器。因为 `KeyFuncs` 参数处于最后，所以这个 `TMap` 类型需要该参数。

```cpp
		TMap<
			FMyStruct,
			int32,
			FDefaultSetAllocator,
			TMyStructMapKeyFuncs<int32>
		> MyMapToInt32;
		
		// Add some elements
		MyMapToInt32.Add(FMyStruct(3.14f), 5);
		MyMapToInt32.Add(FMyStruct(1.23f), 2);
 
		// MyMapToInt32 == [
		// 	{
		// 		Key: {
		// 			UniqueID:  "D06AABBA466CAA4EB62D2F97936274E4",
		// 			SomeFloat: 3.14f
		// 		},
		// 		Value: 5
		//	},
		// 	{
		// 		Key: {
		// 			UniqueID:  "0661218447650259FD4E33AD6C9C5DCB",
		// 			SomeFloat: 1.23f
		// 		},
		// 		Value: 5
		//	}
		// ]
```

>   在自行设置KeyFuncs时，要注意 `TMap` 假设两个项目使用 `Matches` 比较的结果相等，则它们会从 `GetKeyHash` 返回相同的值。此外，如果对现有映射元素的键进行的修改将会改变来自这两个函数中任一个的结果，那么系统会将这种修改视作未定义的行为，因为这会使映射的内部散列失效。这些规则也适用于使用默认 `KeyFuncs` 时 `运算符==` 和 `GetKeyHash` 的重载。