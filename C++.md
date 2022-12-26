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

## 结构体对齐

编译器有一个对齐字节数，如果某个变量的大小小于对齐字节数，会填充，目的是为了 cpu 访问更快

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
