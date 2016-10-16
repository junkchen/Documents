# **Java异常处理** #

在程序设计和运行的过程中，发生错误是不可避免的。尽管Java语言的设计从根本上提供了一些安全机制，使程序员尽量减少错误的产生，但是使程序被迫停止的错误的存在任然不可避免。因此，Java提供了异常处理机制帮助程序员检查可能出现的错误，保证程序的可读性和可维护性。Java将异常封装到一个类中，出现错误时就会抛出异常。  

异常是一个在程序执行期间发生的事件，它中断了正在执行的程序的正常指令流，如果不做任何处理，系统就不在执行下去而提前结束。  

为保证程序有效的执行，就需要对发生的异常进行相应的处理。在Java中，如果某个方法抛出异常，既可以在当前方法中进行捕捉，然后处理该异常，也可以将异常向上抛出，由方法调用者来处理。  

## **Java捕捉异常** ##

Java中捕捉异常的结构由**try**、**catch**和**finally**三部分组成。其中，try语句块存放的是可能发生异常的Java语句；catch程序块在try语句块之后，用来激发被捕获的异常，可对捕获到的异常进行处理；finally语句块是异常处理结构的最后执行部分，无论try语句块中的代码如何退出，都将执行finally语句块。  

```java
try {
	//程序代码快
} catch(Exceptiontype1 e) {
	//对Exceptiontype2的处理
} catch(Exceptiontype2 e) {
	//对Exceptiontype2的处理
}
......
finally {
	//程序块
}
```

### **try-catch语句块** ###

```java
public static void main(String[] args) {
	System.out.println("Junk Chen的年龄是： ");
	int age = Integer.parseInt("23L");
	System.out.println(age);
	System.out.println("program over.");
}
```

其运行结果是：  

```txt
Junk Chen的年龄是： 
Exception in thread "main" java.lang.NumberFormatException: For input string: "23L"
	at java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
	at java.lang.Integer.parseInt(Integer.java:492)
	at java.lang.Integer.parseInt(Integer.java:527)
	at com.junkchen.TryCatchFinally.main(TryCatchFinally.java:7)
```

可以看到程序只执行了一个打印，当遇到数字转换的异常时，由于没有对其进行处理，程序就停止运行了。接下来我们使用try-catch语句进行异常捕获处理在看看效果。  

```java
public static void main(String[] args) {
	try {
		System.out.println("Junk Chen的年龄是： ");
		int age = Integer.parseInt("23L");
		System.out.println(age);
	} catch (Exception e) {//对捕获到的异常进行处理
		e.printStackTrace();//打印异常信息
		System.out.println("Exception's reason: " + e.getMessage());
	}
	System.out.println("program over.");
}
```

其运行结果是：  

```txt
Junk Chen的年龄是： 
java.lang.NumberFormatException: For input string: "23L"
	at java.lang.NumberFormatException.forInputString(NumberFormatException.java:65)
	at java.lang.Integer.parseInt(Integer.java:492)
	at java.lang.Integer.parseInt(Integer.java:527)
	at com.junkchen.TryCatchFinally.main(TryCatchFinally.java:8)
Exception's reason: For input string: "23L"
program over.
```

当 **try** 代码块中的语句发生异常时，程序就会跳转到 **catch** 代码块，当执行完 **catch** 语句块之后，继续执行它后面的其他代码，而不会执行 **try** 代码块中发生异常的后面的代码。Java 的异常处理是结构化的，不会因为一个异常影响整个程序的执行。  

### **finally 语句块** ###

完整的异常处理语句一定要包含 finally 语句，无论程序中是否有异常发生，并且无论之间的 try-catch 语句块是否顺利执行完毕，都会执行 finally 语句。  

**注意：** Exception 是 try 代码块传递给 catch 代码块的变量类型， e 是变量名。通常，异常处理常用以下 3 个函数来获取异常的相关信息。
- getMessage(): 输出错误信息
- toString(): 输出异常的类型与错误信息
- printStackTrace(): 输出异常的类型、错误信息、栈层次及出现在程序中的位置

4 种特殊情况下，finally 语句不会被执行：  

- finally 语句块中发生异常
- 在前面的代码中使用了 System.exit() 退出程序
- 程序所在的线程死亡
- 关闭 CPU

## **自定义异常** ##

- 自定义异常类只需继承 Exception 类即可。  

```.java
public class CustomException extends Exception {//创建自定义异常类，继承 Exception 类
	public CustomException(String message) {
		super(message);//父类构造方法
	}
}
```

- 使用自定义异常类可分为如下几个步骤：  

1. 创建自定义异常类。
2. 在方法中通过 throw 关键字抛出异常对象。
3. 如果在当前抛出异常的方法中处理异常，可使用 try-catch 语句捕获并处理；否则在方法的声明出通过 throws 关键字指明要抛出给方法调用者的异常，继续进行下一步操作。
4. 在出现异常方法的调用中捕获并处理异常。

