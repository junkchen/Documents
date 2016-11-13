# **C 结构体与共用体的用法** #

## **struct(结构体)** ##

C 语言允许用户建立有不同类型数据组成的组合型的数据结构，它成为**结构体**（structure）。  

### **建立自己的结构体** ###

- 结构体的声明 
 
  **struct 结构体名**  
  **{成员列表}；**

  结构体类型的名字是由一个关键字 struct 和结构体名组合而成的。结构体名是由用户指定，又称“结构体标记”（strcuture）。  

- 各成员都应进行类型声明，即：  

  **类型名 成员名**

  成员列表也成为域表，每一个成员是结构体中的一个域。成员名命名规则与变量名相同。  
 
```.c
struct Date {  //声明一个结构体类型 struct Date
    int month;
    int day;
    int year;
};

struct Student {  //声明一个结构体类型 struct Student
    char name[20];
    char sex;
    int age;
    struct Date birthday;  //成员 birthday 属于 struct Date 类型
};
```

> **Tips:** 习惯于将结构体名、共用体名和枚举名的第 1 个字母用大写表示，以表示和系统提供的类型名相区别。

### **定义结构体类型变量** ###

1. 先声明结构体类型，在定义该类型的变量。  
```.c
struct Student student1， student2；  
```
定义了两个 Student 结构体类型的变量 student1 和 student2。

2. 在声明类型的时候同时定义变量  
```.c
struct  Student {
  char name[20];
  int age;
  char sex;
} student1, student2;
```
一般形式：
```.txt
struct 结构体名 {
    成员表列；
} 变量名表列；
```

3. 不指定类型名而直接定义结构体类型变量

一般形式：  

```.txt
struct {
    成员表列；
} 变量名表列；
```

> **说明：**（1）、只能对变量赋值、存取或运算，而不能对一个类型赋值、存取或运算。在编译时，对类型不分配空间，只对变量分配空间。（2）、结构体类型中的成员名可以与程序中的变量名相同，但二者不代表同意对象。  

### **结构体变量的初始化和引用** ###

(1)在定义结构体变量时可以对它的成员初始化。初始化列表用花括号括起来，依次赋给结构体变量各成员。  

C99 标准允许对某一成员初始化，如：  
```.c
struct Student a = {.name = "Zhang San"};
```

“.name” 隐含代表结构体变量 a 中的成员 a.name 。其他未被指定初始化的数值型成员被系统化为 0 ；字符型初始化为 '\0'，指针型初始化为 NULL。  

（2）结构体变量中的成员值引用方式为：  
**结构体变量名.成员名**  
如a.name 表示引用 a 变量的 name 成员值; “**.**” 是成员运算符，它在所有的运算符中优先级最高。  

（3）如果成员本身又是一个结构体类型，则要用若干个成员运算符，一级一级地找到最低一级的成员，只能对最低一级的成员进行赋值或存取以及运算。  
```.c
student.birthday.month  (结构体变量 student 中的成员 birthday 中的成员 month)
```

（4）对结构体成员变量可以向普通变量一样进行各种运算。
```.c
student.age++  (自加运算)
```

（5）同类结构体变量可以相互赋值。
```.c
student1 = student2;
```

（6）使用 scanf 函数输入结构体变量时，必须分别输入各成员的值，不能再 scanf 函数中使用结构体变量名一揽子输入全部成员的值。  
```.c
scanf("%d%s%c", &student.num, student.name, &student.sex);
```
注意 student.name 前没有 & 运算符，因为 name 是数组名，本身就代表地址。  

**Example:**

```.c
#include <stdio.h>

int main() {
  struct Student {
    int num;
    char name[20];
    int age;
    char sex;
  } student = {1001, "Jun Chen", 23, 'M'};
  printf("No.: %d\nname: %s\nage: %d\nsex: %c\n",
    student.num, student.name, student.age, student.sex);
  return 0;
}
```

**Run result:**

```.txt
No.: 1001
name: Jun Chen
age: 23
sex: M
```

### **使用结构体数组** ###

结构体数组与数值型的数组的不同之处在于每个数组元素都是一个结构体类型的数据，都分别包含各个成员项。  

定义结构体数组的一般形式：  

（1）
```.c
struct 结构体名 {
  成员列表
} 数组名[数组长度];
```

（2）先声明一个结构体类型，然后在用此类型定义结构体数组：  
**结构体类型 数组名[数组长度];**  

（3）对结构体数组初始化的形式是在定义数组的后面加上:  
**={初值表列};**  

**Example：**

```.c
#include <stdio.h>

int main() {
  struct Person {
    char name[20];
    int age;
  } leader[2] = {"Jun Chen", 23, "Sally", 18};
  int i = 0;
  for(; i < 2; i++)
    printf("name: %s, age: %d\n", leader[i].name, leader[i].age);
  struct Person student[3];
  student[2].name[0] = 'J';
  student[2].name[1] = 'u';
  student[2].name[2] = 'n';
  student[0].age = 100;
  printf("name: %s, age: %d\n", student[2].name, student[0].age);
  return 0;
}
```

