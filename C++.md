# C++

## error & warning

compile error: 编译器会报错 eg:漏了一个分号  
runtime error:不会报错 eg:数组越界  
warning:编译器报 warning,如除 0

## 引用&指针

指针存地址，地址指向一段内存，指针可以多级，指针定义时可以不初始化，指针可以指向 null，指针可以变  
引用就是别名(alias)，(引用只能一级)，引用定义时必须初始化，引用不能指向 null，引用不可变

### dangling pointer(or stray/wild pointer)

指针所指的内存已经被 delete，之后该指针指向的内存应当不能被访问，解决方法：delete 完就把指针置为 NULL，每次访问内存时先判断是不是指针 NULL

## 函数传参

传指针  
传引用  
传值：拷贝一个新对象

## const

## 结构体/类 对齐

编译器有一个对齐字节数，如果某个变量的大小小于对齐字节数，会填充，目的是为了 cpu 访问更快

## 类和结构体的区别

结构体成员和函数默认 public，类默认 private  
结构体继承默认 public，类默认 private  
结构体是值类型，类是引用类型  
结构体构造函数只能有参

## class

如果没有写构造函数，编译器会帮我们加一个无参的默认构造函数，如果写了（不管写的是无参还是有参），编译器就不加，子类不显时调用父类的构造函数就会报错  
**什么时候触发析构？**  
栈上的局部变量被释放，堆上的变量被手动 delete，子类释放时会自动调父类的析构  
**构造函数/析构函数可以是虚函数吗？**  
构造：不可以。虚函数会被放入虚函数表，如果构造函数是虚函数，那么调用构造函数就要去找虚函数表，但此时还没有虚函数表  
析构：可以，希望子类继承，不然的话当用父类指针指向子类对象时（多态），析构的时候会导致只有基类被释放，而子类没有

### 继承

子类永远不能访问父类的 private 对象

```c++
class B: public A{

}
```

public 继承：继承了父类的 protect 和 public 对象，且 scope 不变  
protected 继承：父类的 public 会变成 protected  
private 继承：父类的 public 对象在子类中都变成了 private，继承子类的子子类就不能访问，所以是这种继承是 selfish 的

### this 指针

每个对象都有一个 this 指针，指向该元素的起始地址

### resurrect

```c++
class B:private class A
{
    public:
    using A:field1;//即使是private继承，但是对于B的子类，依然是public
}
```

### 友元函数 friend

定义在类外部，可以访问 private 和 protected 变量的函数，不是类的成员函数

```c++
class A{
    friend void print(A a);
};
void print(A a){
    ...
}
```

友元也可以是类，叫友元类，整个类的成员都是友元

```c++
class A{
    friend class B;
}
```

### 虚函数

虚函数表中记录的是函数指针（如果是继承并重写了就记录重载后的函数指针，如果没有继承重写就记录父类的该函数指针），虚函数表位于每个类对象实例最前面的位置，保证性能，调用虚函数就是找到对应的函数指针

### 纯虚函数

有纯虚函数的类就是抽象类，抽象类不能实例化

```c++
    virtual void f() = 0;
```

只有纯虚函数且没有成员变量的抽象类可以作为接口（c++本身没有接口这个概念）

### final

final 类不能被继承
final 方法不能被继承重写

```c++
virtual void func() const final{} // 合法，但仍然是final的
```

### const 成员函数

const 成员函数不能对类的对象进行修改

### 多态 动态绑定/静态绑定

什么是多态：可以用基类指针或引用去调用子类的方法
静态：没有 virtual 修饰的，编译器靠指针或引用的类型来决定调用的函数
动态：有 virtual 修饰的，执行时根据虚函数表来决定（需要占用更多内存空间作为代价）

## static/dynamic cast

static：compile time 的转换  
dynamic：运行时转换

```c++
Derived d;
Base* objBase = &d; // ok 向上转换不需要cast

Base b;
Derived* p_d = static_cast<Derived*>(b);//可以转，但是在运行时会遇到unexpected的结果
Derived* p_d  = dynamic_cast<Derived*>(&b);//会失败

Base* p_b = new Derived();
Derived* p_d = dynamic_cast<Derived*>(p_b);//成功
```

