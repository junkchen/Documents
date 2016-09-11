# **指针数组和多种指针** #

## **什么是指针数组** ##

一个数组，若其元素均为指针类型数组，称为**指针数组**，指针数组中的每一个元素都存放一个地址，相当于一个指针变量。  

定义一维数组指针的一般形式：  

**类型名 \* 数组名[数组长度]；**  

[] 比 \* 优先级高。  

指针数组比较适合用来指向若干字符串，是字符串处理更加灵活方便。  

**Sample:**

```c
#include <stdio.h>
#include <string.h>

int main() {
  void sort(char *name[], int n);
  void print(char *name[], int n);
  char *name[] = {"Follow me", "BASIC", "Great Wall", "FORTRAN", "Computer design"};
  sort(name, 5);
  print(name, 5);
  return 0;
}

void sort(char *name[], int n) {
  char *temp;
  int i, j, k;
  for(i = 0; i < n - 1; i++) {
    k = i;
    for(j = i + 1; j < n; j++)
      if(strcmp(name[k], name[j]) > 0) k = j;
    if(k != i) {
      temp = name[i];
      name[i] = name[k];
      name[k] = temp;
    }
  }
}

void print(char *name[], int n) {
  int i;
  for(i = 0; i < n; i++)
    printf("%s\n", name[i]);
}
```

**Run result:**

```txt
BASIC
Computer design
FORTRAN
Follow me
Great Wall
```

## **指向指针数据的指针** ##

定义指向指针数据的指针：  

**char  \*\*p**  

p 前面有两个 \* 号。\* 运算符的结合性是从右到左的。因此 \*\*p 相当于 \*(*p) ， \*p 是指针变量的定义形式。可以分为两部分看： char \* 和 (\*p) ，后面的 (\*p) 表示 p 是指针变量，前面的 char \* 表示 p 是指向 char \* 型的数据。也就是说， p 指向一个字符型指针变量（这个字符型指针变量指向一个字符型数据）。如果引用 \*p , 就得到 p 所指向的字符指针变量的值。  

**Sample：**  

```c
#include <stdio.h>

int main() {
  char *name[] = {"Follow me","BASIC","Great Wall","FORTRAN","Computer design"};
  char **p;
  int i;
  for(i = 0; i < 5; i++) {
    p = name + i;
    printf("%s\n", *p);
  }
  return 0;
}
```

**Run result:**  

```txt
Follow me
BASIC
Great Wall
FORTRAN
Computer design
```

**程序分析：** p 是指向 char \* 型数据的指针变量，即指向指针的指针。第一次执行 p = name + i 时，p 指向 name 数组的 0 号元素 name[0] ， \*p 是 name[0] 的值，即第一个字符串首字符的地址。  

指针数组的元素也可以不指向字符串，而指向整型或实型数据。如下面这样：  

**Sample：**  

```c
#include <stdio.h>

int main() {
  int a[] = {1, 3, 5, 7, 9};
  int *num[5] = {&a[0], &a[1], &a[2], &a[3], &a[4]};
  int **p, i;
  for(i = 0; i < 5; i++) {
    p = num + i;
    printf("%d ", **p);
  }
  printf("\n");
  return 0;
}
```

**Run result:**  

```txt
1 3 5 7 9
```

**程序分析：** num 是指针数组， p 是指向指针型数据的指针变量，name[0] 是 num 的首元素，是指针型元素，指向整型数组 a 的首元素 a[0] 。开始时 p 的值是 &num[0], \*p 是 num[0] 的值，即 &a[0], \*(\*p) 是 a[0] 的值。  

指针数组的元素只能存放地址，不能存放整数。  

利用指针变量访问另一个变量就是“间接访问”。如果一个指针变量中存放一个目标变量的地址，这就是“单级间址”。指向指针数据的指针是“二级间址”。间址的方法可以延伸到更多的级，即多重指针，但实际上在程序中很少超过二级间址，级数越多越难理解，易产生混乱，出错机会多。  