**Run result:**

```.txt
name: Jun Chen, age: 23
name: Sally, age: 18
name: Jun, age: 100
```

### **结构体指针** ###

结构体指针就是指向结构体变量的指针，一个结构体变量的起始地址就是这个结构体变量的指针。  

#### **指向结构体变量的指针** ####

指向结构体对象的指针变量既可指向结构体变量，也可指向结构体数组中的元素。  

**Example：**
```.c
#include <stdio.h>
#include <string.h>

int main() {
  struct Student {
    char name[20];
    float score;
  };
  struct Student *p, student;
  p = &student;
  strcpy(student.name, "Jun Chen");
  student.score = 98;
  printf("name: %s, score: %f\n\n", student.name, student.score);
  printf("name: %s, score: %f\n", (*p).name, (*p).score);
  return 0;
}
```

**Run result:**
```.txt
name: Jun Chen, score: 90.000000

name: Jun Chen, score: 90.000000
```

(\*p) 表示 p 指向结构体变量， （\*p）.name 是 p 指向的结构体变量中的成员 score 。注意 \*p 两侧的括号不可省略，因为成员运算符 “**.**” 优先于 “ **\*** ” 运算符。\*p.score 等价于 \*(p.score) 。  

> **说明：** 为了方便和直观，C 语言允许把 (\*p).name 用 p->name 来代替， “->” 代表一个箭头，p->name 表示 p 指向的结构体变量中的 name 成员。“->” 称为指向运算符。  

#### **指向结构体数组的指针** ####

可以用指针变量指向结构体数组的元素。  

**Example:**

```.c
#include <stdio.h>

struct Student {
  char name[20];
  float score;
};

struct Student stu[2] = {{"Tom", 98}, {"Sindy", 100}};

int main() {
  struct Student *p;
  p = stu;
  printf("name      score\n");
  for(; p < stu+2; p++)
    printf("%-10s%3.2f\n", p -> name, p -> score);
  return 0;
}
```

**Run result:**

```.txt
name      score
Tom       98.00
Sindy     100.00
```

#### **用结构体变量和结构体变量的指针做函数参数** ####

将一个结构体变量的值传递给另一个函数，有三个方法：  

1、用结构体变量的成员作参数；（值传递）  
2、用结构体变量做实参；（值传递）  
3、用指向结构体变来那个的指针作实参，将结构体变量的地址做实参。（址传递）  

**Example:**
```.c
#include <stdio.h>
#define N 3

struct Student {
  long num;
  char name[20];
  float score[3];
  float aver;
};

int main() {
  void input(struct Student stu[]);
  struct Student max(struct Student stu[]);
  void print(struct Student);
  struct Student stu[N], *p = stu;
  input(p);
  print(max(p));
  return 0;
}

void input(struct Student stu[]) {
  int i;
  printf("Please input three students's grade:\n");
  for(i = 0; i < N; i++) {
    scanf("%ld %s %f %f %f", &stu[i].num, stu[i].name, 
      &stu[i].score[0], &stu[i].score[1], &stu[i].score[2]);
    stu[i].aver = (stu[i].score[0] + stu[i].score[1] + stu[i].score[2]) / 3.0;
  }
}

struct Student max(struct Student stu[]) {
  int i, m = 0;
  for(i = 0; i < N; i++) 
    if(stu[i].aver > stu[m].aver) m = i;
  return stu[m];
}

void print(struct Student stu) {
  printf("\nMaximum grade  student is: \n");
  printf("No.: %ld\nName: %s\nScore: %5.1f, %5.1f, %5.1f\nAverage: %6.2f\n", 
    stu.num, stu.name, stu.score[0], stu.score[1], stu.score[2], stu.aver);
}
```

**Run result:**
```.txt
Please input three students's grade:
10101 Jun 80 83 85
10102 Sam 90 93 98
10103 Tom 78 96 68

Maximum grade  student is: 
No.: 10102
Name: Sam
Score:  90.0,  93.0,  98.0
Average:  93.67
```

## **union(共用体)** ##

使几个不同的变量共享同一段内存的结构，称为“**共用体**”类型的结构。  

### **共用体的定义** ###

定义共用体类型的一般形式为：  

```.c
union 共用体名{
	成员表列;
} 变量表列;
```

**Example：**
```.c
union Data { //表示不同的变量类型 i、ch、f 可以存储到一段存储单元中
  int i;
  char ch;
  float f;
} a, b, c;
```

也可以将类型声明与变量定义分开：  
```.c
union Data {
  int i;
  char ch;
  float f;
};
union Data a, b, c;
```

