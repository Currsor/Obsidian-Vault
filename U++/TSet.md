`TSet` 类似于 `TMap` 和 `TMultiMap`，但有一个重要区别：`TSet` 是通过对元素求值的可覆盖函数，使用数据值==本身作为键==，而不是将数据值与独立的键相关联。`TSet` 可以非常快速地添加、查找和删除元素（恒定时间）。默认情况下，`TSet` 不支持重复的键，但使用模板参数可激活此行为。

和 `TArray` 一样，`TSet` 是同质容器。换而言之，其所有元素均完全为相同类型。

TSet 是基于散列的集合，通过 KeyFuncs 确定键和散列方式，默认不允许重复键。可通过扩展 DefaultKeyFuncs 自定义键功能。TSet 使用特定分配器控制内存分配，不使用标准 UE4 分配器，且元素排序不稳定，内存中不连续。移除元素后，空间可能被新元素填补，但指针可能因内存重新分配而失效。
___
# 创建和填充集合
`TSet` 的创建方法
`TSet<FString> FruitSet;`

填充集合的标准方法是使用 `Add` 函数并提供键（元素）：
```CPP
	FruitSet.Add(TEXT("Banana"));
	FruitSet.Add(TEXT("Grapefruit"));
	FruitSet.Add(TEXT("Pineapple"));
	// FruitSet == [ "Banana", "Grapefruit", "Pineapple" ]
```
由于此集合使用了默认分配器，可以确保键是唯一的。如果尝试添加重复键，会发生以下情况：
```CPP
	FruitSet.Add(TEXT("Pear"));
	FruitSet.Add(TEXT("Banana"));
	// FruitSet == [ "Banana", "Grapefruit", "Pineapple", "Pear" ]
	// Note:Only one banana entry.
```

和 `TArray` 一样，还可使用 `Emplace` 代替 `Add`，避免插入集合时创建临时文件

可使用 `Append` 函数进行合并来插入另一个集合中的所有元素
```CPP
	TSet<FString> FruitSet2;
	FruitSet2.Emplace(TEXT("Kiwi"));
	FruitSet2.Emplace(TEXT("Melon"));
	FruitSet2.Emplace(TEXT("Mango"));
	FruitSet2.Emplace(TEXT("Orange"));
	FruitSet.Append(FruitSet2);
	// FruitSet == [ "Banana", "Grapefruit", "Pineapple", "Pear", "Orange", "Kiwi", "Melon", "Mango" ]
```
___
# 编辑
如果用 `UPROPERTY` 宏和一个可编辑的关键词（`EditAnywhere`、`EditDefaultsOnly` 或 `EditInstanceOnly`）标记 `TSet`，则可在虚幻编辑器中添加和编辑元素.
___
# 迭代
与TArray类似[[TArray#迭代]]、
___
# 查询
使用 `FSetElementId` 结构体可查找集合中某个键的索引。然后，就可使用该索引与 `运算符[]` 查找元素。在非常量集合上调用 `operator[]` 将返回非常量引用，而在常量集合上调用将返回常量引用。

```cpp
	FSetElementId BananaIndex = FruitSet.Index(TEXT("Banana"));
	// BananaIndex is a value between 0 and (FruitSet.Num() - 1)
	FPlatformMisc::LocalPrint(
		*FString::Printf(
			TEXT(" \"%s\"\n"),
			*FruitSet[BananaIndex]
		)
	);
	// Prints "Banana"

	FSetElementId LemonIndex = FruitSet.Index(TEXT("Lemon"));
	// LemonIndex is INDEX_NONE (-1)
	FPlatformMisc::LocalPrint(
		*FString::Printf(
			TEXT(" \"%s\"\n"),
			*FruitSet[LemonIndex]
		)
	); // Assert!
```

如果不确定集合中是否包含某个键，可使用 `Contains` 函数和 `运算符[]` 进行检查。但这并非理想的方法，因为同一键需要进行两次查找才能获取成功。使用 `Find` 函数查找一次即可完成这些行为。如果集合中包含该键，`Find` 将返回指向元素数值的指针。如果映射不包含该键，则返回null。对常量集合调用`Find`，返回的指针也将为常量。

```cpp
	FString* PtrBanana = FruitSet.Find(TEXT("Banana"));
	FString* PtrLemon = FruitSet.Find(TEXT("Lemon"));
	// *PtrBanana == "Banana"
	//  PtrLemon == nullptr
```

`Array` 函数会返回一个 `TArray`，其中填充了 `TSet` 中每个元素的一份副本。被传递的数组在填入前会被清空，因此元素的生成数量将始终等于集合中的元素数量：

```cpp
	TArray<FString> FruitArray = FruitSet.Array();
	// FruitArray == [ "Banana","Grapefruit","Pineapple","Pear","Orange","Kiwi","Melon","Mango" ] (order may vary)
```
___
# 移除
同TArray[[TArray#移除]]
___
# 排序
`TSet` 可以排序。排序后，迭代集合会以排序的顺序显示元素，但下次修改集合时，排序可能会发生变化。由于排序不稳定，可能按任何顺序显示集合中支持重复键的等效元素。

`Sort` 函数使用指定排序顺序的二进制谓词，如下所示：
```cpp
	FruitSet.Sort([](const FString& A, const FString& B) {
		return A > B; // sort by reverse-alphabetical order
	});
	// FruitSet == [ "Pear", "Orange", "Melon", "Mango", "Kiwi", "Grapefruit" ] (order is temporarily guaranteed)

	FruitSet.Sort([](const FString& A, const FString& B) {
		return A.Len() < B.Len(); // sort strings by length, shortest to longest
	});
	// FruitSet == [ "Pear", "Kiwi", "Melon", "Mango", "Orange", "Grapefruit" ] (order is temporarily guaranteed)
```
___
# Slack
`TSet` 不像 `TArray` 中的 `Max` 函数那样可检查预分配元素的数量。
以下代码可在不取消任何内存的情况下移除集合中的所有元素，从而产生slack：
```cpp
	FruitSet.Reset();
	// FruitSet == [ <invalid>, <invalid>, <invalid>, <invalid>, <invalid>, <invalid> ]
```
使用 `Reserve` 函数可直接创建slack，例如在添加元素之前预分配内存。
> 预先分配slack会导致以倒序添加新元素。与数组不同，集合不维护元素排序，处理集合的代码不能指望元素排序稳定或可预测。