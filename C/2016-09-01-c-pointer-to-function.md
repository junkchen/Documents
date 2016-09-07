# **指向函数的指针** #

## **函数指针** ##

如果在程序中定义了一个函数，在编译时，编译系统为函数代码分配一段存储空间，这段**存储空间的起始地址（又称入口地址）称为这个函数的指针**。  

如：  

```c
int (*p)(int, int);
```

定义 p 是一个指向函数的指针变量，它可以指向函数的类型为整型且有两个整型参数的函数。p 的类型用 int(*)(int, int)表示。  

## **用函数指针变量调用函数** ##

调用一个函数，除了可以通过函数名调用外，还可以通过指向函数的指针变量来调用该函数。  

**通过指针变量访问它所指向的函数**  

示例：  

```c
#include <stdio.h>

int main() {
  int max(int, int);
  int (*p)(int, int);
  int a, b, c;
  p = max;
  printf("Please enter a and b(a,b): ");
  scanf("%d,%d", &a, &b);
  c = (*p)(a, b);
  printf("a = %d, b = %d\nmax = %d\n", a, b, c);
  return 0;
}

int max(int x, int y) {
  int z;
  if(x < y) z = y;
  else z = x;
  return z;
}
```

运行结果：  

```txt
Please enter a and b(a,b): 78,65
a = 78, b = 65
max = 78
```

> **注意：** *p 两侧的括号不可省略，表示 p 先与 * 结合，是指针变量，然后再与后面的 () 结合， () 表示函数，即该指针变量不是指向一般的变量，而是指向函数。  


## **定义和使用指向函数的指针变量** ##

定义指向函数的指针的一般形式为：  

**类型名 （\* 指针变量名）（函数参数列表）；**

**说明：**  

- 由于优先级关系，“ * 指针变量名 ”要用圆括号括起来。  

- 要用指针调用函数，必须先使指针变量指向该函数。  

- 给函数指针变量赋值时，只需给出函数名而不必给出参数。

- 用函数指针变量调用函数时，只需将 （*p） 代替函数名即可（p 为指针变量名），在 (*p) 之后的括号中根据需要写上实参。如： c = (*p)(a, b);

- 对指向函数的指针变量不能进行算术运算。  

- 用函数名调用函数，只能调用指定的一个函数，而通过指针变量调用函数比较灵活，可以根据不同情况先后调用不同的函数。

Sample：  

```c
#include <stdio.h>

int main() {
  int max(int, int);
  int min(int, int);
  int (*p)(int, int);
  int a, b, c, n;
  printf("please enter a and b(fromat: a,b): ");
  scanf("%d,%d", &a, &b);
  printf("please choose 1 or 2: ");
  scanf("%d", &n);
  if(n == 1) p = max;
  else if(n == 2) p = min;
  c = (*p)(a, b);
  printf("a = %d, b = %d\n", a, b);
  if(n == 1) printf("max = %d\n", c);
  else printf("min = %d\n", c);
  return 0;
}

int max(int x, int y) {
  if(x > y) return x;
  else return y;
}

int min(int x, int y) {
  if(x < y) return x;
  else return y;
}
```

**Run result**：  

```txt
please enter a and b(fromat: a,b): 23,65
please choose 1 or 2: 1
a = 23, b = 65
max = 65

please enter a and b(fromat: a,b): 23,65
please choose 1 or 2: 2
a = 23, b = 65
min = 23
```

## **用指向函数的指针作函数参数** ##

指向函数的指针变量的一个重要用途是吧函数的地址作为参数传递到其它的的函数。  

使用指向函数的指针作为函数的参数，在调用时只需要将函数名作为实参即可。  

**Sample：**

```c
#include <stdio.h>

int main() {
  void fun(int x, int y, int (*p)(int, int));
  int max(int x, int y);
  int min(int x, int y);
  int add(int x, int y);
  int a = 50, b = 24, n;
  printf("Please choose 1, 2 or 3: ");
  scanf("%d", &n);
  if(n == 1) fun(a, b, max);
  else if(n == 2) fun(a, b, min);
  else if(n == 3) fun(a, b, add);
  return 0;
}

void fun(int x, int y, int (*p)(int, int)) {
  int result;
  result = (*p)(x, y);
  printf("%d\n", result);
}

int max(int x, int y) {
  int z;
  if(x > y) z = x;
  else z = y;
  printf("max = ");
  return z;
}
int min(int x, int y) {
  int z;
  if(x < y) z = x;
  else z = y;
  printf("min = ");
  return z;
}

int add(int x, int y) {
  printf("sum = ");
  return x + y;
}
```

**Run result：**  

```txt
Please choose 1, 2 or 3: 1
max = 50

Please choose 1, 2 or 3: 2
min = 24

Please choose 1, 2 or 3: 3
sum = 74
```

# **返回指针值的函数** #

一个函数的返回值可以是整型值、字符值、实型值等，也可以返回指针型的数据，即地址。  

定义返回指针值的函数的一般形式为：  

**类型名  * 函数名 （参数列表）**  

**Sample：**  

```c
#include <stdio.h>

int main() {
  float *search(float (*pointer)[4], int n); 
  float score[][4] = {{60,70,80,90}, {56,89,67,88}, {34,78,90,66}};
  int i, k;
  float *p; 
  printf("Input the number of student: ");
  scanf("%d", &k);
  printf("The scores of No. %d are: \n", k); 
  p = search(score, k - 1);   
  for(i = 0; i < 4; i ++) {
    printf("%3.2f\t", *(p++));
    //p++;
  }
  printf("\n");
  return 0;
}

float *search(float (*pointer)[4], int n) {
  float *pt;
  pt = *(pointer + n); 
  return pt; 
}
```

**Run result:**  

```txt
Input the number of student: 2
The scores of No. 2 are: 
56.00	89.00	67.00	88.00	
```

> **欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  
