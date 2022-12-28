# C++

## error & warning

compile error: 编译器会报错 eg:漏了一个分号  
runtime error:不会报错 eg:数组越界  
warning:编译器报 warning,如除 0

## 引用&指针

指针存地址，地址指向一段内存，指针可以多级，指针定义时可以不初始化，指针可以指向 null，指针可以变  
引用就是别名(alias)，引用只能一级，引用定义时必须初始化，引用不能指向 null，引用不可变

### dangling pointer(or stray/wild pointer)

指针所指的内存已经被 delete，之后该指针指向的内存应当不能被访问，解决方法：delete 完就把指针置为 NULL，每次访问内存时先判断是不是指针 NULL

## 函数传参

传指针  
传引用  
传值：拷贝一个新对象

## const

```c++
const int* i;//指针所指向的内存中的值不可变，（叫做指向const变量的指针
int* const i;//指针不能指向别的地方吗，（叫做const指针
const int* const i;//指针不能乱指，值也不能变

//Keyword constexpr allows function-like declaration of constants:
constexpr double GetPi() {return 22.0 / 7;}

//如果是引用
int original = 30;
const int& constRef = original;
constRef = 40; // Not allowed: constRef can’t change value in original
int& ref2 = constRef; // Not allowed: ref2 is not const
const int& constRef2 = constRef; // OK
```

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
