# **Java 中 Lambda 表达式的基本使用** #

## **Lambda 表达式的来由** ##

当我们使用内部类时有这样一个问题，如果内部类的实现是非常简单的，如一个接口只包含一个方法，此时使用内部类显得有点笨重且不清晰。在这种情况下，我们通常将一个功能方法作为参数传到另一个方法中。Lambda 表达式允许我们这样做，将功能方法或者代码作为数据。  

匿名类实现了如何在不给定类名的情况下实现一个基类。虽然这比一个有名字的类更简洁，但对于只有一个方法的类，匿名类又显得有点笨重。而 Lambda 表达式可以让你更简洁的去表达只有一个方法的类的实例。  

Lambda 表达式用在函数式接口中，函数式接口即只包含一个抽象方法的任意接口。（一个函数式接口可以包含一个或多个 default 方法或者 static 方法。）因为函数式接口只包含一个抽象方法，当你去实现它的时候可以省略它的方法名。用 Lambda 表达式代替一个匿名类表达式。

现在假设有一个 Calculator 类，类中有一个函数式接口 IntegerMath 和一个 operateBinary() 方法，如下：   

```.java

	interface IntegerMath {
		int operation(int a, int b);
	}

	public int operateBinary(int a, int b, IntegerMath op) {
		return op.operation(a, b);
	}

```

那么我们在使用 operateBinary() 方法时传入 IntegerMath 参数可以匿名类或者 Lambda 表达式。    

**使用匿名类** 

```.java

	calculator.operateBinary(2, 3, new IntegerMath() {
		@Override
		public int operation(int a, int b) {
			return a + b;
		}
	});

```

**使用 Lambda 表达式**

```.java

	calculator.operateBinary(2, 3, (x, y) -> x + y);

```

> 注意：Java JDK 1.8 中才可以使用 Lambda 表达式。

两种方式最后得到的结果都是一样的。可以看出使用 Lambda 表达式使程序变得更简洁。  

Java JDK 中已经定义了几个常见的函数式接口，在包 *java.util.function* 中。

## **Lambda 表达式的语法规则** ##

Lambda 表达式由以下三部分组成：  

- 一个存放参数的圆括号 **()**。
	- 有多个参数时使用逗号 “**,**” 分隔；
	- 参数类型可以省略，省略时系统会自动推断类型，如***(int age, String name)*** 和 ***(age, name)*** 是一样的； 
	- 只有一个参数时可以省略圆括号，即 ***(p)*** 和 ***p*** 是一样的；
	- 可以没有参数，即为空的圆括号 ***()***，此时圆括号不能省略。
- 箭头标识， **->** 。
- **类体**，由一个单一的表达式或者一个语句块组成。
	- 如果语句是表达式，Java 运行时会判断该表达式并返回它的值；
	- 如果是语句块，则必须使用大括号，有返回值时应该使用 return 返回。

一个简单的示例：  

```.java

public class Calculator {

	interface IntegerMath {
		int operation(int a, int b);
	}

	public int operateBinary(int a, int b, IntegerMath op) {
		return op.operation(a, b);
	} 
	
	interface CheckNumber<T> {
		boolean test(T t);
	}
	
	public <T> boolean isEven(T t, CheckNumber<T> tester) {
		return tester.test(t);
	}

	public static void main(String[] args) {
		Calculator calculator = new Calculator();

		//使用匿名类
		int sum = calculator.operateBinary(2, 3, new IntegerMath() {
			@Override
			public int operation(int a, int b) {
				return a + b;
			}
		});
		System.out.println("2 + 3 = " + sum);
		
		//使用 Lambda 表达式
		int sum2 = calculator.operateBinary(2, 3, (x, y) -> x + y);
		System.out.println("2 + 3 = " + sum2);
		
		IntegerMath addition = (int a, int b) -> a + b;
		IntegerMath subtraction = (x, y) -> x - y;

		System.out.println("40 + 2 = " + calculator.operateBinary(40, 2, addition));
		System.out.println("20 - 10 = " + calculator.operateBinary(20, 10, subtraction));
		
		//Thread
		new Thread(new Runnable() {	
					@Override
					public void run() {
						System.out.println("Hello world!");
					}
				}
			).start();
		
		new Thread(
				() -> System.out.println("Hello world!")//a single expression 
			).start();
		new Thread(
				() -> { 
					System.out.println("Hello world!");//a statement block
				}
			).start();
		
		//Single parameter, single expression, statement block
		boolean isEven = calculator.isEven(10, new CheckNumber<Integer>() {
			@Override
			public boolean test(Integer i) {
				return i % 2 == 0;
			}
		});
		System.out.println("isEven: " + isEven);
		isEven = calculator.isEven(
				10, 
				(Integer i) -> i % 2 == 0);
		System.out.println("isEven: " + isEven);
		isEven = calculator.isEven(
				10, 
				i -> i % 2 == 0);
		System.out.println("isEven: " + isEven);
		isEven = calculator.isEven(
				10, 
				i -> {
					System.out.println("test");
					return i % 2 == 0;
				}
			);
		System.out.println("isEven: " + isEven);
		
	}

}

```