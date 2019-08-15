---
layout: post
#标题配置
title:  C++
#时间配置
date:   2019-06-20
#大类配置
categories:Interview
#小类配置
tag: C++
---

* content
{:toc}

# C++

### 001 判断struct的字节数

一般使用sizeof判断struct所占的字节数，那么计算规则是什么呢？

关键词：

1. 变量的起始地址和变量自身的字节数
2. 以最大变量字节数进行字节对齐（倍数关系）。

注：这里介绍的原则都是在没有#pragma pack宏的情况下

先举个例子：

```cpp
struct A
{
    char a[5];
    int b;
    short int c;
}struct A;
```

在上例中，要计算 sizeof(A) 是多少？

有两个原则：

1）各成员变量存放的起始地址相对于结构的起始地址的<u>偏移量必须为该变量的类型所占用的字节数的倍数，</u>即当 A中的a占用了5个字节后，b需要占用四个字节，此时如果b直接放在a后，则b的起始地址是5（不是6，即起始地址从0开始算），不是sizeof(int)的整数倍，所以需要在a后面补充3个空字节，使得b的起始地址为8. 当放完b后，总空间为5+3+4 = 12. 接着放c，此时为 12 + 2 = 14.

2）为了确保<u>结构的大小为结构的字节边界数（即该结构中占用最大空间的类型所占用的字节数）的倍数</u>，所以在为最后一个成员变量申请空间后，还会根据需要自动填充空缺的字节。这是说A中占用最大空间的类型，就是int型了，占用了4个字节，那么规定A占用的空间必须是4的整数倍。本来计算出来占用的空间为14，不是4的整数倍，因此需要在最后补充2个字节。最终导致A占用的空间为16个字节。

再举个例子：

```cpp
struct B
{
    char *d;
    short int e;
    long long f;
    char c[1];
}b;
 
void test2() {
	printf("%d\n", sizeof(b));
}

```

对于此题，需要注意的一点是：<u>windows系统对long long是按照8字节进行对齐的，但是Linux系统对long long则是按照4字节对齐的</u>（此为对齐规则，而不是占用字节数）。

因此:

- d占用4字节（因为d是指针）
- e占用2字节
- f占用8字节，但是其起始地址为为6，不是4的整数倍（对于Linux系统），或不是8的整数倍（对于Windows系统），因此对e之后进行字节补齐，在这里不管对于Linux还是Windows都是补充2个字节，因此 f 的起始地址是8，占用8个字节。
- 对于c，它占用了1个字节，起始地址是16，也是1的整数倍。 
- 最后，在c之后需要对整个b结构体占用的空间进行补齐，目前占用空间是16+1 = 17个字节。

对于Linux，按4字节补齐（long long 是按4字节补齐的），因此补充了3位空字节，最后占用空间是 17 + 3 = 20字节。

对于Windows系统，是按8字节补齐的，因此就补充了7个字节，最后占用的空间是24字节。

**参考资料**

