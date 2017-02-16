# **策略模式（Strategy Pattern）** #

## **定义** ##

策略模式定义了一系列的算法，并将每一个算法封装起来，而且使它们还可以相互替换。策略模式让算法独立于使用它的客户而独立变化。

策略模式属于行为型模式。  

## **组成** ##

- **环境类（Context）**： 持有一个策略类（Strategy）的引用，最终给客户端使用。
- **“抽象策略类（Strategy）**： 通常由一个接口或者抽象类实现，定义所有 ConcreteStrategy 支持的算法。Context使用这个接口来调用 ConcreteStrategy 定义的算法。
- **具体策略类（ConcreteStrategy）**： 包含了相关的算法和行为，实现抽象策略类。

## **应用场景** ##

1、 多个类的区别只在表现行为不同，可以使用Strategy 模式，在运行时动态选择具体要执行的行为。  

2、 需要在不同情况下使用不同的策略(算法)，或者策略还可能在未来用其它方式来实现。  

3、 对客户隐藏具体策略(算法)的实现细节，彼此完全独立。  

4、如果一个对象有很多行为，如果不用恰当的模式，这些行为只好使用多重条件选择语句来实现。  

## **优缺点** ##

### **优点** ###

1、算法可以自由切换。  
2、避免使用多重条件判断。 
3、扩展性良好。

### **缺点** ###

1、策略类会增多。  
2、所有策略类都需要对外暴露。  

## **注意** ##

如果一个系统的策略多于四个，就需要考虑使用混合模式，解决策略类膨胀的问题。  

## **如何运用** ##

**情景**：  

假设现在要设计一个贩卖各类书籍的电子商务网站的购物车系统。一个最简单的情况就是把所有货品的单价乘上数量，但是实际情况肯定比这要复杂。比如，本网站可能对所有的高级会员提供每本20%的促销折扣；对中级会员提供每本10%的促销折扣；对初级会员没有折扣。  

**分析**：  

算法一：对初级会员没有折扣。  
算法二：对中级会员提供10%的促销折扣。  
算法三：对高级会员提供20%的促销折扣。  

### **定义抽象策略类** ###

首先定义一个抽象会员策略类，定义一个折扣计算的方法。  

**MemberStrategy.java**

```.java
public interface MemberStrategy {
	float calDiscountPrice(float originalPrice);
}
```

### **定义环境类** ###

定义一个计算价格的环境类，这个类持有抽象策略的引用，供客户端使用,。  

```.java
public class Price {
	private MemberStrategy strategy;
	
	public Price(MemberStrategy strategy) {
		this.strategy = strategy;
	}
	
	public float quote(float originalPrice){
		return strategy.calDiscountPrice(originalPrice);
	}
}
```

### **定义具体策略类** ###

有三种会员，可以定义三个具体的策略类，实现抽象策略中的方法。  

**初级会员**
**PrimaryMemberStrategy.java**

```.java
public class PrimaryMemberStrategy implements MemberStrategy {

	@Override
	public float calDiscountPrice(float originalPrice) {
		System.out.println("初级会员没有折扣");
		return originalPrice;
	}

}
```

**中级会员**
**IntermediateMemberStrategy.java**

```.java
public class IntermediateMemberStrategy implements MemberStrategy {

	@Override
	public float calDiscountPrice(float originalPrice) {
		System.out.println("对中级会员提供10%的促销折扣");
		return originalPrice * 0.9f;
	}

}
```

**高级会员**
**AdvanceMemberStrategy.java**

```.java
public class AdvanceMemberStrategy implements MemberStrategy {

	@Override
	public float calDiscountPrice(float originalPrice) {
		System.out.println("对中级会员提供20%的促销折扣");
		return originalPrice * 0.8f;
	}

}
```

**创建一个客户端测试**
**Client.java**

```.java
public class Client {

	public static void main(String[] args) {
		float originalPrice = 100;
		Price price = new Price(new PrimaryMemberStrategy());
		float discount = price.quote(originalPrice);
		System.out.println("初级会员购买价格为： " + discount);
		
		price = new Price(new IntermediateMemberStrategy());
		discount = price.quote(originalPrice);
		System.out.println("中级会员购买价格为： " + discount);
		
		price = new Price(new AdvanceMemberStrategy());
		discount = price.quote(originalPrice);
		System.out.println("高级会员购买价格为： " + discount);
	}

}
```

**运行结果为：**
```.txt
初级会员没有折扣
初级会员购买价格为： 100.0
对中级会员提供10%的促销折扣
中级会员购买价格为： 90.0
对中级会员提供20%的促销折扣
高级会员购买价格为： 80.0
```

根据不同的会员级别，会给出相应的购买价格。
