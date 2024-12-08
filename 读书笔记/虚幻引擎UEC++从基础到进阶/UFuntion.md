UPARAM(ref)
如果一个函数在蓝图中传了一个数组到C++，但C++中并没有收到，可以使用这个
>注意将传入的数组加上`&`

纯函数在每次被调用时候都会执行一次，而正常的函数则只有在执行时才会执行，其他时候调用都是调用之前执行的数据。
```cpp
UFUNCTION(BlueprintPure)  
float BlueprintPureFunction();  
  
UFUNCTION(BlueprintCallable)  
float BlueprintCallableFunction();  
  
UFUNCTION(BlueprintCallable)  
int32 BlueprintCallableConstFunction() const;  
  
UFUNCTION(BlueprintPure=fasle)  
int32 BlueprintPureFalseFunction() const;
```
>C++中加入`BlueprintPure`标识则会将函数变为纯函数
>使用了`const`为后缀也可变为纯函数
>也可以使用`BlueprintPure=fasle`来使得加了`const`为后缀不变为纯函数
![[Pasted image 20241005142713.png]]