- [【C++】计算struct结构体占用的长度](https://blog.csdn.net/nisxiya/article/details/22456283?utm_source=copy )

### 002 auto关键字的作用

- 声明变量时根据初始化表达式自动推断该变量的类型
- 声明函数时函数返回值的占位符

### 002 static作用（修饰变量和函数）

**什么是static？**

static 是C++中很常用的修饰符，它被用来控制变量的存储方式和可见性。

**为什么要引入static？**

函数内部定义的变量，在程序执行到它的定义处时，编译器为它在栈上分配空间，大家知道，函数在栈上分配的空间在此函数执行结束时会释放掉，这样就产生了一个问题: 如果想将函数中此变量的值保存至下一次调用时，如何实现？ 最容易想到的方法是定义一个全局的变量，但定义为一个全局变量有许多缺点，最明显的缺点是破坏了此变量的访问范围（使得在此函数中定义的变量，不仅仅受此函数控制）。

**static的作用**

<u>第一个作用是限定作用域（隐藏）；第二个作用是保持变量内容持久化</u>；

- 函数体内static变量的作用范围为该函数体，不同于auto变量，该变量的内存只被分配一次，因此其值在下次调用时仍维持上次的值。

举个例子

```
#include<iostream>

using namespace std;

int main(){

    for(int i=0; i<10; ++i){
	    int a = 0;
		static int b = 0;
		cout<<"a: "<< a++ <<endl;
		cout<<"b(static): " << b++ <<endl;
	}
	return 0;
}
```

输出结果：

```
a: 0
b(static): 0
a: 0
b(static): 1
a: 0
b(static): 2
a: 0
b(static): 3
a: 0
b(static): 4
a: 0
b(static): 5
a: 0
b(static): 6
a: 0
b(static): 7
a: 0
b(static): 8
a: 0
b(static): 9
```

- 在**模块内的static全局变量**可以被模块内所有函数访问，但不能被模块外其他函数访问
- 在**模型内的static函数**只可被这一模块内的其他函数调用，这个函数的使用范围被限制在声明它的模块内。
- 在**类中的static成员变量**属于整个类所有，对类的所有对象只有一份复制。即类的所有对象访问的static成员变量是同一个，而不是对象专属的。
- 在**类中的static成员函数**属于整个类所有，这个函数不接收this指针，因而只能访问类的static成员变量。同类的static成员变量性质一样，类的对象访问的static成员函数是同一个，不是对象专属的。
- 函数中的静态变量,在函数退出后不被释放
- 作用域为当前文件，从定义/声明位置到文件结尾
- 动态全局变量可以通过extern关键字在外部文件中使用，但静态全局变量不可以在外部文件中使用，静态全局变量相当于限制了动态全局变量的作用域

**参考资料**

- [C++中static关键字作用总结](https://www.cnblogs.com/songdanzju/p/7422380.html)
- [C/C++中STATIC用法总结](https://www.cnblogs.com/jhmu0613/p/7131997.html)

### 003 const作用（修饰变量和函数）

const含义：只要一个变量前用const来修饰，就意味着该变量里的数据只能被访问，而不能被修改，也就是意味着const“只读”（readonly）。

规则：

1. const离谁近，谁就不能被修改；
2. const修饰一个变量时，一定要给这个变量初始化，若不初始化，在后面也不能初始化。

const作用：

1. 修饰变量

* 具有常属性，可以在定义数组的时候用该变量定义，<u>每次取值从寄存器中取</u>，在编译过后，直接将对应的值，替换到当前变量的位置。与之相对的是volatile。被这个关键字修饰的话，代表告诉了编译器，这个变量是随时可能被修改的。防止编译器优化，<u>每次读取该值时从内存中读取</u>。而不是从编译器优化的寄存器中读取。C++中，被const修饰的变量，会在编译期间将对应的变量，直接替换为该变量的值。但是C语言中不会。

2. 修饰指针

* int * const p ；表示<u>指针变量本身不能被修改，只能指向这一个地址</u>，这与引用比较类似，一个变量的引用，在生命周期内只能引用一个对象。但是这个<u>指针所指向的内容是可以改变的</u>。
* const int *p这表示<u>p所指向的空间内容不可修改</u>。C语言中int *p = 1; 可以正常运行。因为发生了隐式类型转换会出现警告。但是C++中有严格的类型检测，不允许这样进行赋值。必须显式的给出强制转换。强行转换之后对对应地址内容进行修改，C中输出 *p与m值相同，C++中 *p是修改后的值，m则是定义时的值，因为C++中被const修饰的变量，会在编译时期进行替换。

3. 修饰函数

* 放在返回值前 const int add(int a, int b)无意义，因为返回的值本就是一个临时变量。所以const修饰也没有任何作用
* 放在参数列表前修饰。用来保护传进来的参数，保证尽可能的不被修改。
* 放在函数后面。用来修饰类的成员函数中的this所指向的，也就是保护类成员（非静态成员除外）不被修改。同时，没有const修饰的函数可以调用const修饰的成员函数，但是被const修饰的成员函数不可以调用非const修饰的成员函数。而且非const定义出来的对象可以调用const与非const函数，但是const定义出来的对象只能调用const修饰的函数，不能调用非const函数，因为他有可能对对象进行修改。
* 类成员函数中，后面加与不加const也可以形成重载。const修饰的对象调用的是const修饰函数，非const修饰的对象调用的是非const函数。

4. 修饰类成员变量

* <u>const成员变量必须在类的构造函数的初始化列表中初始化</u>，因为此时类并没有进行实例化（创建对象），因此也没有分配内存。因为类中变量只是声明时候用的cosnt来修饰，const要修饰一个变量不能被改变，总不能这个变量都没有值，就让他不能被改变吧。所以要在这个变量被创建之前就给他定义一个值。类的构造函数的初始化列表中就可以在对象创建之前做到这一点。如果不在构造函数初始化列中给出值，有可能在构造函数体中，这个const成员已经指向了一个随机的初始值。这样就不切合实际了。
* 如果是static const 同时修饰的类成员变量。可以<u>在类的内部声明时给出初始化，同时在类外（全局作用域）进行声明</u>，否则不会为这个变量分配空间。

1. 可以用来定义常量，修饰函数参数，修饰函数返回值，且被const修饰的东西，都受到强制保护，可以预防其它代码无意识的进行修改，从而提高了程序的健壮性（是指系统对于规范要求以外的输入能够判断这个输入不符合规范要求，并能有合理的处理方式。ps：即所谓高手写的程序不容易死）；
2. 使编译器保护那些不希望被修改的参数，防止无意代码的修改，减少bug；
3. 给读代码的人传递有用的信息，声明一个参数，是为了告诉用户这个参数的应用目的；

const优点：

1. 编译器可以对const进行类型安全检查（所谓的类型安全检查，能将程序集间彼此隔离开来，这种隔离能确保程序集彼此间不会产生负面影响，提高程序的可读性）；
2. 有些集成化的调试工具可以对const常量进行调试，使编译器对处理内容有了更多的了解，消除了一些隐患。

```
    eg：void hanshu（const  int i）{.......}   编译器就会知道i是一个不允许被修改的常量
```

3. 可以节省空间，避免不必要的内存分配，因为编译器通常不为const常量分配内存空间，而是将它保存在符号表中，这样就没有了存储于读内存的操作，使效率也得以提高；
4. 可以很方便的进行参数的修改和调整，同时避免意义模糊的数字出现；

### 004 多态

参考：https://blog.csdn.net/skysongkran/article/details/82012698

### 005 虚函数

- 父类的析构函数是非虚的，但是子类的析构函数是虚的，delete子类对象指针会调用父类的析构函数

参考：https://www.runoob.com/cplusplus/cpp-inheritance.html（菜鸟教程）

https://blog.csdn.net/lyztyycode/article/details/81326699

### 006 继承

参考：https://blog.csdn.net/studyhardi/article/details/90744785

### 007 多线程的同步问题

参考：https://blog.csdn.net/s_lisheng/article/details/74278765

https://www.cnblogs.com/raichen/p/5766634.html

### 008 C++的设计模式

- 工厂模式
- 策略模式
- 适配器模式
- 单例模式
- 原型模式
- 模板模式
- 建造者模式
- 外观模型
- 组合模式
- 代理模式
- 享元模式
- 桥接模式
- 装饰模式
- 备忘录模式
- 中介者模式
- 职责链模式
- 观察者模式

**参考资料**

- [C++ 常用设计模式（学习笔记）](https://www.cnblogs.com/chengjundu/p/8473564.html)
- [设计模式（C++实例）](https://blog.csdn.net/phiall/article/details/52199659)


### 009 动态内存管理

* C动态内存管理（malloc、calloc、realloc/free）

* C++动态内存管理（new/delete、new[]/delete[]）

**参考资料**

- https://blog.csdn.net/z_x_m_m_q/article/details/82503092
- https://blog.csdn.net/xinwenhuayu/article/details/86212171
- https://www.cnblogs.com/tp-16b/p/8684298.html

### 010 new/delete和malloc/free的区别

- malloc/free是标准库函数，new/delete是运算符
- new初始化对象，调用对象的构造函数，malloc仅仅分配内存
- new返回的是所分配变量（对象）的指针，malloc返回的是void指针
- new/delete，malloc/free均能在C++使用

### 011 long long转成string

```
#include <strstream>
#include <sstream>
#include <string>
string IntToString(int n)
{
    std::string result;
    std::strstream ss;
    ss <<  n;
    ss >> result;
    return result;
}
 
string lltoString(long long t)
{
    std::string result;
    std::strstream ss;
    ss <<  t;
    ss >> result;
    return result;
}
std::wstring IntToWstring(unsigned int i)
{
	std::wstringstream ss;
	ss << i;
	return ss.str();
}
```

### 012 NULL和nullptr的区别

* C语言中，NULL实际上是一个void *的指针

  ```
  #define NULL ((void *)0)
  ```

* C++中，NULL就是0

  ```
  #ifdef __cplusplus ---简称：cpp c++ 文件
  #define NULL 0
  #else
  #define NULL ((void *)0)
  #endif
  ```

* 如果使用 nullptr 初始化对象，就能避免 0 指针的二义性的问题
* null没有分配空间，""分配了空间

### 015 C++基类的析构函数为什么建议是虚函数？

* 在实现多态时，当用基类操作派生类，在析构时防止只析构基类而不析构派生类的状况发生
* 基类对派生类及其对象的操作，只能影响到那些从基类继承下来的成员。如果想要用基类对非继承成员进行操作，则要把基类的这个函数定义为虚函数。

### 016 STL中的vector和list的区别

**参考资料**

- https://blog.csdn.net/zhongguoren666/article/details/7403922/ 

### 017 多线程和多进程的区别

进程：系统进行资源分配和调度的基本单位。

线程：基本的CPU执行单元，也是程序执行过程中的最小单元，由线程ID、程序计数器、寄存器集合和堆栈共同组成。（有种说法是线程是进程的实体）

线程和进程的关系：

（1）一个线程只能属于一个进程，而一个进程可以有多个线程，但至少有一个线程
（2）资源分配给进程，同一个进程的所有线程共享该进程的所有资源
（3）CPU分给进程，即真正在CPU上运行的是线程

**参考资料**

- [多线程与多进程](https://www.cnblogs.com/yuanchenqi/articles/6755717.html)

### 018 实现atoi，即将"1234"转化成1234（int类型）

```
int my_atoi(char* pstr)
{
	int Ret_Integer = 0;
	int Integer_sign = 1;
	
	/*
	* 判断指针是否为空
	*/
	if(pstr == NULL)
	{
		printf("Pointer is NULL\n");
		return 0;
	}
	
	/*
	* 跳过前面的空格字符
	*/
	while(isspace(*pstr) == 0)
	{
		pstr++;
	}
	
	/*
	* 判断正负号
	* 如果是正号，指针指向下一个字符
	* 如果是符号，把符号标记为Integer_sign置-1，然后再把指针指向下一个字符
	*/
	if(*pstr == '-')
	{
		Integer_sign = -1;
	}
	if(*pstr == '-' || *pstr == '+')
	{
		pstr++;
	}
	
	/*
	* 把数字字符串逐个转换成整数，并把最后转换好的整数赋给Ret_Integer
	*/
	while(*pstr >= '0' && *pstr <= '9')
	{
		Ret_Integer = Ret_Integer * 10 + *pstr - '0';
		pstr++;
	}
	Ret_Integer = Integer_sign * Ret_Integer;
	
	return Ret_Integer;
}
```

### 019 实现atof，即将"1.234"转换成1.234（float类型）

```
#include<iostream>
 
using namespace std;
 
double atof_my(const char *str)
{
	double s=0.0;
 
	double d=10.0;
	int jishu=0;
 
	bool falg=false;
 
	while(*str==' ')
	{
		str++;
	}
 
	if(*str=='-')//记录数字正负
	{
		falg=true;
		str++;
	}
 
	if(!(*str>='0'&&*str<='9'))//如果一开始非数字则退出，返回0.0
		return s;
 
	while(*str>='0'&&*str<='9'&&*str!='.')//计算小数点前整数部分
	{
		s=s*10.0+*str-'0';
		str++;
	}
 
	if(*str=='.')//以后为小数部分
		str++;
 
	while(*str>='0'&&*str<='9')//计算小数部分
	{
		s=s+(*str-'0')/d;
		d*=10.0;
		str++;
	}
 
	if(*str=='e'||*str=='E')//考虑科学计数法
	{
		str++;
		if(*str=='+')
		{
			str++;
			while(*str>='0'&&*str<='9')//将e或者E后面的str转成int
			{
				jishu=jishu*10+*str-'0';
				str++;
			}
			while(jishu>0)
			{
				s*=10;
				jishu--;
			}
		}
		if(*str=='-')
		{
			str++;
			while(*str>='0'&&*str<='9')
			{
				jishu=jishu*10+*str-'0';
				str++;
			}
			while(jishu>0)
			{
				s/=10;
				jishu--;
			}
		}
	}
 
    return s*(falg?-1.0:1.0);
}
```

### 020 结构体和联合体的区别

结构体：把不同类型的数据组合成一个整体，自定义数据类型，结构体变量所占内存长度是<u>各成员占的内存长度的总和</u>。

联合体：使几个不同类型的变量共占一段内存(相互覆盖)，共同体变量所占内存长度是各<u>最长的成员占的内存长度</u>。

**struct 与 union主要有以下区别：**

1.struct和union都是由多个不同的数据类型成员组成, 但在任何同一时刻, union中只存放了一个被选中的成员, 而struct的所有成员都存在。在struct中，各成员都占有自己的内存空间，它们是同时存在的。一个struct变量的总长度等于所有成员长度之和。在Union中，所有成员不能同时占用它的内存空间，它们不能同时存在。Union变量的长度等于最长的成员的长度。

2.对于union的不同成员赋值, 将会对其它成员重写, 原来成员的值就不存在了, 而对于struct的不同成员赋值是互不影响的。

3.联合体的各个成员共用内存，并应该同时只能有一个成员得到这块内存的使用权（即对内存的读写），而结构体各个成员各自拥有内存，各自使用互不干涉。所以，某种意义上来说，联合体比结构体节约内存。

举个例子：

```cpp
typedef struct
{
int i;
int j;
}A;
typedef union
{
int i;
double j;
}U;
```

sizeof(A)的值是8，sizeof(U)的值也是8（不是12）。

为什么sizeof(U)不是12呢？因为union中各成员共用内存，i和j的内存是同一块。而且整体内存大小以最大内存的成员的划分。即U的内存大小是double的大小，为8了。

sizeof(A)大小为8，因为struct中i和j各自得到了一块内存，每人4个字节，加起来就是8了。
了解了联合体共用内存的概念，也就是明白了为何每次只能对其一个成员赋值了，因为如果对另一个赋值，会覆盖了上一个成员的值。

```
举个例子：
例如：书包；可以放置书本、笔盒、记事本等物。
联合体，仅能放入一样东西的包(限制)，其尺寸，是可放物品中，最大一件的体积。
结构体，是能放入所有物品的包，所以其尺寸，可同时容纳多样物品。
联合体，同时间只能有一个成员在内。或是说，可以用不同型态，去看同一组数据。
结构体，可以包含多个成员在一起，成员都能个别操作。
```

### 021 引用和指针

* 引用不可以为空，但指针可以为空，故定义一个引用的时候，必须初始化

* 声明指针是可以不指向任何对象，因此，使用指针之前必须做判空操作

* 引用不可以改变指向，但是指针可以改变指向，而指向其它对象。说明：虽然引用不可以改变指向，但是可以改变初始化对象的内容。例如就++操作而言，对引用的操作直接反应到所指向的对象，而不是改变指向；而对指针的操作，会使指针指向下一个对象，而不是改变所指对象的内容。

* 引用的大小是所指向的变量的大小，因为引用只是一个别名而已；指针是指针本身的大小，4个字节

**参考资料**

- https://blog.csdn.net/qq_39539470/article/details/81273179

### 022 C++ operator new 和 new operator

* new operator做两件事，分配内存+调用构造函数初始化
* operator new所了解的是内存分配，它对构造函数一无所知

**参考资料**

- https://blog.csdn.net/qq_26079093/article/details/94211205

### 023 怎么理解C++面向对象？跟Python面向对象有什么区别？

**参考资料**

- https://blog.csdn.net/lanchunhui/article/details/52381391

### 024 使用char* p = new char[100]申请一段内存，然后使用delete p释放，有什么问题？

- 不会有内存泄露，但不建议用
- 基本类型的对象没有析构函数，因此回收基本类型的数组空间用delete和delete[]都可以，但为了规范，不建议用delete

### 025 虚函数有哪些作用？多态的虚函数（父类和子类）返回值类型可以不一样吗？什么情况下，返回值类型不一样？

* 纯虚函数则必须完全一致，虚函数的返回值类型可以不一样。

### 026 C++四种强制类型转换有哪些？每种特性是什么？有什么区别？

**参考资料**

- https://www.cnblogs.com/cauchy007/p/4968707.html

### 027 STL中的sort函数实现

* 数据量大时采用Quick Sort，分段递归排序。一旦分段后的数据量小于某个阈值，为避免Quick Sort的递归调用带来过大的额外开销，就改用Insertion Sort（插入排序）。如果递归层次过深，还会改用Heap Sort。

**参考资料**

- https://blog.csdn.net/Hanani_Jia/article/details/82498469

### 028 std::vector描述一下，如何动态扩展，如何shink内存

* 扩容：reverse
* 缩容：shrink_to_fit

**参考资料**

- http://www.cplusplus.com/reference/vector/vector/reserve/

### 029 unorder容器与ordered容器的区别

- [ ] TODO

### 030 说一下智能指针，shared_ptr与unique_ptr

- [ ] TODO

### 031 普通指针如何实现一块内存只能有一个指针指向这种功能

- [ ] TODO

### 032 C++ RTTI 是什么东西？

- [ ] TODO

### 034 vector的iterator什么时候失效？

### 035 try、catch、finall

```
public static int func (){
    try{
        return 1;
    }catch (Exception e){
        return 2;
    }finally{
        return 3;
    }
}
```

- try中没有异常时，但是有return等跳转语句，这样会引发程序控制流离开当前的try,自动完成finally中资源的释放
- try中有异常时，catch在获取到异常之前，进行finally执行，接着执行catch中的语句

**参考资料**

- [1世纪入门C++](https://zhuanlan.zhihu.com/p/57016086)
- [SLAM、定位、建图求职分享](https://zhuanlan.zhihu.com/p/68858564)

# 数据结构与算法

### 001 动态规划的三条性质

- 最优子结构、子问题重叠性、无后效性

### 002 分支限界法思想

- 以广度优先或以最小耗费（最大效益）优先的方式搜索问题的解空间树
- 分支限界法中，每一个活结点只有一次机会成为扩展结点，活结点一旦成为扩展结点，就一次性产生其所有儿子结点，其中导致不可行解或导致非最优解的儿子结点被舍弃，其余儿子结点被加入活结点表中
- 然后从活结点表中取下一结点成为当前扩展结点
- 重复上述结点扩展过程，直至到找到所需的解或活结点表为空时为止

### 003 中缀表达式转后缀表达式

中缀表达式“9+(3-1)*3+10/2”转化为后缀表达式“9 3 1-3*+ 10 2/+”

- 规则：从左到右遍历中缀表达式的每个数字和符号，若是数字就输出，即成为后缀表达式的一部分；若是符号，则判断其与栈顶符号的优先级，是右括号或优先级低于找顶符号（乘除优先加减）则栈顶元素依次出找并输出，并将当前符号进栈，一直到最终输出后缀表达式为止。

### 004 完全二叉树的性质

### 005 设G是有n个结点，m条边的连通图，必须删去G的( )条边，才能确定G的一棵生成树．

- 树的边数 = 点数 - 1 = n - 1，所以要删掉m - （n - 1）= m - n + 1 条边。

### 001 排序介绍

**排序的定义**

假设含有n个记录的序列为{r1,r2,...,rn}，其相应的关键字分别是{k1,k2,...,kn}，需要确定1,2,...,n的一种排列p1,p2,...,pn，使其相应的关键字满足kp1<=kp2<=...<=kpn 非递减（或非递增）的关系，即使得序列成为一个按关键字有序的序列{rp1,rp2,...,rpn}，这样的操作就称为排序[1]。

简单来说，排序就是使输入的序列变成符合一定规则（关键字）的有序序列（非递减或非递增）。大多数遇到的排序问题都是按数据元素值的大小规则进行排序的问题。所以本文为了方便起见，只讨论数据元素值大小比较的排序问题。

**排序的稳定性**

假设ki=kj（1<=i《=n，1<=j<=n，i!=j），且在排序前的序列中ri领先于rj（即i<j）。

- 如果排序后ri仍领先于rj，则称所用的排序方法是稳定的；
- 反之，若可能使得排序后的序列中rj领先于ri，则称所用的排序方法是不稳定的。

简单来概括稳定和不稳定[2]：

- **稳定**：如果a原本在b前面，而a=b，排序之后a仍然在b前面；
- **不稳定**：如果a原本在b前面，而a=b，排序之后a可能在b的后面。

**时间和空间复杂度**

- **时间复杂度**：对排序数据的总的操作次数。反应当n变化时，操作次数呈现什么规律
- **空间复杂度**：算法在计算机内执行时所需要的存储空间的容量，它也是数据规模n的函数。

### 002 排序算法

排序算法可以分成两大类：

**非线性时间比较类排序**：通过比较来决定元素间的相对次序，由于其时间复杂度不能突破O(nlogn)，因此称为非线性时间比较类排序。

**线性时间非比较类排序**：不通过比较来决定元素间的相对次序，它可以突破基于比较排序的时间下界，以线性时间运行，因此称为线性时间非比较类排序。

- [`冒泡排序（Bubble Sort`](https://github.com/amusi/coding-note/tree/master/Data%20Structures%20and%20Algorithms/sort#bubblesort)
- [`选择排序（Selection Sort`](https://github.com/amusi/coding-note/tree/master/Data%20Structures%20and%20Algorithms/sort#selectionsort)
- [`插入排序（Insertion Sort`](https://github.com/amusi/coding-note/tree/master/Data%20Structures%20and%20Algorithms/sort#insertionsort)
- [`希尔排序（Shell Sort`](https://github.com/amusi/coding-note/tree/master/Data%20Structures%20and%20Algorithms/sort#shellsort)
- [`堆排序（heap Sort`](https://github.com/amusi/coding-note/tree/master/Data%20Structures%20and%20Algorithms/sort#heapsort)
- [`归并排序（merge Sort`](https://github.com/amusi/coding-note/tree/master/Data%20Structures%20and%20Algorithms/sort#mergesort)
- [`快速排序（Fast Sort`](https://github.com/amusi/coding-note/tree/master/Data%20Structures%20and%20Algorithms/sort#fastsort)

### 003 常见排序算法复杂度

[![常用排序算法1](https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/sort_algorithms_1.png)](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/images/sort_algorithms_1.png)

[![常用排序算法2](https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/sort_algorithms_2.png)](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/images/sort_algorithms_2.png)

- [8大排序算法稳定性分析](https://www.cnblogs.com/codingmylife/archive/2012/10/21/2732980.html)

### 004 冒泡排序（Bubble Sort）

**基本思想**

比较相邻的两个元素，将值大的元素交换到右边（降序则相反）

**步骤：**

1. 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
3. 针对所有的元素重复以上的步骤，除了最后一个；
4. 重复步骤1~3，直到排序完成。

比如有n个元素，那么第一次比较迭代，需要比较n-1次（因为是相邻成对比较，最后一个元素没有与下一个相邻比较的对象，所以是n-1次），此次迭代完成后确定了最后一个元素为最大值；第二次比较迭代，需要比较n-2次，因为第一次迭代已经确定好了最后一个元素，所以不再需要比较；...；第 i次比较迭代，需要比较n-i次，此时确定后面i个元素是有序的较大元素；...；第n-1次比较迭代，需要比较n-(n-1)次，此时完成冒泡排序操作。

时间复杂度：o(n^2) = (n-1)*(n-1)

**动图演示：**

![bubble_sort](https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/bubble_sort_1.jpg)

![bubble_sort](https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/bubble_sort.gif)

**过程演示：**待排序数组：{5, 4, 7, 1, 6, 2}，升序排序

\---------------------------------------

第一次循环：

第一次比较5和4，5>4，交换位置：{4，5，7，1，6，2}

第二次比较5和7，5

第三次比较7和1，7>1，交换位置：{4，5，1，7，6，2}

第四次比较7和6，7>6，交换位置：{4，5，1，6，7，2}

第五次比较7和2，7>2，交换位置：{4，5，1，6，2，7}

第一次循环完成结果：{4，5，1，6，2，7}

\----------------------------------------

第二次循环：

第一次比较4和5，4

第二次比较5和1，5>1，交换位置：{4，1，5，6，2，7}

第三次比较5和6，5

第四次比较6和2，6>2，交换位置：{4，1，5，2，6，7}

第五次比较6和7，6

第二次循环完成结果：{4，1，5，2，6，7}

\----------------------------------------

第三次循环：

第一次比较4和1，4>1，交换位置：{1，4，5，2，6，7}

第二次比较4和5，4

第三次比较5和2，5>2，交换位置：{1，4，2，5，6，7}

第四次比较5和6，5

第五次比较6和7，6

第三次循环完成结果：{1，4，2，5，6，7}

\----------------------------------------

第四次循环：

第一次比较1和4，1

第二次比较4和2，4>2，交换位置：{1，2，4，5，6，7}

第三次比较4和5，4

第四次比较5和6，5

第五次比较6和7，6

第三次循环完成结果：{1，2，4，5，6，7}

\----------------------------------------

第五次循环：

第一次比较1和2，1

第二次比较2和4，2

第三次比较4和5，4

第四次比较5和6，5

第五次比较6和7，6

第三次循环完成结果：{1，2，4，5，6，7}

相信看完上面的演示过程，你对冒泡排序过程及原理有了完全的理解，但是细心的朋友应该会发现其实在第四次循环就已经得到了最终的结果，这么来看第五次循环完全是多余的，于是就有冒泡排序的改进版本：当某一轮循环当中没有交换位置的操作，说明已经排好序了，就没必要再循环了，break退出循环即可。

**复杂度分析：**

- 时间复杂度：若给定的数组刚好是排好序的数组，采用改进后的冒泡排序算法，只需循环一次就行了，此时是最优时间复杂度：O(n)，若给定的是倒序，此时是最差时间复杂度：O(n^2) ，因此综合平均时间复杂度为：O(n^2)
- 空间复杂度：因为每次只需开辟一个temp的空间，因此空间复杂度是：O(1)

**代码实现：**

- [bubble_sort.cpp](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/bubble_sort.cpp)

```c++
/* Summary: 冒泡排序
* Author: Amusi
* Date:   208-05-27
*
* Reference: 
*	http://en.wikipedia.org/wiki/Bubble_sort
*	https://github.com/xtaci/algorithms/blob/master/include/bubble_sort.h
*   https://zhuanlan.zhihu.com/p/37077924
*
* 冒泡排序说明：比较相邻的两个元素，将值大的元素交换到右边（降序则相反）
*
*/

#include <iostream>

// 冒泡函数
namespace alg{
	template<typename T>
	static void BubbleSort(T list[], int length)
	{

#if 1
		// 版本1：两层for循环 
		for (int i = 0; i < length-1; ++i)
		{ 
			for (int j = 0; j < length - i -1; ++j)
			{
				// 两两相邻元素比较大小，从小到大排序
				// if (list[j] < list[j + 1]) : 从大到小排序
				if (list[j] > list[j + 1])
				{
					int temp = list[j + 1];
					list[j + 1] = list[j];
					list[j] = temp;
				}
			}
		}

#else
		// 版本2：while+一层for循环
		bool swapped = false;
		while (!swapped)
		{
			swapped = true;
	
			for (int i = 0; i < length - 1; ++i)
			{
				// 两两相邻元素比较大小，从小到大排序
				// if (list[j] < list[j + 1]) : 从大到小排序
				if (list[i] > list[i + 1])
				{
					int temp = list[i + 1];
					list[i + 1] = list[i];
					list[i] = temp;
				}
				swapped = false;
			}
			length--;
		}
			
#endif

	}
}

using namespace std;
using namespace alg;

int main()
{
	int a[8] = { 5, 2, 5, 7, 1, -3, 99, 56 };
	BubbleSort<int>(a, 8);
	for (auto e : a)
		std::cout << e << " ";

	return 0;
}
```

- [bubble_sort.py](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/bubble_sort.py)

```c++
''' Summary: 冒泡排序
* Author: Amusi
* Date:   208-05-27
*
* Reference: 
*	http://en.wikipedia.org/wiki/Bubble_sort
*	https://github.com/xtaci/algorithms/blob/master/include/bubble_sort.h
*   https://zhuanlan.zhihu.com/p/37077924
*
* 冒泡排序说明：比较相邻的两个元素，将值大的元素交换到右边（降序则相反）
*
'''

def BubbleSort(array):
    lengths = len(array)
    for i in range(lengths-1):
        for j in range(lengths-1-i):
            if array[j] > array[j+1]:
               array[j+1], array[j] = array[j], array[j+1]

    return array


array = [1,3,8,5,2,10,7,16,7,4,5]
print("Original array: ", array)
array = BubbleSort(array)
print("BubbleSort: ", array)
```

**参考：**

1 [经典排序算法之冒泡排序](http://baijiahao.baidu.com/s?id=1585931471155461767&wfr=spider&for=pc)

2 (图示版)：[来、通俗聊聊冒泡排序](https://zhuanlan.zhihu.com/p/37077924)

### 005 选择排序（Selection Sort）

**基本思想**

首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

**步骤**

n个记录的直接选择排序可经过n-1趟直接选择排序得到有序结果。具体算法描述如下：

- 初始状态：无序区为R[1..n]，有序区为空；
- 第i趟排序(i=1,2,3…n-1)开始时，当前有序区和无序区分别为R[1..i-1]和R(i..n）。该趟排序从当前无序区中-选出关键字最小的记录 R[k]，将它与无序区的第1个记录R交换，使R[1..i]和R[i+1..n)分别变为记录个数增加1个的新有序区和记录个数减少1个的新无序区；
- n-1趟结束，数组有序化了。

**动图演示**

![selection_sort](https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/selection_sort.gif)

**复杂度分析**

- 时间复杂度：O(n2)

  注：无论什么数据进去，选择都是O(n2)的时间复杂度，所以若要使用它，建议数据规模越小越好。

- 空间复杂度：O(1)

**代码实现**

- [selection_sort.cpp](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/selection_sort.cpp)

```c++
/* Summary: 选择排序
* Author: Amusi
* Date:   208-06-22
*
* Reference: 
*   https://en.wikipedia.org/wiki/Selection_sort
*   https://github.com/xtaci/algorithms/blob/master/include/selection_sort.h
* 选择排序说明：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。 
*
*/

#include <iostream>

// 选择排序函数
namespace alg{
	template<typename T>
	static void SelectionSort(T list[], int length)
	{
		// 外循环: length-1次，因为当length-1个元素排序好后，第length个元素位置不再变化
		for (int i = 0; i < length-1; ++i)
		{ 
			int minIndex = i;
			// 从i的位置，进行遍历，因为前i-1个元素已经排序好
			for (int j = i; j < length; ++j)
			{
				// 每次从未排序的数组中选出最小的值放入已排序的数组中，即从小到大排序
				if (list[j] < list[minIndex])
				{
					minIndex = j;
				}
			}
			int temp = list[minIndex];
			list[minIndex] = list[i];
			list[i] = temp;
		}

	}
}

using namespace std;
using namespace alg;

int main()
{
	int a[8] = { 5, 2, 5, 7, 1, -3, 99, 56 };
	SelectionSort<int>(a, 8);
	for (auto e : a)
		std::cout << e << " ";

	return 0;
}
```

- [selection_sort.py](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/selection_sort.py)

```c++
''' Summary: 选择排序
* Author: Amusi
* Date:   208-06-22
*
* Reference: 
*   https://en.wikipedia.org/wiki/Selection_sort
*
* 选择排序说明：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。 
*
'''

def SelectionSort(array):
    lengths = len(array)
    for i in range(lengths-1):
        min_index = i
        for j in range(i, lengths):
            if array[j] < array[min_index]:
               min_index = j
        array[i], array[min_index] = array[min_index], array[i]
		
    return array


array = [1,3,8,5,2,10,7,16,7,4,5]
print("Original array: ", array)
array = SelectionSort(array)
print("SelectionSort: ", array)
```



### 006 插入排序（Insertion Sort）

**基本思想**

插入排序（insertion sort）又称直接插入排序（staright insertion sort），其是将未排序元素一个个插入到已排序列表中。对于未排序元素，在已排序序列中从后向前扫描，找到相应位置把它插进去；在从后向前扫描过程中，需要反复把已排序元素逐步向后挪，为新元素提供插入空间。

**步骤**

1. 从第一个元素开始，该元素可以认为已经被排序；
2. 取出下一个元素（未排序），在已经排序的元素序列中从后向前扫描；
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置（往前移动）；
4. 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置；
5. 将新元素插入到该位置后；
6. 重复步骤2~5。

**动图演示**

![插入排序图示](https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/insertion_sort.gif)

**复杂度分析**

- 时间复杂度：最坏O(n2)、平均O(n2)、最好O(n)
- 空间复杂度：O(n1)
- 稳定性：稳定

举个例子（暴力手绘图）

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/insertion_sort.png" width = 400 height = 600 align = center>

**代码实现**

- [insertion_sort.cpp](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/insertion_sort.cpp)

```c++
/* Summary: 插入排序（Insertion Sort）
* Author: Amusi
* Date:   2018-07-16
*
* Reference: 
*   https://en.wikipedia.org/wiki/Insertion_sort
*	
* 插入排序（insertion sort）又称直接插入排序（staright insertion sort），其是将未排序元素一个个插入到已排序列表中。对于未排序元素，在已排序序列中从后向前扫描，找到相应位置把它插进去；在从后向前扫描过程中，需要反复把已排序元素逐步向后挪，为新元素提供插入空间。
*
*/

#include <iostream>

// 插入排序函数
namespace alg{
	template<typename T>
	static void InsertionSort(T list[], int length)
	{
		// 从索引为1的位置开始遍历
		for (int i = 1; i < length; ++i)
		{
			T currentValue = list[i];	// 保存当前值
			int preIndex = i - 1;	// 前一个索引值
			// 循环条件: 前一个索引值对应元素值大于当前值 && 前一个索引值大于等于0
			while (list[preIndex] > currentValue && preIndex >= 0){
				list[preIndex + 1] = list[preIndex];
				preIndex--;
			}
			list[preIndex + 1] = currentValue;
		}
	}
}

using namespace std;
using namespace alg;

int main()
{
	int a[8] = { 5, 2, 5, 7, 1, -3, 99, 56 };
	InsertionSort<int>(a, 8);
	for (auto e : a)
		std::cout << e << " ";

	return 0;
}
```

- [insertion_sort.py](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/insertion_sort.py)

```c++
''' Summary: 插入排序（Insertion Sort）
* Author: Amusi
* Date:   208-07-16
*
* Reference: 
*   https://en.wikipedia.org/wiki/Insertion_sort
*   https://www.cnblogs.com/wujingqiao/articles/8961890.html
*	
* 插入排序（insertion sort）又称直接插入排序（staright insertion sort），其是将未排序元素一个个插入到已排序列表中。对于未排序元素，在已排序序列中从后向前扫描，找到相应位置把它插进去；在从后向前扫描过程中，需要反复把已排序元素逐步向后挪，为新元素提供插入空间。
*
'''

def InsertionnSort(array):
    lengths = len(array)
    # 从索引位置1开始
    for i in range(1, lengths):
        currentValue = array[i] # 当前索引对应的元素数值
        preIndex = i-1		     # 前一个索引位置
        # 循环条件: 前一个索引对应元素值大于当前值，前一个索引值大于等于0
        while array[preIndex] > currentValue and preIndex>=0:
        	array[preIndex+1] = array[preIndex]	# 前一个索引对应元素值赋值给当前值
        	preIndex -= 1	# 前一个索引位置-1
        # preIndex+1，实现元素交换
        array[preIndex+1] = currentValue
		
    return array


array = [1,3,8,5,2,10,7,16,7,4,5]
print("Original array: ", array)
array = InsertionnSort(array)
print("InsertionnSort: ", array)
```



### 007 希尔排序（Shell Sort）

**基本思想**

设待排序元素序列有n个元素，首先取一个整数increment（小于n）作为间隔将全部元素分为increment个子序列，所有距离为increment的元素放在同一个子序列中，在每一个子序列中分别实行直接插入。

注：在[希尔排序算法](https://en.wikipedia.org/wiki/Shellsort)提出之前，排序算法的时间复杂度都为O(n^2)，如冒泡排序、选择排序和插入排序。而希尔排序算法是突破这个时间复杂度的第一批算法之一。该复杂度为O(nlogn)，其实直接插入排序算法的改进版，也称为缩小增量排序。**希尔排序是直接插入排序的一种改进，减少了其复制的次数，速度要快很多**。原因是，当n值很大时，数据项每一趟排序需要移动的个数很少，但数据项的距离很长；当n值减小时，每一趟需要移动的数据增多。正是因为这两种情况的结合才使得希尔排序效率比插入排序高很多。

**步骤**

先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，具体算法描述：

- 选择一个增量序列t1，t2，…，tk
- 按增量序列个数k，对序列进行k 趟排序；
- 每趟排序，根据对应的增量ti，将待排序列分割成若干长度为m 的子序列，分别对各子表进行直接插入排序。仅增量因子为1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

注：增量的初始值一般为序列长度的一半，然后之后每次再自身减半。

**动图演示**

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/shell_sort01.gif" width = 600 height = 290 align = center>

上图是不是很难理解，那么来看看这个！

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/shell_sort02.png" width = 600 height = 680 align = center>

说实话，看了上面两个图，我还是有点不理解，不怕，我又找了下面这幅图像。

简单介绍一下：

对于[592, 401, 874, 141, 348, 72, 911, 887, 820, 283]序列，一共10个元素。

**第一次，设置增量为5=10/2**，所以有5个子序列：[592,72]、[401,911]、[874,887]、[141,820]和[348,283]。然后对每个子序列进行插入排序，结果是[72,592]、[401,911]、[874,887]、[141,820]和[283,348]。注意，这些元素在序列中的位置初始是不变的，只会随着部分子序列间元素的位置变化而变化，比如[592,72]在原来的序列中索引是[0,5]，之后变成了[5,0]；而[401,911]的序列索引是[1,6]，第一次处理后，索引值不变。

总之，第一次处理的结果是将**[592, 401, 874, 141, 348, 72, 911, 887, 820, 283]—> [72, 401, 874, 141, 283, 592, 911, 887, 820, 348]**。

**第二次，设置增量为2=5/2**，所以有2个子序列：[72, 874, 283, 911, 820]和[401, 141, 592, 887, 348]。然后对每个子序列进行插入排序，[72, 874, 283, 911, 820]的结果是[72, 283, 820, 874, 911]，而[401, 141, 292, 887, 348]的结果是[141, 292, 348, 401, 887]。

总之，第二次处理的结果是将 **[72, 401, 874, 141, 283, 592, 911, 887, 820, 348]—>[72, 141, 283, 292, 820, 348, 874, 401, 911, 997]**。

**第三次，设置增量为1=2/2**，所以有1个序列，就是上述生成的序列[72, 141, 283, 292, 820, 348, 874, 401, 911, 997]。然后进行插入排序，其结果为[72, 141, 283, 292, 348, 401, 820, 874, 911, 997]。

总之，第三次处理的结果是将 **[72, 141, 283, 292, 820, 348, 874, 401, 911, 997]—>[72, 141, 283, 292, 348, 401, 820, 874, 911, 997]**。

其实本质上还是利用了插入排序，但这里通过增量作用，相当于添加了预处理，减少插入排序中移动元素的次数，提高了效率。通过"增量"预处理，使得希尔排序算法时间复杂度降低。

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/shell_sort03.jpg" width = 400 height = 380 align = center>

**复杂度分析**

希尔排序的时间复杂度与增量序列的选取有关。

时间复杂度：

- 平均：O(n^1.3)
- 最差：O(n2)
- 最好：O(n)

空间复杂度：O(1)

稳定性：不稳定

**代码实现**

[shell_sort.cpp](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/shell_sort.cpp)

```c++
/* Summary: 希尔排序（Shell Sort）
* Author: Amusi
* Date:   2018-09-23
*
* Reference:
*   https://en.wikipedia.org/wiki/Shellsort
*   https://www.geeksforgeeks.org/shellsort/
*   希尔排序（shell sort）：设待排序元素序列有n个元素，首先取一个整数increment（小于n）作为间隔将全部元素分为increment个子序列，所有距离为increment的元素放在同一个子序列中，在每一个子序列中分别实行直接插入。
*
*/

#include <iostream>

// 希尔排序函数（基于快速插入排序）
namespace alg{
	template<typename T>
	static void ShellSort(T list[], int n)
	{
		// 设置增量：以n/2为初始gap，然后逐渐减小gap（每次缩小为上次gap的一半）
		for (int gap = n / 2; gap > 0; gap /= 2){
			// 遍历当前趟，对每个子序列进行插入排序
			for (int i = gap; i < n; i++){
				int temp = list[i];
				int j = 0;
				// 遍历子序列
				for (j = i; j >= gap && list[j - gap]>temp; j -= gap)
					list[j] = list[j - gap];

				list[j] = temp;
			}
		}
	}
}

using namespace std;
using namespace alg;

int main()
{
	int a[8] = { 5, 2, 5, 7, 1, -3, 99, 56 };
	int n = sizeof(a) / sizeof(a[0]);
	ShellSort<int>(a, n);
	for (auto e : a)
		std::cout << e << " ";

	return 0;
}
```

[shell_sort.py](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/shell_sort.py)

```c++
'''
* Summary: 希尔排序（Shell Sort）
* Author: Amusi
* Date:   2018-09-23
*
* Reference:
*   https://en.wikipedia.org/wiki/Shellsort
*   https://www.geeksforgeeks.org/shellsort/
*   希尔排序（shell sort）：设待排序元素序列有n个元素，首先取一个整数increment（小于n）作为间隔将全部元素分为increment个子序列，所有距离为increment的元素放在同一个子序列中，在每一个子序列中分别实行直接插入。
*
*
'''

def ShellSort(array):
    lengths = len(array)
    # 初始化gap
    gap = lengths//2
    # 减少增量，遍历子序列进行插入排序
    while(gap > 0):
        for i in range(gap, lengths):
            # 
            temp = array[i]
            j = i
            # 子序列插入排序
            while j>=gap and array[j-gap]>temp:
                array[j] = array[j-gap]
                j -= gap

            array[j] = temp
        
        gap = gap//2
		
    return array

array = [1,3,8,5,2,10,7,16,7,4,5]
print("Original array: ", array)
array = ShellSort(array)
print("InsertionnSort: ", array)
```

参考：

- [Shell Sort](https://www.geeksforgeeks.org/shellsort/)
- [理解希尔排序的排序过程](https://blog.csdn.net/weixin_37818081/article/details/79202115)
- [图解排序算法(二)之希尔排序](https://www.cnblogs.com/chengxiao/p/6104371.html)

### 008 堆排序（Heap Sort）

**基本思想**

[堆排序（heap sort）](https://en.wikipedia.org/wiki/Heapsort)：将待排序序列构造成一个大顶堆，此时，整个序列的最大值就是堆顶的根节点。将其与末尾元素进行交换，此时末尾就为最大值。然后将剩余n-1个元素重新构造成一个堆，这样会得到n个元素的次小值。如此反复执行，便能得到一个有序序列了。

注：堆排序是一种选择排序，指利用堆这种数据结构所设计的一种排序算法。堆积是一个近似完全二叉树的结构，并同时满足堆积的性质：即子结点的键值或索引总是小于（或者大于）它的父节点。

在介绍堆排序之前，先了解一下什么是**堆（heap）**？

**堆**

堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆；或者每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆。如下图：

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap.png" width = 700 height = 330 align = center>

同时，我们对堆的结点按层进行编号，将这种逻辑结构映射到数组中就是下面这个样子：

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap2.png" width = 400 height = 120 align = center>

该数组从逻辑上讲就是一个堆结构，我们用简单的公式来描述一下堆的定义就是：

**大顶堆：arr[i] >= arr[2i+1] && arr[i] >= arr[2i+2] **
**小顶堆：arr[i] <= arr[2i+1] && arr[i] <= arr[2i+2] **

ok，了解了这些定义。接下来，我们来看看堆排序的基本思想及基本步骤：

**步骤**

- 将初始待排序关键字序列(R1,R2….Rn)构建成大顶堆，此堆为初始的无序区；
- 将堆顶元素R[1]与最后一个元素R[n]交换，此时得到新的无序区(R1,R2,……Rn-1)和新的有序区(Rn),且满足R[1,2…n-1]<=R[n]；
- 由于交换后新的堆顶R[1]可能违反堆的性质，因此需要对当前无序区(R1,R2,……Rn-1)调整为新堆，然后再次将R[1]与无序区最后一个元素交换，得到新的无序区(R1,R2….Rn-2)和新的有序区(Rn-1,Rn)。不断重复此过程直到有序区的元素个数为n-1，则整个排序过程完成。

**动图演示**

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_sort.gif" width = 500 height = 320 align = center>

看到这里，你可能还是晕乎乎的，下面看个讲解示例：

**步骤一 构造初始堆。将给定无序序列构造成一个大顶堆（一般升序采用大顶堆，降序采用小顶堆)。**

a.假设给定无序序列结构如下

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_p1.png" width = 400 height = 360 align = center>

b.此时我们从最后一个非叶子结点开始（叶结点自然不用调整，第一个非叶子结点 arr.length/2-1=5/2-1=1，也就是下面的6结点），从左至右，从下至上进行调整。

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_p2.png" width = 700 height = 320 align = center>

c.找到第二个非叶节点4，由于[4,9,8]中9元素最大，4和9交换。

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_p3.png" width = 700 height = 320 align = center>

d.这时交换导致了子根[4,5,6]结构混乱，继续调整，[4,5,6]中6最大，交换4和6。

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_p4.png" width = 700 height = 300 align = center>

此时，我们就将一个无需序列构造成了一个大顶堆。

**步骤二 将堆顶元素与末尾元素进行交换，使末尾元素最大。然后继续调整堆，再将堆顶元素与末尾元素交换，得到第二大元素。如此反复进行交换、重建、交换。**

a.将堆顶元素9和末尾元素4进行交换

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_p5.png" width = 700 height = 300 align = center>

b.重新调整结构，使其继续满足堆定义

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_p6.png" width = 700 height = 300 align = center>

c.再将堆顶元素8与末尾元素5进行交换，得到第二大元素8.

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_p7.png" width = 700 height = 300 align = center>

后续过程，继续进行调整，交换，如此反复进行，最终使得整个序列有序

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/heap_p8.png" width = 400 height = 320 align = center>

再简单总结下堆排序的基本思路：

**a.将无需序列构建成一个堆，根据升序降序需求选择大顶堆或小顶堆;**
**b.将堆顶元素与末尾元素交换，将最大元素"沉"到数组末端;**
**c.重新调整结构，使其满足堆定义，然后继续交换堆顶元素与当前末尾元素，反复执行调整+交换步骤，直到整个序列有序。**

**复杂度分析**

堆排序整体主要由构建初始堆+交换堆顶元素和末尾元素并重建堆两部分组成。其中构建初始堆经推导复杂度为O(n)，在交换并重建堆的过程中，需交换n-1次，而重建堆的过程中，根据完全二叉树的性质，[log2(n-1),log2(n-2)...1]逐步递减，近似为nlogn。所以堆排序时间复杂度一般认为就是O(nlogn)级。

时间复杂度：

- 最差：O(nlogn)
- 平均：O(nlogn)
- 最优：O(nlogn)

空间复杂度：O(n)

稳定性：不稳定

**代码实现**


参考：

- [图解排序算法(三)之堆排序](https://www.cnblogs.com/chengxiao/p/6129630.html)
- [heap-sort](https://www.geeksforgeeks.org/heap-sort/)

### 009 归并排序（Merge Sort）

**基本思想**

[归并排序（merge sort）](https://en.wikipedia.org/wiki/Merge_sort)：

**步骤**

- [ ] TODO

### 010 快速排序（Quick Sort）

**基本思想**

[快速排序（quick sort）](https://en.wikipedia.org/wiki/Quicksort)：通过一趟排序将待排列表分隔成独立的两部分，其中一部分的所有元素均比另一部分的所有元素小，则可分别对这两部分继续重复进行此操作，以达到整个序列有序。（这个过程，我们可以使用递归快速实现）

**步骤**

快速排序使用分治法来把一个串（list）分为两个子串（sub-lists）。具体算法描述如下：

- 从数列中挑出一个元素，称为 “基准”（pivot），这里我们通常都会选择第一个元素作为prvot；
- 重新排序数列，将比基准值小的所有元素放在基准前面，比基准值大的所有元素放在基准的后面（相同的数可以到任一边）。这样操作完成后，该基准就处于新数列的中间位置，即将数列分成了两部分。这个操作称为分区（partition）操作；
- 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数按上述操作进行排序。这里的递归结束的条件是序列的大小为0或1。此时递归结束，排序就完成了。

**动图演示**

[![Quick Sort](https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/quick_sort.gif)](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/images/quick_sort.gif)

**复杂度分析**

- 时间复杂度：
  - 平均情况：O(nlogn)
  - 最好情况：O(nlong)
  - 最坏情况：O(n^2) 其实不难理解，快排的最坏情况就已经退化为冒泡排序了！所以大家深入理解就会发现各个排序算法是相通的，学习时间久了你就会发现它们的内在联系！是不是很神奇哈～
- 空间复杂度：
  - 平均情况：O(logn)
  - 最好情况：O(logn)
  - 最坏情况：O(n)
- 稳定性：不稳定 （由于关键字的比较和交换是跳跃进行的，所以快速排序是一种不稳定的排序方法～）

举个例子（暴力手绘图）

<img src = "https://github.com/amusi/coding-note/raw/master/Data%20Structures%20and%20Algorithms/sort/images/quick_sort.png" width = 400 height = 550 align = center>

**代码实现**

注：下面都是利用递归法实现快速排序。

[quick_sort.cpp](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/quick_sort.cpp)

```c++
/* Summary: 快速排序（Quick Sort）
* Author: Amusi
* Date:   2018-07-28
*
* Reference: 
*   https://en.wikipedia.org/wiki/Quicksort
*	
* 快速排序（quick sort）：通过一趟排序将待排列表分隔成独立的两部分，其中一部分的所有元素均比另一部分的所有元素小，则可分别对这两部分继续重复进行此操作，以达到整个序列有序。（这个过程，我们可以使用递归快速实现）
*
*/

#include <iostream>

// 快速排序函数（递归法）
namespace alg{
	template<typename T>
	static void QuickSort(T list[], int start, int end)
	{
		int i = start;
		int j = end;
		// 结束排序（左右两索引值见面，即相等，或者左索引>右索引）
		if (i >= j)
			return;
		// 保存首个数值（以首个数值作为基准）
		// 这个位置很重要，一定要在if i >= j判断语句之后，否则就索引溢出了
		T pivot = list[i];

		// 一次排序，i和j的值不断的靠拢，然后最终停止，结束一次排序
		while (i < j){
			// 一层循环实现从左边起大于基准的值替换基准的位置，右边起小于基准的值位置替换从左起大于基准值的索引
			//（从右往左）和最右边的比较，如果 >= pivot, 即满足要求，不需要交换，然后j - 1，慢慢左移，即拿基准值与前一个值比较; 如果值<pivot，那么就交换位置
			while (i < j && pivot <= list[j])
				--j;
			list[i] = list[j];
			// 交换位置后，（从左往右）然后在和最左边的值开始比较，如果 <= pivot, 然后i + 1，慢慢的和后一个值比较; 如果值>pivot，那么就交换位置
			while (i < j && pivot >= list[i])
				++i;
			list[j] = list[i];
		}
		// 列表中索引i的位置为基准值，i左边序列都是小于基准值的，i右边序列都是大于基准值的，当前基准值的索引为i，之后不变
		list[i] = pivot;
		// 左边排序
		QuickSort(list, start, i-1);
		// 右边排序
		QuickSort(list, i+1, end);
	}
}

using namespace std;
using namespace alg;

int main()
{
	int a[8] = { 5, 2, 5, 7, 1, -3, 99, 56 };
	QuickSort<int>(a, 0, sizeof(a)/sizeof(a[0]) - 1);
	for (auto e : a)
		std::cout << e << " ";

	return 0;
}
```

[quick_sort.py](https://github.com/amusi/coding-note/blob/master/Data%20Structures%20and%20Algorithms/sort/code/quick_sort.py)

```c++
''' Summary: 快速排序（Quick Sort）
* Author: Amusi
* Date:   208-07-28
*
* Reference: 
*   https://en.wikipedia.org/wiki/Quicksort
*   https://www.cnblogs.com/wujingqiao/articles/8961890.html
*	https://github.com/apachecn/LeetCode/blob/master/src/py3.x/sort/QuickSort.py
*   快速排序（quick sort）：通过一趟排序将待排列表分隔成独立的两部分，其中一部分的所有元素均比另一部分的所有元素小，则可分别对这两部分继续重复进行此操作，以达到整个序列有序。（这个过程，我们可以使用递归快速实现）
*
'''

def QuickSort(array, start, end):
    lengths = len(array)
    i = start
    j = end
    # 结束排序（左右两索引值见面，即相等，或者左索引>右索引）
    if i >= j:
        return	# 返回空即可
    # 保存首个数值（以首个数值作为基准）
	# 这个位置很重要，一定要在if i>=j判断语句之后，否则就索引溢出了
    pivot = array[i]
    # 一次排序，i和j的值不断的靠拢，然后最终停止，结束一次排序
    while i < j:
        # （从右往左）和最右边的比较，如果>=pivot,即满足要求，不需要交换，然后j-1，慢慢左移，即拿基准值与前一个值比较; 如果值<pivot，那么就交换位置
        while i < j and pivot <= array[j]:
            # print(pivot, array[j], '*' * 30)
            j -= 1
        array[i] = array[j]
        # 交换位置后，然后在和最左边的值开始比较，如果<=pivot,然后i+1，慢慢的和后一个值比较;如果值>pivot，那么就交换位置
        while i < j and pivot >= array[i]:
            # print(pivot, array[i], '*' * 30)
            i += 1
        array[j] = array[i]
    # 列表中索引i的位置为基准值，i左边序列都是小于基准值的，i右边序列都是大于基准值的，当前基准值的索引为i，之后不变
    array[i] = pivot
    # 左边排序
    QuickSort(array, start, i-1)
    # 右边排序
    QuickSort(array, i+1, end)
		
    #return array

if __name__ == "__main__":
    array = [1,3,8,5,2,10,7,16,7,4,5]
    print("Original array: ", array)
    #array = QuickSort(array, 0, len(array)-1)
	# 因为python中的list对象是可变对象，所以在函数做"形参"时，是相当于按引用传递
	# 所以不写成返回值的形式，也是OK的
    QuickSort(array, 0, len(array)-1)
    print("QuickSort: ", array)
```

### 011 求二叉树的最大高度

### 012 找到链表倒数第k个结点

### 013 动态规划

### 014 打印螺旋矩阵

### 015 翻转链表

### 016 找最小字串

### 017 二叉排序树/二叉查找树BST

### 018 平衡二叉树

### 019 2-sum、3-sum和4-sum问题

N-sum就是从序列中找出N个数，使得N个数之和等于指定数值的问题。

**2-sum**

2-sum就是从序列中找出2个数，使得2个数之和等于0的问题，即a+b=sum。

下面的例子是规定指定数值为0：

```cpp
int sum_2()
{
	int res = 0;
	int n = data.size();
	for(int i=0; i<n-1; i++)
	{
		for(int j=i+1; j<n; j++)
		{
			if(data[i] + data[j] == 0)
			{
				res ++;
			}
		}
	}
	return res;
}
```

上述算法的由于包含了两层循环，因此时间复杂度为O(N^2)。

观察发现，上述算法时间主要花费在数据比对，为此可以考虑使用二分查找来减少数据比对时间，要想使用二分查找，首先应该对数据进行排序，在此使用归并排序或者快速排序对数组进行升序排列。排序所花时间为O(NlogN)，排序之后数据查找只需要O(logN)的时间，但是总共需要查找N次，为此改进后算法的时间复杂度为O(NlogN)。

```cpp
int cal_sum_2()
{
	int res = 0;
	for(int i=0; i<data.size(); i++)
	{
		int j = binary_search(-data[i]);
		if(j > i)	
		res++;
	}
	return res;
}
```

**3-sum**

上述2-sum的解题思路适用于3-sum及4-sum问题，如求解a+b+c=0，可将其转换为求解a+b=-c，此就为2-sum问题。

为此将2-sum，3-sum，4-sum的求解方法以及相应的优化方法实现在如下所示的sum类中。

sum.h

```cpp
#ifndef SUM_H
#define SUM_H
#include <vector>
using std::vector;
class sum
{
private:
	vector<int> data;
public:
	sum(){};
	sum(const vector<int>& a);
	~sum(){};
	int cal_sum_2() const;
	int cal_sum_3() const;
	int cal_sum_4() const;
	int cal_sum_2_update() const;
	int cal_sum_3_update() const;
	int cal_sum_3_update2() const;
	int cal_sum_4_update() const;
	void sort(int low, int high);
	void print() const;
	friend int find(const sum& s, int target); 
};
#endif
```

sum.cpp

```cpp
#include "Sum.h"
#include <iostream>
using namespace std;
sum::sum(const vector<int>& a)
{
	data = a;
}
void sum::sort(int low, int high)
{
	if(low >= high)
		return;
	int mid = (low+high)/2;
	sort(low,mid);
	sort(mid+1,high);
	vector<int> temp;
	int l = low;
	int h = mid+1;
	while(l<=mid && h <=high)
	{
		if(data[l] > data[h])
			temp.push_back(data[h++]);
		else
			temp.push_back(data[l++]);
	}
	while(l<=mid)
		temp.push_back(data[l++]);
	while(h<=high)
		temp.push_back(data[h++]);
	for(int i=low; i<=high; i++)
	{
		data[i] = temp[i-low];
	}
}
void sum::print() const
{
	for(int i=0; i<data.size(); i++)
	{
		cout<<data[i]<<" ";
	}
	cout<<endl;
}
int find(const sum& s, int target)
{
	int low = 0;
	int high = s.data.size()-1;
	while(low <= high)
	{
		int mid = (low + high)/2;
		if(s.data[mid] < target)
		{
			low = mid+1;
		}
		else if(s.data[mid] > target)
		{
			high = mid - 1;
		}
		else
		{
			return mid;
		}
	}
	return -1;
}
int sum::cal_sum_2() const
{
	int res = 0;
	for(int i=0; i<data.size(); i++)
	{
		int j = find(*this, -data[i]);
		if(j > i)	
			res++;
	}
	return res;
}
int sum::cal_sum_3() const
{
	int res = 0;
	for(int i=0; i<data.size(); i++)
	{
		for(int j=i+1; j<data.size(); j++)
		{
			for(int p=j+1;p<data.size();p++)
			{
				if(data[i] + data[j] + data[p] == 0)
					res++;
			}
		}
	}
	return res;
}
int sum::cal_sum_4() const
{
	int res = 0;
	for(int i=0; i<data.size(); i++)
	{
		for(int j=i+1; j<data.size(); j++)
		{
			for(int p=j+1; p<data.size(); p++)
			{
				for(int q=p+1; q<data.size(); q++)
				{
					if(data[i]+data[j]+data[p]+data[q] == 0)
						res++;
				}
			}
		}
	}
	return res;
}
int sum::cal_sum_2_update() const
{
	int res = 0;
	for(int i=0,j=data.size()-1; i<j; )
	{
		if(data[i] + data[j] > 0)
			j--;
		else if(data[i] + data[j] < 0)
			i++;
		else
		{
			res++;
			j--;
			i++;
		}
	}
	return res;
}
int sum::cal_sum_3_update() const
{
	int res = 0;
	for(int i=0; i<data.size(); i++)
	{
		for(int j=i+1; j<data.size(); j++)
		{
			if(find(*this, -data[i] - data[j]) > j)
				res ++;
		}
	}
	return res;
}
int sum::cal_sum_3_update2() const
{
	int res = 0;
	for(int i=0; i<data.size(); i++)
	{
		int j=i+1;
		int p=data.size()-1;
		while(j<p)
		{
			if (data[j] + data[p] < -data[i])
				j++;
			else if(data[j] + data[p] > -data[i])
				p--;
			else
			{
				res++;
				j++;
				p--;
			}
		}
	}
	return res;
}
int sum::cal_sum_4_update() const
{
	int res = 0;
	for(int i=0; i<data.size(); i++)
	{
		for(int j=i+1; j<data.size(); j++)
		{
			for(int p=j+1; p<data.size(); p++)
			{
				if(find(*this, -data[i]-data[j]-data[p])>p)
					res++;
			}
		}
	}
	return res;
}
```

test.cpp

```cpp
#include "Sum.h"
#include <iostream>
#include <fstream>
#include <vector>
using namespace std;
void main()
{
	ifstream in("1Kints.txt");
	vector<int> a;
	while(!in.eof())
	{
		int temp;
		in>>temp;
		a.push_back(temp);
	}
	sum s(a);
	s.sort(0,a.size()-1);
	s.print();
	cout<<"s.cal_sum_2() = "<<s.cal_sum_2()<<endl;
	cout<<"s.cal_sum_2_update() = "<<s.cal_sum_2_update()<<endl;
	cout<<"s.cal_sum_3() = "<<s.cal_sum_3()<<endl;
	cout<<"s.cal_sum_3_update() = "<<s.cal_sum_3_update()<<endl;
	cout<<"s.cal_sum_3_update()2 = "<<s.cal_sum_3_update2()<<endl;
	cout<<"s.cal_sum_4() = "<<s.cal_sum_4()<<endl;
	cout<<"s.cal_sum_4_update() = "<<s.cal_sum_4_update()<<endl;
}
```

**参考资料**

- [剖析3-sum问题(Three sum)](https://blog.csdn.net/shaya118/article/details/40755551)
- [2-sum, 3-sum, 4-sum问题分析](http://shmilyaw-hotmail-com.iteye.com/blog/2085129)

### 020 无序数组a，b 归并排序成有序数组c

### 021 将256*256二维数组逆时针旋转90°

### 022 二分查找

### 023 分治与递归：逆序对数、大数相加、大数相乘

### 024 动态规划：背包问题、找零钱问题和最长公共子序列（LCS）

### 025 BFS和DFS解决最短路径

### 026 DFS和BFS的区别（优缺点）

深度优先搜索算法（Depth-First-Search，DFS）是一种利用递归实现的搜索算法。简单来说，其搜索过程和"不撞南墙不回头"类似。

广度优先搜索算法（Breadth-First-Search，BFS）是一种利用队列实现的搜索算法。简单来说，其搜索过程和"湖面丢进一块石头激起层层涟漪"类似。

BFS 的重点在于队列，而 DFS 的重点在于递归。这是它们的本质区别。

**应用方向**

BFS 常用于找单一的最短路线，它的特点是 "搜到就是最优解"，而 DFS 用于找所有解的问题，它的空间效率高，而且找到的不一定是最优解，必须记录并完成整个搜索，故一般情况下，深搜需要非常高效的剪枝（剪枝的概念请百度）。

**参考资料**

- **[一文读懂** BFS 和 DFS 区别](https://www.sohu.com/a/201679198_479559)

### 027 数组的最大区间和

**[1]（推荐）**[《大话数据结构》](https://book.douban.com/subject/6424904/)

**[2]（推荐）**[十大经典排序算法（动图演示）](https://www.cnblogs.com/onepixel/p/7674659.html)

**[3]（推荐）**[轻松搞定十大排序算法(C++版)](https://blog.csdn.net/opooc/article/details/80994353)

**[4]（推荐）**[从头说12种排序算法：原理、图解、动画视频演示、代码以及笔试面试题目中的应用](https://blog.csdn.net/han_xiaoyang/article/details/12163251)

**[5]（推荐）**[排序算法时间复杂度、空间复杂度、稳定性比较](https://blog.csdn.net/yushiyi6453/article/details/76407640)

**[6]（推荐）**[十大排序算法和七大查找算法总结（原理讲解和Python代码实现）---（一）排序算法篇](https://www.cnblogs.com/wujingqiao/p/8961890.html)

**[7]** [常见排序算法C++总结](https://www.cnblogs.com/zyb428/p/5673738.html)

**[8]** [十大经典排序算法（JavaScript版）](http://web.jobbole.com/87968/)

**[9]** [八大排序算法总结（JAVA版）](http://www.runoob.com/w3cnote/sort-algorithm-summary.html)

**[10]** [C++代码](https://github.com/xtaci/algorithms/blob/master/include/selection_sort.h)

# 操作系统

### 001 下列哪些操作会使线程释放锁资源？

- wait()、join()

### 002 OSI模型的层次概念

- 应用层（Application Layer）提供为应用软件而设的接口，以设置与另一应用软件之间的通信。例如: HTTP，HTTPS，FTP，TELNET，SSH，SMTP，POP3等。
- 表达层（Presentation Layer）把数据转换为能与接收者的系统格式兼容并适合传输的格式。
- 会话层（Session Layer）负责在数据传输中设置和维护计算机网络中两台计算机之间的通信连接。
- 传输层（Transport Layer）把传输表头（TH）加至数据以形成数据包。传输表头包含了所使用的协议等发送信息。例如:传输控制协议（TCP）等。
- 网络层（Network Layer）决定数据的路径选择和转寄，将网络表头（NH）加至数据包，以形成分组。网络表头包含了网络数据。例如:互联网协议（IP）等。
- 数据链路层（Data Link Layer）负责网络寻址、错误侦测和改错。当表头和表尾被加至数据包时，会形成帧。数据链表头（DLH）是包含了物理地址和错误侦测及改错的方法。数据链表尾（DLT）是一串指示数据包末端的字符串。例如以太网、无线局域网（Wi-Fi）和通用分组无线服务（GPRS）等。
- 物理层（Physical Layer）在局部局域网上传送[数据帧](https://baike.baidu.com/item/数据帧)（data frame），它负责管理计算机通信设备和网络媒体之间的互通。包括了针脚、电压、线缆规范、集线器、中继器、网卡、主机适配器等。
