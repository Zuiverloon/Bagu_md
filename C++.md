# C++

## error & warning

compile error: 编译器会报错 eg:漏了一个分号  
runtime error:不会报错 eg:数组越界  
warning:编译器报 warning,如除 0

## 引用&指针

指针存地址，地址指向一段内存，指针可以多级，指针定义时可以不初始化，指针可以指向 null，指针可以变  
引用就是别名(alias)，引用只能一级，引用定义时必须初始化，引用不能指向 null，引用不可变

## 函数传参

传指针  
传引用  
传值：拷贝一个新对象

## 结构体/类 对齐

编译器有一个对齐字节数，如果某个变量的大小小于对齐字节数，会填充，目的是为了 cpu 访问更快

## class

如果没有写构造函数，编译器会帮我们加一个无参的默认构造函数，如果写了（不管写的是无参还是有参），编译器就不加，子类不显时调用父类的构造函数就会报错  
**什么时候触发析构？**  
栈上的局部变量被释放，堆上的变量被手动 delete，子类释放时会自动调父类的析构  
**构造函数/析构函数可以是虚函数吗？**  
构造：不可以。虚函数会被放入虚函数表，如果构造函数是虚函数，那么调用构造函数就要去找虚函数表，但此时还没有虚函数表
析构：可以，希望子类继承，不然的话当用父类指针指向子类对象时（多态），析构的时候会导致只有基类被释放，而子类没有

## 友元函数 friend

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

## 虚函数

虚函数的指针会被放进虚函数表，虚函数表位于每个类对象实例最前面的位置，保证性能，调用虚函数就是遍历这张表

## 继承

子类永远不能访问父类的 private 对象

```c++
class B: public A{

}
```

public 继承：继承了父类的 protect 和 public 对象，且 scope 不变  
protected 继承：父类的 public 会变成 protected  
private 继承：父类的 public 对象在子类中都变成了 private，继承子类的子子类就不能访问，所以是这种继承是 selfish 的

### resurrect

```c++
class B:private class A
{
    public:
    using A:field1;//即使是private继承，但是对于B的子类，依然是public
}
```

## STL

vector:底层是数组
unordered_map:底层是 hashtable
map:底层是红黑树
unordered_set:底层是 hashtable
set:底层是红黑树
