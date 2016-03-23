#**使用Gson轻松解决复杂结构的Json数据解析**

##**JSON简介**

JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。JSON 是存储和交换文本信息的语法，类似XML，但是比XML更小、更快，更易解析。  

----------

###**JSON语法**  

JSON构建于两种结构：  

- “名称/值”对的集合（A collection of name/value pairs）。不同的编程语言中，它被理解为对象（object），纪录（record），结构（struct），字典（dictionary），哈希表（hash table），有键列表（keyed list），或者关联数组 （associative array）。


- 值的有序列表（An ordered list of values）。在大部分语言中，它被实现为数组（array），矢量（vector），列表（list），序列（sequence）。

**对象（object）** 是一个无序的“‘**名称/值**’对”集合。一个对象以“ **{** ”（左括号）开始，“ **}** ”（右括号）结束。每个“名称”后跟一个“ **:** ”（冒号）；“‘名称/值’ 对”之间使用“ **,** ”（逗号）分隔。通过对象.名称获取值，值的类型可以是数字、字符串、数组、对象几种。  

**数组（array）** 是值（value）的有序集合。一个数组以“ **[** ”（左中括号）开始，“ **]** ”（右中括号）结束。值之间使用“ **,** ”（逗号）分隔。使用索引获取，值的类型可以是数字、字符串、数组、对象几种。  

经过对象、数组两种结构可以组合成复杂的数据结构。

json语法规则是：数据在键值对中，数据由逗号分隔，花括号保存对象，方括号保存数组。  

json数据书写格式是：名称/值。名称写在前面，值写在后面，都在双引号中，中间用冒号隔开。如
	
	"name":"JunkChen"

**json的值**可以是：***数字***(整数或浮点数)、 ***字符串***(在双引号中)、***逻辑值***(true或false)、 ***数组***(在方括号中)、 ***对象***(在花括号中)、 ***null*** 。  

----------

###**Json数据示例**

**Json对象**  

	{
		"firstName":"Junk",
		"lastNmae":"Chen",
		"sex":"male",
		"age":23
	}

那么，如何取值呢？假设我们给这个对象取名personObj，personObj.firstName = Junk , personObj.age = 23 。  

如果用xml表示，如：

	<?xml version="1.0" encoding="utf-8"?>
	<person>
		<firstName>Junk</firstName>
		<lastName>Chen</lastName>
		<sex>male</sex>
		<sec>23</sex>
	</person>

使用xml描述就比Json显得臃肿，xml中都是标记对形式,数据量肯定比Json大。如果用Json数组表示那就更简单了（如下）。

**Json数组**

	[
		"Junk","Chen","male",23
	]

如果这个数组取名为personArray，则 personArray[0] = Junk, personArray[2] = male 。

**复合结构**  

	{
		"person":["Junk","Chen","male",23],
		"cat":
		{
			"name":"Jon",
			"age":3
		}
		"province":
		[{
			"name":"广东",
			"cities":["深圳","广州","珠海"]
		},
		{
			"name":"陕西",
			"cities":["西安","汉中","咸阳"]
		}]
	}  

这个示例中，首先是一个Json对象，对象里面包含Json数组，Json数组里面又包含有Json对象，这样就构成了一个复杂结构的Json数据。
  

----------

##**使用Gson快速解析Json数据**  

解析简单的Json数据使用JsonObject和JsonArray还可以，但是遇到复杂结构的Json数据，就很费力了，这时我们可以采用Gson进行Json数据解析。Gson是谷歌推出的Json解析的工具包。接下来就看个实例：  

###1、导入Gson jar包

