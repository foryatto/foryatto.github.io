# C++内存管理

C++中，内存分为5个区

- 栈

  由编译器在需要时自动分配，不需要时自动清除的变量存储区。通常存放局部变量、函数参数等。

- 堆

  由malloc/new分配的内存块，由程序员释放（编译器不管），一般一个malloc对应一个free。如果程序员没有释放掉， 资源将由操作系统在程序结束后自动回收。

- 自由存储区

  是C++中通过new和delete动态分配和释放对象的抽象概念，通过new来申请的内存区域可称为自由存储区。基本上，所有的C++编译器默认使用堆来实现自由存储。

- 全局/静态存储区

  全局变量和静态变量被分配到同一块内存中（在C语言中，全局变量又分为初始化的和未初始化的，C++中没有这一区分）。

- 常量存储区

  这是一块特殊存储区，里边存放常量，不允许修改。



# 多文件编程

一个完整的c++项目常常是多个代码文件组成，根据后缀名不同，大致分为两类:

- .h文件: 头文件，用于存放常量，函数的声明部分，类的声明部分
- .cpp文件: 源文件，用于存放变量，函数的定义部分，类的实现部分

```c++
// student.h
class Student{
public:
	const char *name;
	int age;
	float score;
	void say();
};

// student.cpp
#include <iostream>
#include "student.h"
void Student::say(){
	std::cout << name << " " << age;
}

// main.cpp
#include "student.h"
int main(){
	Student *pStu = new Student;
	pStu -> name = "wuzhongqiang";
	pStu -> age = 25;
	pStu -> score = 92.5f;
	pStu -> say();
	delete pStu;
}
```

引入编译器自带的头文件（包括标准头文件）用尖括号，例如 ；引入自定义的头文件用 “” 双引号，例如 “student.h”。

## 重复引入问题

 C++多文件编程中， 处理"多次#include导致重复引入"问题的方式， 有下面3种:
 - 1. 使用宏定义避免重复引入

```c++
#ifndef _NAME_H
#define _NAME_H
// 头文件内容
#endif
```
其中，_NAME_H是宏的名称。需要注意的是，这里设置的宏名必须是独一无二的，不要和项目中其他宏的名称相同。

当程序第一次#include该文件时， 由于`_NAME_H`尚未定义，所以会定义`_NAME_H`并执行"头文件内容代码"， 当发生多次#include时， 因为前面定义好了_NAME_H， 所以不会重复执行"头文件内容"部分的代码了。

所以， 将上面项目中student.h文件做如下修改：

```c++
#ifndef _STUDENT_H
#define _STUDENT_H
class Student{
	//....
};
#endif
```

虽然该项目 main.cpp 文件中仍 #include 了 2 次 "student.h"，但鉴于 _STUDENT_H 宏只能定义一次，所以 Student 类也仅会定义一次。再次执行该项目会发现，其可以正常执行。

 - 2. 使用#pragma once避免重复引入

除了第一种最常用方式之外，还可以使用#pragma once指令，将其附加到指定文件的最开头位置。则该文件只会被#include一次。
```c++
#pragma once
class Student{
	//.....
};
```
#ifndef是通过定义独一无二的宏来避免重复引入的， 这意味每次引入头文件都要进行识别，效率不高。 但考虑C和C++都支持宏定义，所以项目使用#ifndef规避可能出现的"头文件重复引入"问题，不会影响项目的可移植性。

#pragma once不涉及宏定义，当编译器遇到它时就会立刻知道当前文件只引入一次，所以效率很高。 但值得一提的是，并不是每个版本编译器都能识别#pragma once指令，所以兼容性可能不是很好。 另外， #pragma once只能作用于某个具体文件， 无法像#ifndef那样仅作用于指定的一段代码。

 - 3. 使用_Pragma操作符

C99标准中新增加了一个和#pragma指令类似的_Pragma操作符，可以看做是#pragma的plus版，不仅实现了#prama所有功能，更重要的， _Pragma还能和宏搭配使用。

