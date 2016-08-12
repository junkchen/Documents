# **Java 随机数** #

实际开发中产生随机数的使用很常见，因此在程序中产生随机数的操作很重要。在Java中主要提供了两种方法产生随机数，分别是调用Math类的random()方法和Random类提供的产生各种数据类型的随机数的方法。  

## **Math.random()方法** ##

在Math类中存在一个random()方法，用于产生随机数，这个方法默认生成大于等于0.0小于1.0的double型随机数，即0<=Math.random()<1.0,虽然Math.random()方法可以产生0~1之间的double型数字，其实只要在Math.random()语句上稍加处理，就可以使用这个方法产生任意范围的随机数。  

```txt
(int)(Math.Random()*n) ==>> 返回大于等于0小于n的随机数
m+(int)(Math.Random()*n) ==>> 返回大于等于m小于m+n(不包括m+n)的随机数
```

下面写个示例：  

```java
public class MathRandomNumber {

	public static void main(String[] args) {
		System.out.println("Math.random()产生的随机数： " + Math.random());
		System.out.println("任意一个0~50的随机数： " + getRandomNumber(0, 50));
	}
	
	/**
	 * 获得某个范围内的随机数
	 * @param num1 起始范围参数
	 * @param num2 终止范围参数
	 * @return 返回num1到num2的随机数
	 */
	public static int getRandomNumber(int num1, int num2) {
		return (int) (num1 + Math.random() * (num2 - num1));
	}

}

```

运行结果为：  

```txt
Math.random()产生的随机数： 0.43653516378934365
任意一个0~50的随机数： 28
```

每次运行的结果都不一样。  

使用Math类的random()方法也可以随机生成字符，可以使用如下代码生成a~z之间的字符。  

```java
(char)('a' + Math.random() * ('z' - 'a' + 1))
```

任意两个字符之间的随机字符，可以使用如下方式：  

```java
(char)(char1 + Math.random() * (char2 - char1 + 1))
```

示例：  

```java
public class MathRandomChar {

	public static void main(String[] args) {
		//获取a~z之间的随机字符
		System.out.println("任意小写字符： " + getRandomChar('a', 'z'));
		//获取A~Z之间的随机字符
		System.out.println("任意大写字符： " + getRandomChar('A', 'Z'));
		//获取0~9之间的随机字符
		System.out.println("0~9之间任意数字： " + getRandomChar('0', '9'));
	}

	//获取任意字符之间的随机字符
	public static char getRandomChar(char char1, char char2) {
		return (char)(char1 + Math.random() * (char2 - char1 + 1));
	}
}
```

运行结果为：  

```txt
任意小写字符： g
任意大写字符： U
0~9之间任意数字： 6
```

> random()方法返回的值实际上是伪随机数，他通过复杂的运算而得到一系列的数。该方法是通过当前时间作为随机数生成的参数，所以每次执行程序都会产生不同的随机数。  


## **Random类方式** ##

除了Math类中的random()方法可以获取随机数之外，Java中还提供了一种可以获取随机数的方式，就是java.util.Random类。可以通过实例化一个Random对象创建一个随机数生成器。  

语法如下：  

```java
Random r = new Random();
```

其中，r是Random对象。  

以这种方式实例化对象时，Java编译器以系统当前时间作为随机数生成种子，因为每时每刻的时间不能相同，所以产生的随机数不同。  

也可以在实例化Random对象时，设置随机数生成的种子。  

语法如下：  

```java
Random r = new Random(seedValue);
```

seedValue是随机数生成的种子。  

- public int nextInt()： 返回一个随机整数
- public int nextInt(int n)： 返回大于等于0小于n的随机整数
- public long nextLong()： 返回一个随机长整型数
- public boolean nextBoolean()： 返回一个随机布尔型值
- public float nextFloat()： 返回一个随机浮点型值
- public double nextDouble()： 返回一个随机双精度浮点型数
- public double nextGaussian()： 返回一个概率密度为高斯分布的双精度值

实例代码：  

```java
public static void main(String[] args) {
	//实例化一个Random类
	Random r = new Random();
	//随机产生一个整数
	System.out.println("随机产生一个整数: " + r.nextInt());
	//随机产生一个大于等于0小于10的整数
	System.out.println("随机产生一个大于等于0小于10的整数: " + r.nextInt(10));
	//随机产生一个布尔型的值
	System.out.println("随机产生一个布尔型的值: " + r.nextBoolean());
	//随机产生一个浮点型的值
	System.out.println("随机产生一个浮点型的值: " + r.nextFloat());
	//随机产生一个双精度型的值
	System.out.println("随机产生一个双精度型的值: " + r.nextDouble());
	//随机产生一个概率密度为高斯分布的双精度值
	System.out.println("随机产生一个概率密度为高斯分布的双精度值: " + r.nextGaussian());
}
```

运行结果如下：  

```txt
随机产生一个整数: 105798029
随机产生一个大于等于0小于10的整数: 3
随机产生一个布尔型的值: false
随机产生一个浮点型的值: 0.20587307
随机产生一个双精度型的值: 0.6920970515624921
随机产生一个概率密度为高斯分布的双精度值: 0.5640177494983246
```

通过以上两者方式我们就可以轻松的产生我想要的随机数。  

**欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  
