使用C++创建的Actor是没有根组件的，需要手动添加。

UObject析构函数需要写虚析构，加前缀virtual

UObject* 可以使用UPROPERTY进行包裹，这样可以避免垃圾回收掉其
若不想使用UPROPERTY进行包裹，则可以使用TWeakObjectPtr智能指针，这不会妨碍GC，但可以查询有效性，并且它所指向的Object要被销毁时，它会被设置为空。