当处理头文件重复引入问题， 可以把下面的具体加到相应文件开头:

```c++
_Pragma("once")
class Student {
	//......
};
```

事实上，无论是 C 语言还是 C++，为防止用户重复引入系统库文件，几乎所有库文件中都采用了以上 3 种结构中的一种，这也是为什么重复引入系统库文件编译器也不会报错的原因。


这三种方法的后两种其实是一类， 特点是编译效率高，但可移植性差(编译器不支持，会发出警告等)， 而#ifndef特点是可移植性高，编译效率差。 **一般常用的是#ifndef / #define / #endif 组合解决头文件被重复引入的问题。**



## 命名空间

多文件编程时， **命名空间在.h头文件中**

## const常量

- const常量直接定义在`.h`头文件中，只需要引入头文件， 就可以使用

- const常量的定义先可以遵循"声明在.h文件，定义在.cpp文件"， 借助extern关键字

  ```c++
  //demo.h
  #ifndef _DEMO_H
  #define _DEMO_H
  extern const int num;
  #endif
  
  // demo.cpp
  #include "demo.h"
  const int num = 10;
  
  // main.cpp
  #include <iostream>
  #include "demo.h"
  int main(){
  	std::cout << num << std::endl;
  }
  
  //C++ const 关键字会限定变量的可见范围为当前文件，即无法在其它文件中使用该常量。而 extern 关键字会 const 限定可见范围的功能，它可以使 const 常量的可见范围恢复至整个项目
  ```

- 借助extern直接定义const常量

  这个是第二种方式的简化， 直接在demo.cpp中定义和初始化即可。

  ```c++
  // demo.cpp
  extern const int num = 10;
  ```

  `extern`是一个关键字，它告诉编译器存在着一个变量或者一个函数，如果在当前编译语句的前面中没有找到相应的变量或者函数，也会在当前文件的后面或者其它文件中定义。

## 如何用g++命令执行

```c++
//student.h
class Student {
public:
    const char *name;
    int age;
    float score;
    void say();
};

//student.cpp
#include <iostream>   //std::cout、std::endl
#include "student.h"  //Student
void Student::say() {
    std::cout << name << "的年龄是" << age << "，成绩是" << score << std::endl;
}

//main.cpp
#include "student.h"  //Student
int main() {
    Student *pStu = new Student;
    pStu->name = "wuzhongqiang";
    pStu->age = 25;
    pStu->score = 66.6f;
    pStu->say();
    delete pStu;  //删除对象
    return 0;
}

```

```bash
$ g++ main.cpp student.cpp -o student.exe
```

C++多文件编程中，一个完整C++项目可以包含2类文件， 头文件(.h)和源文件(.cpp)。

同属一个C++项目中所有代码是分别进行编译的，只需要在编译成目标文件后再与其他目标文件做一次链接即可。

例如，在 a.cpp 源文件中定义有一个全局函数 a()，而在文件 b.cpp 中需要调用这个函数。即便如此，处于编译阶段的 a.cpp 和 b.cpp 并不需要知道对方的存在，它们各自是独立编译的是，只要最后将编译得到的目标文件进行链接，整个程序就可以运行。

那么，这个过程是怎么实现的呢？ b.cpp怎么就能找到a.cpp里面的a()函数了？ 怎么知道a()函数就在a.cpp中了？ 万一有多个a()函数咋办？ 下面一一来捋捋。

首先， 写程序的角度， 当文件b.cpp需要调用a()函数时， 只需要先声明一下该函数即可， 这是因为， 编译器在编译b.cpp的时候，会生成一个符号表， 类似a()这样看不到定义的符号就存到这个表中。 在链接阶段， 编译器就会在别的目标文件中寻找这个符号的定义， 一旦找到， 程序就可以顺利生成，否则，链接错误。

这里的两个概念: 声明和定义

