# 引入头文件
`#include <iostream>`
`#include <string>	 //字符串`
# 运算符号
++I  返回值为i+1；   i++  返回值为i，但i返回之后i加一

# std库
## 引入
`using namespace std;`
## 使用
`cout <<  "HelloWorld!" <<endl`
`cout << "Please tell me y or n./n"`
`cin >> answer`
## 注意
若不用引用，则使用需要加前缀
`std::cout`
# 函数
## 声明
void HelloWorld(char answer);

>void：函数返回值的类型		char answer： 输入值
## 定义
`void HelloWorld(char answer){}`

>若函数定义与主函数以下，则需要在主函数前声明

# if判断
## if
```cpp
if()
{
}
else
{
}
```
## else-if
```cpp
if()
{
}
else if()	//若if语句为假则进行判断else if，若if为真则跳过
{
}
```


# While循环
## while
```cpp
while (_a_>=_b_)
{
}
```
## do-while
```cpp
do
{
} while (_a_>=_b_);
```
## while与do-while区别
While会==先判断条件==，若true则进入循环体，若false则退出循环
Do-while==先进行一遍循环==然后进行判断

# for循环
## 格式
```cpp
for (int i=1;i<=10;i++)
{
}
```
# 引用
## 格式
```cpp
void addOne(int& Num)
{
Num++;
}
```
## 注意点
==**int&引用的是这个输入的变量本身，而int则是引用输入的变量的数值**==
eg：
```cpp
int b=1;
 int& Ref_b=b;
 Ref_b++;
 cout << b <<endl
```
结果:
>2

Ref_b引用b的存储路径，所以Ref_b++则b变为了2
# 字符串
## C
字符串最后一个要是/0，用于告诉程序这是字符串末尾
e.g.:	`char M[5] = { 'Y','N','y','n','\0'};`
## c++
需要添加头文件	`#include <string>`
若没引入std则需要使用前缀	`std::string`
# 常量
const作为前缀加与变量声明前，该变量会变为常量无法改变
==使用const的声明需要给予其初始值==
const int PI=3.14159
用法举例
```cpp
const pawn(const int& Num)
{
std::cout<<Num<<std::endl;
}
int b=2;
pawn(b);
pawn(2);
```
---

> const使得函数输入值可以填入常数，因为其将引用的变量转换为常数，但是这样无法改变引入的变量

# 与门和或门
## 与门 AND
A&&B :若有一方为假则为假
## 或门 OR
A||B ：若有一方为真则为真
# 数组 Array
```cpp
int Num[2];
Num[0]=D;
Num[1]=O;
```
or
`double myArray[] = {{3.1415},{1.2345}};`

>[]之中可以不填元素个数
# 枚举 Enums
```cpp
enum PlayStafus{
PS_Crouched,
PS_Standing,
PS_Running,
PS_Sprinting
}	//建立枚举
PlayStafus Stafus = PS_Crouched;  //声明变量，从枚举中选择
switch(Stafus)
{
case PS_Crouched:
cout<<"Crouched"<<endl;
break;	//break可以直接跳出当前函数
case PS_Standing:
cout<<"Standing"<<endl;
break;
case PS_Running:
cout<<"Running"<<endl;
break;
case PS_Sprinting:
cout<<"Sprinting"<<endl;
break;
default:
cout<<"Inralid choice!"<<endl;	//剔除无效信息
}
```

>枚举若无特别定义则第一个默认值为0，但是可以给予其定义
e.g.：
`enum Array{`
`a=1,`
`b,`
`c	//b为2，c为3`
`}`
`enum Array{`
`d=1,`
`e=10，`
`f=100`
`}`

