`CreateIterator` 方法返回一个迭代器，允许你遍历数组并修改数组中的元素。  
`TArray<FString>::TIterator It = StrArr.CreateIterator(); `
在迭代器有效期内，你可以使用` *It `来访问当前元素，并且可以使用` ++It `来移动到下一个元素。`

`CreateConstIterator` 方法返回一个常量迭代器，它允许你遍历数组，但不允许修改数组中的元素。  
`TArray<FString>::TConstIterator It = StrArr.CreateConstIterator();`
在这个迭代器中，你不能通过` *It` 来修改元素，它只允许读取。