定义： 某个符号完整的描述清楚， 是变量还是函数， 变量类型以及变量值是多少， 函数的参数有哪些以及返回值是什么等
声明: 仅仅告诉编译器该符号存在，至于该符号具体含义，等链接的时候才能知道
也就是说， 定义的时候需要遵循C++语法规则完整描述一个符号， 而声明时只需要给出该符号原型即可。 一个符号允许被声明多次，但只能被定义一次，否则编译器不知道应该用哪个？

基于声明和定义的不同，才有了 C++ 多文件编程的出现。试想如果有一个很常用的函数 f()，其会被程序中的很多 .cpp 文件调用，那么我们只需要在一个文件中定义此函数，然后在需要调用的这些文件中声明这个函数就可以了。

那么， 如果有几百函数， 声明该怎么办呢？ 一种简单的方法是将它们的声明全部放入一个文件中， 当需要时直接从文件中拷贝。 这种方法可行，但太麻烦， 于是乎头文件便可以发挥作用。

所谓头文件， 其实它内容和.cpp文件内容是一样的，都是C++源代码， 唯一区别是在于头文件不用被编译。 我们把所有的函数声明全部放进一个头文件， 当某个.cpp源文件需要，就#include只接将头文件内容引入到.cpp中。 这样， 当.cpp在预处理的时候， #include引入的.h文件就会替换该文件中的所有声明。

所以上面的三个问题， 其实就清楚了。 b.cpp首先会通过#include操作， 把a()函数声明内容复制过去，这样， 编译b.cpp的时候就知道有这么个符号了。 然后a.cpp和b.cpp都进行编译， 生成.o文件之后 进行链接， b.o就去a.o中找a()函数的定义。 而有多个a()函数的时候， 会进行命名空间的限制。



# 多线程编程

- 线程是在`thread`对象被定义的时候开始执行的，而不是在调用`join`函数时才执行的，调用`join`函数只是阻塞等待线程结束并回收资源。

## 锁

### std::mutex

```c++
// Compiler: MSVC 19.29.30038.1
// C++ Standard: C++17
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

int n = 0;
mutex mtx;

void count10000() {
	for (int i = 1; i <= 10000; i++) {
		mtx.lock();
		n++;
		mtx.unlock();
	}
}

int main() {
	thread th[100];
	for (thread &x : th)
		x = thread(count10000);
	for (thread &x : th)
		x.join();
	cout << n << endl;
	return 0;
}

// bool try_lock()
// 尝试将mutex上锁，如果mutex未被上锁，则将其上锁并返回true，如果mutex已被锁则返回false。
```

### std::atomic

mutex很好地解决了多线程资源争抢的问题，但它也有缺点：太慢

以上述代码为例，我们定义了100个thread，每个thread要循环10000次，每次循环都要加锁、解锁，这样固然会浪费很多的时间

```c++
// Compiler: MSVC 19.29.30038.1
// C++ Standard: C++17
#include <iostream>
#include <thread>
#include <atomic>

using namespace std;

atomic_int n = 0;  // std::atomic_int 是 std::atomic<int>的别名
// 即使是多线程，也要像同步进行一样同步操作atomic对象

void count10000() {
	for (int i = 1; i <= 10000; i++) {
		n++;
	}
}

int main() {
	thread th[100];
	for (thread &x : th)
		x = thread(count10000);
	for (thread &x : th)
		x.join();
	cout << n << endl;
	return 0;
}

```

### std::future (C++11)









# 左右值引用 (TODO)

- 左值引用

  ```c++
  int a = 10;
  int &b = a;  // 定义一个左值引用变量
  b = 20;      // 通过左值引用修改引用内存的值
  
  int &var = 10; // 编译错误, 10无法进行取地址操作，无法对一个立即数取地址
  
  const int &var = 10;
  // 相当于
  const int temp = 10; 
  const int &var = temp;
  ```

- 右值引用

  C++中的右值引用是一种引用类型，用于引用临时对象或右值。在C++11中引入，它们可以用于实现移动语义，从而提高代码的性能。

  ```c++
  int&& x = 10;
  ```

  在这个例子中，`x`是一个右值引用，指向临时对象`5`。右值引用可以看作是对临时对象的"引用"，它们只能绑定到右值，而不能绑定到左值或表达式。

  