>若两个枚举之中出现名称相同的时候可以使用前缀解决
eg：
`Stafus = PlayStafus::PS_Running;`
`Array =MoveStafus::PS_Running;``
# switch函数
```cpp
switch(Stafus)
{
case PS_Running:
cout<<"Running"<<endl;
break;
case PS_Sprinting:
cout<<"Sprinting"<<endl;
break;
default:
cout<<"Inralid choice!"<<endl;	//剔除无效信息，处理不是以上所有情况
}
```
# 结构体 Struct
结构体就是自定义一种数据类型
eg：
```cpp
struct Character
{
string Name;
float Health;
int Level;
float Damage;
void Attack()
{
cout<<Name<<Health<<Leve<<Damage<<endl;
}
}	//创立角色这个数据类型
Character Joe;
Joe.Name = "Joe";
Joe.Health = 100.0f;
Joe.Level =1;
Joe.Damage =2.5f;
Joe.Attack();	//创建character类型的变量Joe
Character Lisa = {"Lisa",100.0f,3,15.f};
Lisa.Attack();	//创建character类型的变量Lisa
```
**在c++中如果用typedef的话，会造成区别：**

```cpp
struct Student
{
int a;
}stu1;	//stu1是一个变量
typedef struct tagStudent
{
int a;
}stu2;	//stu2是一个结构体类型，tagStudent为标识符
```
使用时可以直接访问stu1.a//当成成员函数的调用
但是stu2则必须先 stu2 s2;
然后 s2.a=10;
# 指针pointer
指针就是一串十六进制的数字，代表了一个变量的地址，可以减少数据传输加快运行。
## 声明
`int* ptr = &struct;`
## 使用
`cout<<(*ptr).Name<<endl;	//加括号是因为点操作符优先于指针的解引用`
`cout<<ptr->Name<<endl;	    //语法糖写法，好看`
# 类classes
和结构体很像
```
class Dog
{
string name:
void bark()
{
}
}
Dog Spot;
Dog Ref;	//Spot与Ref均有Dog类的所有变量与函数，但这些都是属于它自己的并不是同一个变量与函数
如果想在类以外编辑类之中的函数
Dog::bark()
{
cout<<"Woof!'<<endl;
}
```
# 构造函数
构造函数要与类名称相同，用于改变类中变量的默认值，无法返回值且不为void类
```
Dog::Dog()
{
name="spor";
age=2;
}
```
# 继承
```cpp
#include <iostream>
#include <string.h>
using namespace std;

class Animal
{
public :
	Animal();
	Animal(string name, int age, int num_limbs);

	string Name;
	int Age;
	int NumberOfLimbs;

	void Report();
};

class Dog : public Animal
{
public:
	Dog();
	Dog(string name, int age, int num_limbs);
};

int main()
{
	Dog dog("A",1,4);

	system("pause");
}

Animal::Animal()
{
	cout << "An ANIMAL is born!\n";

	Name = "DEFAULT";
	Age = 2;
	NumberOfLimbs = 4;
}

Animal::Animal(string name, int age, int num_limbs) :
	Name(name), Age(age), NumberOfLimbs(num_limbs)
{
	cout << "Animal(string name, int age, int num_limbs)" << endl;
	Report();
}

void Animal::Report()
{
	cout << "Name: " << Name << endl;
	cout << "Age: " << Age << endl;
	cout << "NumberOfLimbs: " << NumberOfLimbs << endl;
}

Dog::Dog()
{
	cout << "Dog()" << endl;
}

Dog::Dog(string name, int age, int num_limbs)
{
	Report();
}
```

>如果不给参数运行会执行animal和dog的空的构造函数，若给了参数则会执行有参数的dog构造函数与animal的空的构造函数
如果希望执行animal的有参数的构造函数：`Dog ::Dog(string name, int age, int num_limbs) : Animal(name,age,num_limbs){	}`

## 虚继承
```cpp
//间接基类A
class A{
protected:
    int m_a;
};
//直接基类B
class B: public A{
protected:
    int m_b;
};
//直接基类C
class C: public A{
protected:
    int m_c;
};
//派生类D
class D: public B, public C{
public:
    void seta(int a){ m_a = a; }  //命名冲突
    void setb(int b){ m_b = b; }  //正确
    void setc(int c){ m_c = c; }  //正确
    void setd(int d){ m_d = d; }  //正确
private:
    int m_d;
};
int main(){
    D d;
    return 0;
}
```
这段代码实现了上图所示的菱形继承，第 25 行代码试图直接访问成员变量 m_a，结果发生了错误，因为类 B 和类 C 中都有成员变量 m_a（从 A 类继承而来），编译器不知道选用哪一个，所以产生了歧义。
为了解决多继承时的命名冲突和冗余数据问题，C++ 提出了虚继承，使得在派生类中只保留一份间接基类的成员。
在继承方式前面加上 virtual 关键字就是虚继承
```cpp
#include <iostream>
 
using namespace std;
 
// 基类
class Shape 
{
   public:
      void setWidth(int w)
      {
         width = w;
      }
      void setHeight(int h)
      {
         height = h;
      }
   protected:
      int width;
      int height;
};
 
// 派生类
class Rectangle: public Shape
{
   public:
      int getArea()
      { 
         return (width * height); 
      }
};
 
int main(void)
{
   Rectangle Rect;
 
   Rect.setWidth(5);
   Rect.setHeight(7);
 
   // 输出对象的面积
   cout << "Total area: " << Rect.getArea() << endl;
 
   return 0;
}
```
# 栈与堆	stack heap
栈由操作系统自动分配释放 ，用于存放函数的参数值、局部变量等，其操作方式类似于数据结构中的栈。其中函数中定义的局部变量按照先后定义的顺序依次压入栈中，也就是说相邻变量的地址之间不会存在其它变量。

