# **迭代器模式(Iterator Pattern)** #

迭代器模式属于行为型模式。  

## **定义** ##

提供一种方法顺序访问一个聚合对象中各个元素，而又不需暴露该对象的内部表示。  

## **优缺点** ##

- **优点**

  简化了遍历方式，对于对象集合的遍历，还是比较麻烦的，对于数组或者有序列表，我们尚可以通过游标来取得，但用户需要在对集合了解很清楚的前提下，自行遍历对象，但是对于hash表来说，用户遍历起来就比较麻烦了。而引入了迭代器方法后，用户用起来就简单的多了。

  可以提供多种遍历方式，比如说对有序列表，我们可以根据需要提供正序遍历，倒序遍历两种迭代器，用户用起来只需要得到我们实现好的迭代器，就可以方便的对集合进行遍历了。


- **缺点**
  
   于迭代器模式将存储数据和遍历数据的职责分离，增加新的聚合类需要对应增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性。

  对于比较简单的遍历（像数组或者有序列表），使用迭代器方式遍历较为繁琐，大家可能都有感觉，像ArrayList，我们宁可愿意使用for循环和get方法来遍历集合。

## **使用场合** ##

1、访问一个聚合对象的内容而无须暴露它的内部表示。 2、需要为聚合对象提供多种遍历方式。 3、为遍历不同的聚合结构提供一个统一的接口。

迭代器模式是与集合同生共死，一般来说，我们只要实现一个集合，就需要同时提供这个集合的迭代器，就像java中的Collection，List、Set、Map等，这些集合都有自己的迭代器。假如我们要实现一个这样的新的容器，当然也需要引入迭代器模式，给我们的容器实现一个迭代器。  

由于容器与迭代器的关系太密切了，所以大多数语言在实现容器的时候都给提供了迭代器，并且这些语言提供的容器和迭代器在绝大多数情况下就可以满足我们的需要，所以现在需要我们自己去实践迭代器模式的场景还是比较少见的，我们只需要使用语言中已有的容器和迭代器就可以了。


## **类图** ##

<img src="../iterator-pattern.png">

## **实现** ##

- **抽象迭代器 (Iterator)**：定义遍历元素所需的方法，一般有两个方法：是否有下一个元素的方法 hasNext()；获得下一个元素的方法 next()。  
- **具体迭代器 (ConcreteIterator)**：实现抽象迭代器中的方法。
- **抽象聚合 (Aggregate)**：一般是个接口，提供一个 iterator() 方法。
- **具体聚合 (ConcreteAggregate)**：抽象聚合的实现类。通常可以将具体迭代器在这个类中实现。

### **Iterator.java** ###

```.java
public interface Iterator<E> {
	boolean hasNext();
	E next();
}
```

### **Aggregate.java** ###

```.java
public interface Aggregate<E> {
	boolean add(E e);
	boolean remove(E e);
	Iterator<E> iterator();
}
```

### **ConcreteAggregate.java** ###

```.java
public class ConcreteAggregate<E> implements Aggregate<E> {
	private ArrayList<E> list = new ArrayList<>();
	
	@Override
	public boolean add(E e) {
		return list.add(e);
	}

	@Override
	public boolean remove(E e) {
		return list.remove(e);
	}

	@Override
	public Iterator<E> iterator() {
		return new ConcreteIterator();
	}

	private class ConcreteIterator implements Iterator<E> {
		private int cursor;
		
		@Override
		public boolean hasNext() {
			return cursor != list.size();
		}

		@Override
		public E next() {
			return (E) list.get(cursor++);
		}
		
	}
}
```

### **测试** ###

**Client.java**  

```.java
public class Client {
	public static void main(String[] args) {
		ConcreteAggregate<String> aggregate = new ConcreteAggregate<>();
		aggregate.add("诸葛亮");
		aggregate.add("赵云");
		aggregate.add("关羽");
		aggregate.add("孙权");
		
		Iterator iterator = aggregate.iterator();
		while (iterator.hasNext()) {
			System.out.println(iterator.next());
		}
	}
}
```

运行结果为：  

```.txt
诸葛亮
赵云
关羽
孙权
```