## STL

vector:底层是数组
unordered_map:底层是 hashtable
map:底层是红黑树
unordered_set:底层是 hashtable
set:底层是红黑树

# 面试题

## static 关键字

1. 在函数内部的静态局部变量(作用域：函数内部)：只初始化一次，直到程序结束销毁，保留函数调用间的状态。
2. 静态全局变量：仅当前文件可访问
3. static 方法：仅限当前文件访问
4. static 静态成员变量：属于类本身，所有对象共享，可通过类名访问`MyClass::count`
5. 静态成员函数：没有 this 指针，不可访问非静态成员，通过类名直接调用 `MyClass::showCount()`

## const 关键字

定义常量 `const int maxSize = 100;`

指针（左定值，右定向）

```c++
const int* i;//指针所指向的内存中的值不可变，（叫做指向const变量的指针
int* const i;//指针不能指向别的地方吗，（叫做const指针
const int* const i;//指针不能乱指，值也不能变
```

修饰函数参数，防止被修改  
修饰函数返回值，返回只读，不可被修改  
修饰成员函数，表示函数不修改对象状态

### constexpr

让函数的值在编译器就确定

1. 修饰变量：编译期常量，比 const 更严格，const 允许运行期常量
2. 修饰函数：如果传入变量，就运行期执行，如果传入常量，就编译期确定
3. 修饰构造函数：编译期就创建对象（可能存在静态区或者直接嵌入指令）

## 引用折叠

## malloc 和 new 的区别

```c++
// 使用 malloc
int* p1 = (int*)malloc(sizeof(int));
*p1 = 42;
free(p1);

// 使用 new
int* p2 = new int(42);
delete p2;

// malloc 不调用构造函数
MyClass* obj1 = (MyClass*)malloc(sizeof(MyClass)); // 构造函数不会执行！

// new 会调用构造函数
MyClass* obj2 = new MyClass(); // 构造函数自动执行
```

malloc 返回 void\*，需强转，对于对象不会调用构造函数，不会初始化(可以是任意值)，通过 free 释放，对于数组需要手动计算大小，不可重载
new 返回对象指针，对于对象会调用构造函数，自动初始化(基本类型为 0)，通过 delete 释放，直接指明数组长度，可重载

## inline 和普通函数的区别

inline 函数，编译器可能直接将函数体放入代码调用处，可以减少函数调用开销，不需要入栈传参等，但是是编译器决定的，可能会忽略，也会增大可执行文件体积。
inline 是用于实现的关键字，类中定义的成员函数默认 inline，但是必须在实现的时候加上 inline 关键字。
递归函数不能内联。

## 内存对齐

底层机制，决定了变量或者结构体成员在内存中的位置是否需要满足地址对齐要求。
不对齐会导致 CPU 需要多次执行内存访问，某些平台会抛异常。
每个成员的地址必须是其大小或者编译器指定对齐数的倍数
结构体总大小必须是最大成员对齐数的倍数
编译器会插入 padding 满足对齐
控制对齐方法 #pragma pack(n) 或 c++11 里面的 alignas alignof

```c++
#pragma pack(1)  // 强制按 1 字节对齐
struct Packed {
    char a;
    int b;
};

struct alignas(8) AlignedStruct {
    char a;
    int b;
};

std::cout << alignof(AlignedStruct);  // 输出 8
```

## mmap memory map

将磁盘上的文件内容映射进进程的虚拟内存空间，不需要使用 read() write()。必须以页为单位映射
这使得多个进程可访问同一块物理内存，
写入后需调用 msync() 保证数据落盘

## 指针 vs 引用

指针：是一个变量，占用地址空间，存另一个变量的地址，sizeof 就是指针的大小 8B，可不初始化，可为空
引用：引用本身不出现在符号表中，编译器将引用替换为原变量的地址。sizeof 就是原变量大小，必须初始化，不可为空，不可更改，不可多级引用

## 浅拷贝 vs 深拷贝