```.java
public static void main(String[] args) {
	try {
		System.out.println(arg(-1, 0));
	} catch (CustomException e) {
		System.out.println(e);//输出异常信息
	}
	try {
		System.out.println(arg(10, 10001));
	} catch (CustomException e) {
		e.printStackTrace();
	}
	System.out.println("Program over.");
}

/**
 * 自定义方法抛出异常
 */
public static int arg(int num1, int num2) throws CustomException {
	if (num1 < 0 || num2 < 0)// 判断方法中参数是否满足指定条件
		throw new CustomException("不能为负数");// 抛出错误信息
	if (num1 > 10000 || num2 > 10000)
		throw new CustomException("数值不能大于10000");
	return num1 + num2;
}
```

运行结果：  

```.txt
com.junkchen.CustomException: 不能为负数
com.junkchen.CustomException: 数值不能大于10000
	at com.junkchen.UseCustomException.arg(UseCustomException.java:33)
	at com.junkchen.UseCustomException.main(UseCustomException.java:13)
Program over.
```

## **在方法中抛出异常**

若在某个方法中可能会发生异常，但不想在该方法中处理这个异常，则可以使用 throws 和 throw 在方法总抛出异常。  

### **使用 throws 关键字抛出异常**

throws 关键字通常应用在方法的声明处，用来指定方法可能抛出的异常。多个异常使用逗号(,)分隔。  

```.java
public class UseThrows {
	public static void main(String[] args) {
		try {
			pop();
		} catch (NegativeArraySizeException e) {
			System.out.println("pop() 方法抛出的异常");
			e.printStackTrace();
		}
	}
	public static void pop() throws NegativeArraySizeException {
		//定义方法并抛出 NegativeArraySizeException 异常
		int[] arr = new int[-10];
	}
}
```

Run result:  

```.txt
pop() 方法抛出的异常
java.lang.NegativeArraySizeException
	at com.junkchen.UseThrows.pop(UseThrows.java:16)
	at com.junkchen.UseThrows.main(UseThrows.java:7)
```

使用 throws 关键字将异常抛给上一级后，如果不想处理该异常，可以继续向上抛出，但最终要有能处理该异常的代码。  

### **使用 throw 关键字抛出异常**

throw 关键字通常用于方法体中，并且抛出一个异常对象。程序执行到 throw 时立即终止，它后面的语句不在执行。通过 throw 抛出异常后，如果想要在上一级代码中捕获并处理异常，则需要在抛出异常的方法中使用 throws 关键字在方法的声明中指明要抛出的异常；如果要捕捉 throw 抛出的异常，则必须使用 try-catch 语句块。  

```.java
public class UseThrow {

	public static void main(String[] args) {
		try {
			int result = divisor(10, -2);
		} catch (CustomException e) {
			System.out.println(e);
		} catch (ArithmeticException e) {
			System.out.println("除数不能为0");
		} catch (Exception e) {
			System.out.println("程序发生了其他异常");
		}
	}

	public static int divisor(int x, int y) throws CustomException {
		if(y < 0)
			throw new CustomException("除数不能为负数");
		return x / y;
	}
}
```

Run result:  

```.txt
com.junkchen.CustomException: 除数不能为负数
```

由于 Exception 是所有异常类的父类，如果将 catch(Exception e) 放在其他两个代码的前面，后面的代码将永远得不到执行，也就没有什么意义了，所以 catch 语句的顺序不可调换。  

## **运行时异常**

RuntimeException 异常是程序运行过程产生的错误。Java 类库中每个包中都定义了异常类，所有类都是 Throwable 类的子类。Throwable 类派生了两个子类，分别是 Exception 和 Error 类。Error 类及其子类用来描述 Java 运行系统中的内部错误以及资源耗尽的错误。Exception 类称为非致命性类，可以通过捕捉处理是程序继续执行。  


## **异常使用原则**

Java 异常强制用户去考虑程序的强健性和安全性。主要用来捕获程序在运行时发生的异常并进行相应的处理。一些原则：  

- 尽量避免使用异常，将异常提前检测出来。
- 一个方法被覆盖时，覆盖它的方法必须抛出相同的异常或异常的子类。
- 避免总是catch Exception 或 Throwable，而要 catch 具体的异常类。这样可以使程序更加清晰。
- 不要在循环中使用 try...catch，尽量将 try...catch 放在循环外或者避免使用。
- 需要释放资源的，一定要放在 finally 里面。
- finally 中绝不使用 return 与 throw ，finally 里中如果抛出异常或者使用 return，原本应该抛出的异常会丢失。
- 如果要调用传入的参数的某个方法，一定要Null检查。或者该信息必不可少。

> **欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  