# 其它

## const

函数前加`const`表示该函数返回值为const类型

函数后加`const`表示该函数不可以修改class的成员

```c++
// 把不改变数据成员的函数都加上const关键字进行标识，显然，可提高程序的可读性。其实，它还能提高程序的可靠性，已定义成const的成员函数，一旦企图修改数据成员的值，则编译器按错误处理。

class AA {
    int num;
public:
    AA(int a){
        num = a;
    };
    void out1() const{
        cout<<num<<endl;
    }
    void out2() const{
        num+=10; //出错，const函数不能修改其数据成员
        cout<<num<<endl;
    }
};
```





## 模板

实现泛型编程

```c++
template <typename type> func-name(parameter list)
{
   // 函数的主体
}

template <class type> func-name(parameter list)
{
   // 函数的主体
}
```



## Lambda

lambda的格式：
`[捕获列表]<模板声明>(参数列表)mutable 异常说明->类型{函数体}`

| 格式             | 内容                                                         |
| ---------------- | ------------------------------------------------------------ |
| 捕获列表         | 允许访问当前作用域下的某一个（些）变量                       |
| 模板声明         | 与普通函数的模板相同（但省略template关键字）                 |
| 参数列表（可选） | lambda函数的参数，若无参数，可以连括号一起省略（当然也可以保留，不过没必要），不能有不定参数，不能有默认参数，参数必须有名称（C++14标准允许以auto作为参数类型） |
| mutable（可选）  | 指定捕获列表以值传递的变量可以被修改，但不会改变变量本身（相当于普通函数的值传递），引用传递的变量不变（捕获列表的变量传递方式见下表） |
| 异常说明（可选） | 指定lambda可能抛出的异常，例如`throw(int)`、`noexcept`       |
| 类型（可选）     | 指定返回值类型，包括void类型（如果返回类型比较明显，可以省略，让编译器自动推断，省略时要连同箭头`->`一起省略）如果省略了参数列表的括号，那么不能指定此项 |
| 函数体           | 函数中要执行的代码，如果没有return语句且没指定类型，默认返回类型为void |


​	
捕获列表语法：

| **表达式**   | **作用**                                                     |
| ------------ | ------------------------------------------------------------ |
| []           | 什么也不捕获                                                 |
| [varname]    | 捕获**当前作用域**varname变量，**值传递**(不能修改捕获变量值) |
| [&varname]   | 捕获**当前作用域**varname变量，**引用传递**(可以修改捕获变量值) |
| [=]          | 捕获当前作用域所有变量，**值传递**                           |
| [&]          | 捕获当前作用域所有变量，**引用传递**                         |
| [=,&varname] | 捕获当前作用域所有变量，以值传递，但以引用传递varname（也可以使用[&,varname]来进行值传递） |
| [this]       | 捕获class的this指针，如果捕获列表指定了[=]或[&]，则会自动传递this（PS：不能捕获成员变量，只能捕获this指针，用this指针访问成员变量） |



# C++速度优化

## register
register修饰符暗示编译器相应的变量将被频繁地使用，如果可能的话，应将其保存在CPU的寄存器中，以加快其读写速度。

## inline

内联函数，函数的代码被放入符号表中，在使用时直接进行替换，（像宏一样展开），没有了调用的开销，效率也很高。

inline 关键字较好地解决了函数调用开销的问题。

**内联函数中的代码应该只是很简单、执行很快的几条语句。**

增加了 inline 关键字的函数称为“内联函数”。内联函数和普通函数的区别在于：当编译器处理调用内联函数的语句时，不会将该语句编译成函数调用的指令，而是直接将整个函数体的代码插人调用语句处，就像整个函数体在调用处被重写了一遍一样。

有了内联函数，就能像调用一个函数那样方便地重复使用一段代码，而不需要付出执行函数调用的额外开销。很显然，使用内联函数会使最终可执行程序的体积增加，增加空间消耗来节省时间。