当类中包含指针或动态资源时，浅拷贝只拷贝指针，同时指向一块资源，深拷贝会复制内容
浅拷贝会导致悬空指针，重复释放，且是编译器自动生成的，深拷贝需实现拷贝构造函数
**什么时候调用拷贝构造函数？**
以拷贝的方式初始化对象，编译器默认生成浅拷贝的拷贝构造函数

```c++
Student stu1("Xiao Ming", 18);  // 调用普通构造函数
Student stu3 = stu1;  // 调用拷贝构造函数
stu3 = stu2;  //调用operator=()

Student stu4(stu1);   // 调用拷贝构造函数

Student stu5; // 调用普通构造函数
stu5 = stu2;  //调用operator=()

void func(Student s){
    //TODO:
}
Student stu1("Xiao Ming", 18);  // 普通初始化
func(stu1);  //以拷贝的方式初始化

Student func(){
   Student stu1("Xiao Ming", 18);  // 普通初始化
    return stu1;
}
Student stu = func(); // 为了防止对象被销毁，编译器用拷贝构造函数生成临时对象，然后返回的是临时对象

```

## 智能指针

封装了原始指针的类，自动管理动态内存资源。智能指针对象创建时，自动获取资源，离开作用域时自动释放，防止悬空、内存泄漏
unique_ptr 独占所有权，不能复制，只能移动，析构自动调用 delete

```c++
#include <memory>
std::unique_ptr<int> p1 = std::make_unique<int>(42);
// std::unique_ptr<int> p2 = p1; ❌ 不允许复制
std::unique_ptr<int> p2 = std::move(p1); // ✅ 所有权转移
```

shared_ptr 共享所有权，引用计数管理资源，适合多个对象共享资源

```c++
#include <memory>
std::shared_ptr<int> sp1 = std::make_shared<int>(100);
std::shared_ptr<int> sp2 = sp1; // 引用计数 +1

```

weak_ptr 弱引用，不增加引用计数，打破 shared_ptr 循环引用。通过 lock 方法确定资源是否被释放，被释放就不应该访问

```c++
struct A;
struct B;

struct A {
    std::shared_ptr<B> b_ptr;
};

struct B {
    std::weak_ptr<A> a_ptr; // 不增加引用计数
};

```

控制块：负责管理 shared，weakptr 的生命周期，创建一个 sharedptr 就会创建一个控制块，拷贝 sharedptr 就让强引用+1，销毁一个就-1，强引用变 0 时，调用删除器释放资源，但是控制块仍然存在直到弱引用也变为 0

## RAII

## 左值，右值，左值引用，右值引用

左：可出现在赋值号左边，表示有持久地址的对象，能取地址
右：只能出现在赋值号右边，不可取地址

左值引用&：只能绑定左值，，用于避免拷贝
右值引用&&：只能绑定右值，用于实现移动语义，避免拷贝，可用于延长右值生命周期

## move 方法

将一个对象显示转换为右值引用，触发移动语义，避免拷贝。不是移动，而是允许移动，告诉编译器不需要这个对象的原始值了，触发移动构造函数和移动赋值运算。

```c++
std::string a = "hello";
std::string b = a;              // 拷贝构造：复制字符串内容
std::string c = std::move(a);   // 移动构造：转移 a 的资源，a 变为空

std::string createMessage() {
    std::string msg = "Hi there!";
    return std::move(msg);  // 显式触发移动构造（但 C++17 起编译器自动优化）
}

std::vector<std::string> vec;
std::string s = "data";
vec.push_back(std::move(s));  // 避免拷贝，直接移动资源

class MyClass {
    std::string name;
public:
    MyClass(std::string&& n) : name(std::move(n)) {}  // 移动构造
};

```

对 const 对象使用 move 不会触发移动构造，因为移动构造需要修改源对象

## 构造函数 拷贝构造函数 移动构造函数 析构函数

