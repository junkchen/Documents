# Tips #

## **1、关于List.remove()与List.subList()** ##
1/20/2016 3:15:20 PM 

最近在项目中发现，当非常频繁地调用List.subList()时,就会产生java.lang.StackOverflowError（栈溢出）异常，想了好长时间都没有解决，不用吗又不行，需求所致，最后就像，既然截取不行那就删嘛，因此我就使用remove()方法试了试，结果问题就这么给解决了，真的是fun,困扰了我好长时间。  

当List中的数据很简单时，remove()比subList()快，而在我的项目中，由于List中的数据稍复杂点，量又比较大，测得remove()比subList()慢。

现在假设List中有20个数据，需要去掉前面10个数据而保留11~20的数据，采用如下两种方式，最后所得的结果都是一样的。

	int subLength = 10;//Remove the first 10
	//One
	mList = mList.subList((mList.size() - subLength), mList.size());

	//Two
	for (int i = (subLength - 1); i >= 0; i--) {
		mList.remove(i);
	}

如果你有遇到这样的问题，可以尝试下看能否解决问题，如有更好的方式解决，请给我留言或Email: junkchen@vip.qq.com  


----------

## **2、关于setResult()调用问题** ##

调用setResult()必须在finish()之前，在onPause()、onStop()、onDestroy()中调用无作用。  


----------

## **3、字符串 == “” 和 字符串 == “ ”区别** ##

如果一个字符串的内容为空，那么该字符串 == “” 则为true，而该字符串 == “ ”则为false，有一个空格则证明不是空的，所以在对字符串进行空内容判断时一定要注意，判断字符串是否为空也可直接调用 String.isEmpty() 方法。使用等号进行判断是容易出问题的，建议调用方法判断。


----------


欢迎加QQ群交流： **365532949** 
Homepage: [http://junkchen.com](http://junkchen.com)