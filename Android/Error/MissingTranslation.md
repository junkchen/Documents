#Android开发错误：Error:" " is not translated in "en" (English) [MissingTranslation]如何处理？

----------
今天（2015/11/25）在Android编译时发现这个错误，见下图  
![](https://raw.githubusercontent.com/junkchen/Documents/master/Image/Error/MissingTranslation.png)  

最终在StackOverFlow上找到了解决方法，大概有这么几种方法，现整理如下，供大家参考：  

1、尝试添加translatable="[true / false]"  

	<string name="junkchen" translatable="false">Junk Chen!</string>  

2、在resources中添加属性  
	
	<resources 
		xmlns:tools="http://schemas.android.com/tools"
    	tools:ignore="MissingTranslation" >

3、指定语言  

	<resources 
		xmlns:tools="http://schemas.android.com/tools"
    	tools:locale="en" >
	</resources>

4、使用Android studio可以在build.gradle中的android中添加lintOptions  

	lintOptions {
    	disable 'MissingTranslation'
	}

或者  

	lintOptions {
	    checkReleaseBuilds false
	    abortOnError false
	}

暂时发现这几种方法都可以解决，我都测试通过。

如何任何问题可以加群讨论：Android开发交流群号 365532949  
个人网站：[http://junkchen.com](http://junkchen.com)

参考：  
1. [http://stackoverflow.com/questions/21118725/error-app-name-is-not-translated-in-af](http://stackoverflow.com/questions/21118725/error-app-name-is-not-translated-in-af)  
2. [http://stackoverflow.com/questions/11443996/lint-how-to-ignore-key-is-not-translated-in-language-errors](http://stackoverflow.com/questions/11443996/lint-how-to-ignore-key-is-not-translated-in-language-errors)