堆由开发人员分配和释放， 若开发人员不释放，程序结束时由 OS 回收，分配方式类似于链表。
# 链表
链表的基本思维是，利用结构体的设置，额外开辟出一份内存空间去作指针，它总是指向下一个结点，一个个结点通过NEXT指针相互串联，就形成了链表。
![v2-e8d97bb6244ba0fe35ad29d2a34252f8_720w.png](https://cdn.nlark.com/yuque/0/2023/png/35323970/1677919464346-71ec526a-5032-45ed-9e46-9e78bfb179a7.png#averageHue=%23ddc29b&clientId=uf88ec207-abaf-4&from=drop&id=uf1458ba7&originHeight=31&originWidth=751&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=2746&status=done&style=none&taskId=u1734dab3-4039-45a2-96c0-82d48628864&title=)
## 类型
包含单链表，双链表，循环单链表![v2-fed2e1976f4fa3236e123b6313dbf467_720w.webp](https://cdn.nlark.com/yuque/0/2023/webp/35323970/1677919561598-1dfd1d87-741b-4b22-a62b-dfdd98ac5152.webp#averageHue=%23f8f8f8&clientId=uf88ec207-abaf-4&from=drop&id=u1ec50ae4&originHeight=369&originWidth=696&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=9112&status=done&style=none&taskId=u7d74b440-5d96-4ead-b92e-f66c3f33477&title=)
详细看另一个文档[[链表]]
# 析构函数
析构函数(destructor) 与构造函数相反，当对象结束其[生命周期](https://baike.baidu.com/item/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F?fromModule=lemma_inlink)，如对象所在的函数已调用完毕时，系统自动执行析构函数。析构函数往往用来做“清理善后” 的工作（例如在建立对象时用new开辟了一片内存空间，delete会自动调用析构函数后释放内存）。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35323970/1678003100366-d908b7f1-a77f-4904-9e8c-023798c1d693.png#averageHue=%23a8b0b1&clientId=ue6ffd2ca-f0ae-4&from=paste&height=232&id=udca76683&originHeight=1080&originWidth=1920&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=1630008&status=done&style=none&taskId=ub2d3525d-475c-4acd-aa37-ccba43b9a1d&title=&width=412)
```cpp
struct A()
{
	A(){

	}
	~A(){

	}
}
```
### 特点：
1. **没有返回类型**：析构函数没有返回类型，即使是 `void` 也不行。
2. **没有参数**：析构函数不能接受任何参数。
3. **不能被重载**：一个类只能有一个析构函数。
4. **自动调用**：当对象超出作用域、被删除（使用 `delete` 关键字）或者程序结束时，析构函数会被自动调用。
### 作用：
析构函数通常用于以下任务：
- **释放资源**：如果对象分配了动态内存（例如使用 `new` 分配的内存），析构函数应该释放这些内存（通常使用 `delete`）。
- **关闭文件句柄**：如果对象打开了文件，析构函数应该确保这些文件被正确关闭。
- **断开连接**：如果对象建立了与其他系统的连接（如网络连接、数据库连接等），析构函数应该断开这些连接。
- **清理其他资源**：例如，如果对象使用了操作系统资源，如线程、互斥锁等，析构函数应该确保这些资源被正确释放。
# 静态变量 static
static修饰==局部变量==时，改变了变量的生命周期，让静态局部变量出了作用域依然存在，到程序结束，生命周期才结束，但是作用域不变，仍为局部作用域，当定义它的函数或者语句块结束的时候，作用域随之结束。
static修饰==全局变量==时，这个全局变量只能在本源文件内使用，不能在其他源文件内使用。
static修饰==函数==，该函数只在本文件内被调用或访问，不能跨文件访问，其他文件中可以定义相同名字的函数，不会发生冲突。

==在函数体内==，静态变量具有"记忆"功能。即在函数被调用过程中，一个被声明为静态变量的值维持不变。
static局部变量和普通局部变量的区别：**static局部变量只被初始化一次**，下一次的运算依据上一次的结果值。

与类有关的static：
 static数据成员特点：
（1）对于非静态数据成员，每个类对象都有自己的复制品；而静态数据成员被当做类的成员。无论有多少个对象，静态数据成员只有一个复制品，由类的所有对象共享访问。
（2）static数据成员存储在全局数据区。定义时要分配内存空间，所以不能在类声明中定义。由于类的所有对象共享，不属于特定的类对象，所以，没有产生类对象时，其作用于可见。
（3）和普通数据成员一样，遵从public，private,protected访问规则。
（4）必须初始化；static成员变量的初始化是在类外。初始化时不带上static的关键字。private、protected的static成员虽然可以在类外初始化，但是不能在类外被访问。

static成员函数特点：
静态成员函数是类的内部实现，属于类定义的一部分。普通成员函数一般隐含this指针，指向类的对象本身，因为普通成员函数总是具体的属于某个类的具体对象，所以this是默认的。而静态成员函数由于不是与任何对象相联系，因此不具有this指针。从这个意义讲，它无法访问属于类对象的非静态数据成员，也无法访问非静态成员函数，只能调用静态数据成员和静态成员函数。
# 虚函数 virfual
虚函数用于函数的重写，子类从父类继承的函数若是虚函数则可以进行重写。==重写与重载不同==

**虚函数** 是在基类中使用关键字 **virtual** 声明的函数。在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数。
我们想要的是在程序中任意点可以根据所调用的对象类型来选择调用的函数，这种操作被称为==动态链接==，或==后期绑定==。
父类的虚函数或纯虚函数在子类中依然是虚函数。有时我们并不希望父类的某个函数在子类中被重写，在 C++11 及以后可以用关键字** final** 来避免该函数再次被重写。
```cpp
class Father
{
public:
	virtual void Greet() {
		cout << "Hello" << endl;
	}
};

class Child :public Father
{
public:
	void Greet() {		//子类中不必强调其是虚函数，派生类中依旧可以重写
		cout << "What's up?" << endl;
	}
};

class GrandChild :public	Child
{
public:
	virtual void Greet() override		//虽然不必加virtual，但一般都会加上来表明这是虚函数。override是用来表示这是虚函数的重写。
	{
		cout << "Goo~goo ga ga!" << endl;
	}
};
```
## 纯虚数
虚函数可以不定义的。不定义的虚函数是纯虚函数。
在一个类中如果存在未定义的虚函数，那么不能直接使用该类的实例，可以理解因为未定义 virtual 函数，其类是抽象的，无法实例化。
`virtual int area() = 0;`
# 多态
## 要点

1.  多态是方法的多态，不是属性的多态(多态与属性无关)。 
2.  多态的存在要有3个必要条件：继承，方法重写，父类引用指向子类对象。 
3.  父类引用指向子类对象后，用该父类引用调用子类重写的方法，此时多态就出现了。 

父类引用做方法的形参，实参可以是任意的子类对象，可以通过不同的子类对象实现不同的行为方式。
由此，我们可以看出多态的主要优势是提高了代码的可扩展性，符合开闭原则。**但是多态也有弊端，就是无法调用子类特有的功能**
```cpp
#include <iostream>
#include <string.h>
using namespace std;

class Object
{
public:
	virtual void BeginPlay() {
		cout << "Object" << endl;
	}
};

class Acter :public Object
{
public:
	void BeginPlay() {
		cout << "Acter" << endl;
	}
};

class Pawn :public	Acter
{
public:
	virtual void BeginPlay() override
	{
		cout << "Pawn" << endl;
	}
};

int main() {
	Object* ptr_to_Object = new Object;
	Acter* ptr_to_Acter = new Acter;
	Pawn* ptr_to_Pawn = new Pawn;

	Object* ptrObjectArray[]{ ptr_to_Object ,ptr_to_Acter ,ptr_to_Pawn };

	for (int i=0;i<3;i++)
	{
		ptrObjectArray[i]->BeginPlay();
	}
	system("pause");
}
```
# 转换

- ` const_cast<type> (expr):` const_cast 运算符用于修改类型的 const / volatile 属性。除了 const 或 volatile 属性之外，目标类型必须与源类型相同。这种类型的转换主要是用来操作所传对象的 const 属性，可以加上 const 属性，也可以去掉 const 属性。
- `dynamic_cast<type>` (expr): dynamic_cast 在运行时执行转换，验证转换的有效性。如果转换未执行，则转换失败，表达式 expr 被判定为 null。dynamic_cast 执行动态转换时，type 必须是类的指针、类的引用或者 void*，如果 type 是类指针类型，那么 expr 也必须是一个指针，如果 type 是一个引用，那么 expr 也必须是一个引用。==用于多态类型的继承链之中==
- `einterpret_cast<type> (expr)`: reinterpret_cast 运算符把某种指针改为其他类型的指针。它可以把一个指针转换为一个整数，也可以把一个整数转换为一个指针。==将指针转换为任意形式的指针，不会检测可行性，几乎不用==
- `static_cast<type> (expr)`: static_cast 运算符执行非动态转换，没有运行时类检查来保证转换的安全性。例如，它可以用来把一个基类指针转换为派生类指针。==用于非多态类型的继承链之中==

# argc 和 argv
1. **argc：是argument count 的缩写，保存运行时传递给main函数的参数个数。**
2. **argv：是argument vector 的缩写，保存运行时传递main函数的参数，类型是一个字符指针数组，每个元素是一个字符指针，指向一个命令行参数。**

argv\[0]指向程序运行时的全路径名；

argv\[1] 指向程序在命令行中执行程序名后的第一个字符串；

argv\[2] 指向程序在命令行中执行程序名后的第二个字符串；

以此类推直到argv\[argc]......

argv\[argc] 在C++中指向nullptr，在C语言中指向NULL。