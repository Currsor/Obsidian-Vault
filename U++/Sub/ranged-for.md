C++中的范围for循环（ranged-for）提供了一种简洁的方式来迭代容器或数组的元素。使用范围for循环可以直接访问每个元素，而不需要手动管理迭代器或索引。 以下是一个使用范围for循环迭代数组元素的基本语法： 

`for (声明语句 : 范围表达式) { // 对每个元素执行的代码 }`


例如，如果你有一个整数数组，你可以这样迭代它： 
```cpp 
int arr[] = {1, 2, 3, 4, 5}; 
for (int element : arr) 
{ 
	element std::cout << element << std::endl; 
} 
```

这里，`element` 在每次循环中都会被赋予数组中的下一个值。这种方式不仅适用于数组，还适用于STL容器，如`vector`、`list`、`map`等。如果要修改数组元素的值，可以声明一个引用： 
`for (int& element : arr) { element *= 2; // 将每个元素的值翻倍 } `

范围for循环是C++11及以后版本的特性，所以在使用之前请确保你的编译器支持C++11标准。