共用体与结构体的定义形式相似，但其含义不同。  

结构体变量所占内存长度是各成员长度之和，每个成员分别占有自己的内存单元。而共用体变量所占的内存长度等于最长的成员长度。  

### **共用体的引用** ###

只有先定义了共用体变量才可以引用它，但应注意，不能引用共用体变量而是变量中的成员。其引用方式与结构体相似，使用成员运算符 “ **.** ”。如：  

```.c
a.i; a.ch; a.f;
```

### **共用体类型数据的特点** ###

（1）同一段内存段可以用来存放几种不同类型的成员，但在每一瞬时只能存放其中的一个成员，而不是同时存放几个。因为在同一瞬时存储单元只能有唯一的内容，即在共用体变量中只能存放一个值。  

（2）可以对共用体变量初始化，但初始化表中只能有一个常量。如：    
```.c
union Data a = {18};
union Data a = {.ch = 'M'};
```

（3）共用体变量中起作用的成员是最后一次赋值的成员，在对共用体变量中的一个成员赋值后，原有变量存储单元的值就被取代。  

（4）共用体变量的地址和它的各成员地址都是同一地址。如 &a.i, &a.ch, &a.f 都是同一值。  

（5）不能对共用体变量名赋值，也不能企图引用变量名来得到一个值。  

（6）共用体类型可以出现在结构体类型中，也可以定义共用体数组。  

**使用情况： **需要对同一段空间安排不同的用途，这时使用共用体类型比较方便。  

**Example：**
```.c
#include <stdio.h>

struct {
  int num;
  char name[10];
  char sex;
  char job;
  union {
    int clas;
    char position[10];
  } category;
} person[2];

int main() {
  int i = 0;
  for(; i < 2; i++) {
    printf("Please enter the data of person:\n");
    scanf("%d %s %c %c", &person[i].num, person[i].name,
      &person[i].sex, &person[i].job);
    if(person[i].job == 's') {
      scanf("%d", &person[i].category.clas);
    } else if(person[i].job == 't') {
      scanf("%s", person[i].category.position);
    } else printf("Input error!!!");
  }
  printf("\n");
  printf("No.  name      sex job clas/position\n");
  for(i = 0; i < 2; i++) {
    if(person[i].job == 's')
      printf("%-5d%-10s%-4c%-4c%d\n", person[i].num, person[i].name,
        person[i].sex, person[i].job, person[i].category.clas);
    else printf("%-5d%-10s%-4c%-4c%s\n", person[i].num, person[i].name,
      person[i].sex, person[i].job, person[i].category.position);
  }
  return 0;
}
```

**Run result:**
```.txt
Please enter the data of person:
101 Jason M t leader          
Please enter the data of person:
102 Sindy F s 123

No.  name      sex job clas/position
101  Jason     M   t   leader
102  Sindy     F   s   123
```

## **enum(枚举)** ##

如果一个变量只有几种可能的值，则可以定义为**枚举类型**，所谓“枚举”就是把可能的值一一列举出来，变量的值只限于列举出来的值的范围内。  

声明枚举类型用 **enum** 开头。  

**enum [枚举名] {枚举元素列表}**

```.c
enum Weekday{Sun, Mon, Tue, Wed, Thu, Fri, Sat};//枚举类型声明

enum Weekday workday, weekday;//枚举变量
```

>说明：  
>（1）C 编译对枚举类型的枚举元素按常量处理，故称**枚举常量**。  
>（2）每一个枚举元素都代表一个整数，C 语言编译按定义的顺序默认他们的值为 0， 1， 2， 3...。也可以人为地指定枚举元素的数值，在定义枚举类型时显示地指定。  
>如：  enum Weekday {sun=7, mon=1, tue, wed, thu, fri, sat}weekday;  
>指定枚举常量sun 的值为 7 ，mon 为 1， 以后顺序加 1， sat 值为 6。  
>（3）枚举元素可以用来作判断比较。

**Example：**
```.c
#include <stdio.h>

int main() {
  enum Color {
    RED, YELLOW, WHITE, BLACK, GRAY, BLUE
  };
  int i;
  for(i = 0; i < 6; i++) 
  switch(i) {
    case RED:
      printf("RED\n");
      break;
    case YELLOW:
      printf("YELLOW\n");
      break;
    case WHITE:
      printf("WHITE\n");
      break;
    case BLACK:
      printf("BLACK\n");
      break;
    case GRAY:
      printf("GRAY\n");
      break;
    case BLUE:
      printf("BLUE\n");
      break;
  }
  return 0;
}
```

**Run result:**
```.txt
RED
YELLOW
WHITE
BLACK
GRAY
BLUE
```

本博文回顾了 C 语言**结构体(struct)、共用体(union)、枚举类型(enum)**的基本用法。

> **欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  