默认构造函数：一个构造函数都没有，编译器才会自动加一个默认的构造函数
默认拷贝构造函数：没有定义移动构造函数和移动赋值，编译器才会自动加一个拷贝构造函数，但是是浅拷贝
默认移动构造函数：没有定义拷贝构造和析构，且所有成员都支持移动
析构函数：生命周期结束或 delete 时调用

**构造函数和析构函数可以是虚函数吗**
构造：不行，因为对象没构造完成时还没有虚函数表，构造函数是静态绑定的，明确知道要构造哪个类，不用多态
析构：行，且有继承时，必须是。析构是生命周期最后一步，可能通过基类指针来触发。如果不是虚函数，delete 只会调用基类的析构，不会调用派生类的析构，造成内存泄漏

## 拷贝构造函数

拷贝构造函数用于创建一个新对象，并复制已有对象的内容。
使用场景：

```c++
// ✅ 1. 用已有对象初始化新对象（左值）
MyClass a;
MyClass b = a;        // 拷贝构造
MyClass c(a);         // 拷贝构造

// ✅ 2. 函数参数按值传递（非引用）
void func(MyClass obj);  // 参数是值类型
func(a);                 // 拷贝构造

//  ✅ 3. 函数返回值赋给新对象（如果没有移动构造）
MyClass create() {
    MyClass temp;
    return temp;         // 拷贝构造（如果没有移动构造）
}
MyClass result = create();  // 拷贝构造（或优化）

// ✅ 4. 容器插入已有对象
std::vector<MyClass> vec;
MyClass a;
vec.push_back(a);        // 拷贝构造


```

## 移动构造函数

c++11 引入的新的特殊构造函数，用于移动语义，可以避免深拷贝，提升性能，支持 stl 的高效操作如 vector 的 emplaceback
使用场景：

```c++
// ✅ 1. 临时对象初始化新对象
std::string s = std::string("hello");  // 临时对象触发移动构造
// ✅ 2. 函数返回值赋给新对象
std::string create() {
    std::string temp = "data";
    return temp;  // 返回右值
}
std::string s = create();  // 移动构造（可能被优化）
// ✅ 3. 容器插入临时对象
std::vector<std::string> vec;
vec.push_back(std::string("hi"));  // 移动构造
// ✅ 4. 显式使用 std::move 初始化新对象
std::string a = "hello";
std::string b = std::move(a);  // 移动构造
```

```c++
class Buffer {
public:
    char* data;
    size_t size;

    Buffer(size_t s) : size(s) {
        data = new char[size];
        std::cout << "构造函数\n";
    }

    // 拷贝构造函数（深拷贝）
    Buffer(const Buffer& other) : size(other.size) {
        data = new char[size];
        std::copy(other.data, other.data + size, data);
        std::cout << "拷贝构造函数\n";
    }

    // 移动构造函数（资源转移）
    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
        std::cout << "移动构造函数\n";
    }

    ~Buffer() {
        delete[] data;
    }
};
Buffer b1(1024);
Buffer b2 = std::move(b1);  // 触发移动构造函数

```

如果你定义了移动构造函数，建议也定义移动赋值运算符

## 移动赋值运算符

c++11 引入的特殊成员函数，用于将一个对象的资源转移给另一个对象，而不是复制。参数是右值引用，返回值是 MyClass&，先释放当前，转移 other，置空 other  
使用场景：

```c++
// ✅ 1. 已有对象接收右值对象的资源
std::string a = "hello";
std::string b = "world";
b = std::move(a);  // 移动赋值：b 已存在，接收 a 的资源

// ✅ 2. 函数返回值赋给已有对象
std::string create() {
    std::string temp = "data";
    return temp;
}

std::string s;
s = create();  // 移动赋值（如果 s 已存在）

// ✅ 3. 容器元素替换
std::vector<std::string> vec(3);
vec[0] = std::move(vec[1]);  // 移动赋值



```

```c++
class MyClass {
public:
    MyClass& operator=(MyClass&& other);  // 移动赋值运算符
};

```

## RVO 返回值优化

