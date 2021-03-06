# **指针基础概念** #

指针是 C 语言的精华，正确灵活地运用，可使程序简洁、紧凑、高效。  

## **指针是什么** ##

“指针”即地址，一个变量的地址称为该变量的“**指针**”。  

如果有一个变量专门用来存放另一变量的地址（即指针），则称它为“**指针变量**”。指针变量就是地址变量，用来存放地址，**指针变量的值是地址**（即指针）。  

指针是一个地址，而指针变量是存放地址的变量。  

## **指针变量** ##

存放地址的变量是指针变量。  

### **如何定义指针变量** ###

定义指针变量一般形式为：  

**类型名 \* 指针变量名**

如：  

```c
int *pointer_1, *pointer_2;  
```

左端的 int 是在定义指针变量时必须指定的**“基类型”**。指针变量的基类型用来指定此指针变量可以指向的变量的类型。  

> **说明**  
（1）指针变量前面的 “*” 表示该变量的基类型为指针变量。  
（2）在定义指针变量时必须指定基类型。  
（3）指向整型数据的指针类型表示为 “int \*” ，读作 “指向 int 的指针” 或简称 “int 指针”。可以有 int \* , char \*, float \* 等指针类型。  
（4）指针变量中只能存放地址（指针），不要将一个整数赋给一个指针变量。  

### **应用指针变量** ###

在应用指针变量时，可能有 3 种情况。  

1、给指针变量赋值。  

```c
p = &a；  //把 a 的地址赋给指针变量 p
```

2、应用指针变量指向的变量。  

如果已执行 “p = &a” ,即指针变量 p 指向了整型变量 a ，则：  

```c
printf("%d", *p);
```

其作用是以整数形式输出指针变量 p 所指向的变量的值，即变量 a 的值。  

```c
*p = 1;
```

表示将整数 1 赋给 p 当前所指向的变量，如果 p 指向变量 a ，则相当于把 1 赋给 a ，即 “a = 1;”。  

3、引用指针变量的值。  

```c
printf("%o", p);
```

作用是以八进制数形式输出指针变量 p 的值，如果 p 指向了 a，就是输出 a 的地址，即 &a 。  

> **注意**  
（1） & 取地址运算符， &a 是变量 a 的地址。   
（2） * 指针运算符（或称 “间接访问” 运算符）， *p 代表指针变量 p 指向的对象。  

实例展示：  

```c
#include <stdio.h>

int main() {
  int a = 100, b = 10;
  int *pointer_1, *pointer_2;
  pointer_1 = &a, pointer_2 = &b;
  printf("a = %d, b = %d\n", a, b);
  printf("*pointer_1 = %d, *pointer_2 = %d\n", *pointer_1, *pointer_2);
  return 0;
}
```

运行结果为：  

```txt
a = 100, b = 10
*pointer_1 = 100, *pointer_2 = 10
```

## **指针变量作为函数参数** ##

函数的参数不仅仅可以是整型、浮点型、字符型等数据，还可以是指针类型。它的作用是将一个变量的地址传送到另一个函数中。  

示例代码：  

```c
#include <stdio.h>

int main() {
  void swap(int *p1, int *p2);
  int a, b;
  int *pointer_1, *pointer_2;
  printf("Please enter a and b(format: a, b): ");
  scanf("%d, %d", &a, &b);
  pointer_1 = &a, pointer_2 = &b;
  if(a < b) swap(pointer_1, pointer_2);
  printf("max = %d, min = %d\n", a, b);
  return 0;
}

void swap(int *p1, int *p2) {
  int temp;
  temp = *p1;
  *p1 = *p2;
  *p2 = temp;
}
```

运行结果：  

```txt
Please enter a and b(format: a, b): 25, 66
max = 66, min = 25
```

swap 是用户自定义函数，它的作用是交换两个变量（ a 和 b ）的值。swap 函数的两个形参 p1 和 p2 是指针变量。  

用指针变量作为函数参数，在函数执行过程中使指针变量所指向的变量值发生变化，函数调用结束后，这些变量值得变化依然保留下来。  

通过调用函数使变量的值发生变化，在主调函数中可以使用这些改变了的值。  

不能企图通过改变指针形参的值而使指针实参的值改变。  

C 语言中实参变量和形参变量之间的数据传递时单向的“值传递”方式。用指针变量做函数参数时同样要遵循这一规则。不可能通过执行调用函数来改变实参变量的值，但是可以改变实参指针变量所指变量的值。  

**注意:** 函数的调用可以（而且只可以）得到一个返回值（即函数值），而使用指针变量作参数，可以得到多个变化了的值。  

> **欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  