下载Gson jar包，新建Java工程将下载jar包导入自己的工程。  
Gson 2.6.2 Jar包下载： [http://download.csdn.net/detail/kjunchen/9469938](http://download.csdn.net/detail/kjunchen/9469938) 　　  
最新版Gson下载查找：[https://github.com/google/gson](https://github.com/google/gson)   

###2、创建Java Bean类

新建**Cat**类,如下：  
**Cat.java**

	package me.jc.gson;
	
	public class Cat {
		private String name;
		private String sex;
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
	
		public String getSex() {
			return sex;
		}
	
		public void setSex(String sex) {
			this.sex = sex;
		}
	
		public Cat(String name, String sex) {
			super();
			this.name = name;
			this.sex = sex;
		}
	
		public Cat() {
			super();
		}
	
		@Override
		public String toString() {
			return "Cat [name=" + name + ", sex=" + sex + "]";
		}
	
	}

Cat类中设置字段名为name和sex。

新建**Dog**类，如下：  
**Dog.java**
```
	package me.jc.gson;
	
	public class Dog {
		private String name;
		private int age;
	
		public String getName() {
			return name;
		}
	
		public void setName(String name) {
			this.name = name;
		}
	
		public int getAge() {
			return age;
		}
	
		public void setAge(int age) {
			this.age = age;
		}
	
		public Dog(String name, int age) {
			super();
			this.name = name;
			this.age = age;
		}
	
		public Dog() {
			super();
		}
	
		@Override
		public String toString() {
			return "Dog [name=" + name + ", age=" + age + "]";
		}
	
	}
```

Dog类中同样设置字段名为name和sex。


新建**Person**类，如下：  
**Person.java**

```
package me.jc.gson;

import java.util.Arrays;

public class Person {
	private int id;
	private String name;
	private String sex;
	private long[] phone;
	private Cat cat;
	private Object object;

	public Person() {
	}

	public Person(int id, String name, String sex) {
		super();
		this.id = id;
		this.name = name;
		this.sex = sex;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getSex() {
		return sex;
	}

	public void setSex(String sex) {
		this.sex = sex;
	}

	public long[] getPhone() {
		return phone;
	}

	public void setPhone(long[] phone) {
		this.phone = phone;
	}

	public Cat getCat() {
		return cat;
	}

	public void setCat(Cat cat) {
		this.cat = cat;
	}

	public Object getObject() {
		return object;
	}

	public void setObject(Object object) {
		this.object = object;
	}

	@Override
	public String toString() {
		return "Person [id=" + id + ", name=" + name + ", sex=" + sex
				+ ", phone=" + Arrays.toString(phone) + ", cat=" + cat
				+ ", object=" + object + "]";
	}

}  
```

Person类中设置字段名为id、name、sex、phone、cat、object，这个Person中包含了Cat对象，还有个Object对象，从而构成了一个相对复杂的对象。到目前为止，似乎好像跟Json没什么关系，那么接下来我们看看如何使用Gson解析Json数据。  

***注意***:  

- 1、如果Java Bean类中有内部嵌套的类必须用static进行修饰；
- 2、类中的属性名必须跟Json字段里面的名称(Key)是一样的。


###3、使用Gson解析Json  

新建测试类GsonTest，创建Gson对象，Gson提供toJson()方法将一个JavaBean实例对象转换成Json数据，通过Gson.fromJson()方法将Json数据还原成JavaBean实例对象。

**GsonTest.java**

```java
package me.jc.gson;

import com.google.gson.Gson;

public class GsonTest {

	public static void main(String[] args) {
		Gson gson = new Gson();//实例化Gson对象

		Person person = new Person(1, "JunkChen", "male");//实例化Person对象
		person.setPhone(new long[] { 17802900000l, 17802900001l });
		Cat cat = new Cat("wangwang", "female");//实例化Cat对象
		Dog dog = new Dog();//实例化Dog对象
		dog.setAge(20);
		person.setCat(cat);
		person.setObject(dog);
		System.out.println(gson.toJson(person));//将Person对象转换成Json对象数据
		System.out.println();

		String json = "{\"phone\":[17802900000, 17802900001],\"id\":1,\"cat\":{\"name\":\"wangwang\"},\"sex\":\"female\",\"name\":\"Junk\",\"object\":{\"name\":\"doggg\",\"age\":12}}";//Json格式的数据
		Person fromJson = gson.fromJson(json, Person.class);//使用Gson将Json数据转换成Person对象
		System.out.println(fromJson.toString());
		System.out.println();

		System.out.println(gson.fromJson(gson.toJson(fromJson.getObject()), Dog.class));
	}
}  
```

运行结果如下图所示：  
    
![](https://github.com/junkchen/Documents/raw/master/Android/GsonParseJson/ec20160322164435.png)

OK，如果你之前是使用***JsonObject***和***JsonArray***进行Json数据解析的，那么看了这个是不是觉得Json数据解析很简单呢。只要根据Json数据创建相应的Java Bean对象，然后使用Gson轻轻松松搞定Json数据解析。赶快去试试吧。  

Json解析的工具类还有很多，除了谷歌Gson外，还有阿里的FastJson也很好用。使用方法大同小异，具体用哪个自己决定。 

欢迎加qq群讨论：***365532949***  
HomePage：**[http://junkchen.com ](http://junkchen.com )**