直接在调用者的内存空间构造对象(没有 RVO 会拷贝或移动构造，有 RVO 直接构造)，分为有名`return obj;`或匿名`return MyClass();`
c++11 和 c++14 中，编译器可以选择用 RVO，可以手动禁止`-fno-elide-constructors`。从 c++17 开始，如果是纯右值，强制 RVO
RVO 失效场景：

```c++
// ❌ 场景一：多个返回语句
MyClass create(bool flag) {
    MyClass a(1);
    MyClass b(2);
    if (flag)
        return a;
    else
        return b;  // RVO 可能失效
}

// ❌ 场景二：异常处理存在
MyClass create() {
    MyClass obj;
    try {
        // 可能抛出异常
    } catch (...) {
        // 异常处理
    }
    return obj;  // RVO 可能失效
}

// ❌ 场景三：对象具有可观察副作用
struct MyClass {
    MyClass() { std::cout << "构造\n"; }
    MyClass(const MyClass&) { std::cout << "拷贝构造\n"; }
    ~MyClass() { std::cout << "析构\n"; }
};

MyClass create() {
    MyClass obj;
    return obj;  // RVO 可能失效，为保留副作用
}



```

## 多继承 有什么问题 如何解决 虚继承 菱形继承 多成员重名

多继承：一个类可以同时继承多个基类

**命名冲突**
多个基类有同名成员，派生类无法直接访问，需要指定作用域

```c++
class A { public: void show(); };
class B { public: void show(); };

class C : public A, public B {
    // C::show() 会产生二义性
};

C obj;
// obj.show(); ❌ 编译错误
obj.A::show(); ✅ 明确调用 A 的版本

```

**菱形继承**
两个基类都继承自同一个祖先类，而派生类又继承这两个基类，会导致祖先类的成员被继承两份。
解决方案：虚继承，所有派生类共享同一份基类成员，避免重复继承。最底层的派生类负责构造虚基类

```c++
class A { public: int val; };
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};  // D 中只有一份 A::val

```

虚继承会改变内存布局，增加了 vbptr 虚基类指针，指向虚基类表 vbtable，通过虚基类表定位虚基类成员。虚基类成员存储在对象末尾或共享区

## 虚函数 纯虚函数 虚函数表

虚函数：用 virtual 修饰的成员函数，允许通过基类指针或引用调用派生类的重写版本，实现动态绑定
纯虚函数：没有函数体，`virtual void speak() = 0;`,包含纯虚函数的类叫抽象类，不能实例化，派生类必须实现所有纯虚函数，才能被实例化
底层实现：vtable+vptr
虚函数表：每个类都有一个虚函数表，是一个函数指针数组，表中存储了该类所有虚函数的地址，如果派生类重写了，就会替换成派生类的函数地址
虚表指针 vptr：每个对象都有一个隐藏的的指针成员，指向所属类的虚函数表，通过基类指针调用虚函数时，会先读 vptr 找到 vtable，在调用表中对应位置的函数地址
`c++`

```

```

## 内存管理 代码区 常量区 全局/静态区 data+bss 栈区 堆

代码区：存编译后的指令
常量区 read only data：存字符串常量或 const 修饰的全局常量
全局/静态区 data+bss segment：全局变量，静态变量
栈：函数参数，局部变量，返回地址。每次函数调用都创建栈帧，后进先出，自动释放，但空间有限（会栈溢出）
堆：动态分配的内存

```
高地址
│
│  栈区（局部变量、函数调用，高地址往低地址增长）
│
│  堆区（动态分配）
│
│  BSS段（未初始化静态/全局变量/初始化为0的全局变量-编译器优化）
│
│  数据段（已初始化静态/全局变量）
│
│  常量区（字符串字面量、const常量）
│
│  代码区（程序指令）
│
低地址

```

## 函数调用过程

调用时：

1. 参数压栈（从右往左，也可能被编译器优化，把参数存在寄存器）
2. 返回地址压栈
3. 保存 rbp：旧的栈指针压栈，用于恢复
4. 设置新帧指针
5. 分配局部变量空间

返回时：

1. 返回值存入寄存器 eax
2. 恢复旧帧地址
3. 跳转回返回地址
4. 清理参数空间

## 程序启动过程
