#Android Studio安装与使用（二）

----------
看[Android Studio安装与使用（一）](http://blog.csdn.net/kjunchen/article/details/49980931)  

自从写了Android Studio安装与使用（一）后，发现存在很多问题，一些小伙伴总是Q我，我也发现，应该是我的博客还很不完善，另外一方面，我也希望，看博客的小伙伴，在安装于使用的过程中，应该按照我的博客里的步骤一步一步操作，要认真哦，当然上次写的确实还不完善，有的还没有讲到，这些小伙伴们也是很纠结，所以今天在续一篇Android Sdudio安装与使用，如果你有幸看到的话，又很想用，那要认认真真看完哦。好了，扯淡结束，上正文：  

在没有谷歌还没有出Android Studio工具之前，大家应该都是使用Eclipse进行Android应用程序开发的。那么在Eclipse中，我们要开始一个项目的时候，首先会选择一个工作空间（Workspace），然后创建一个工程（Project），对于一个工程，其实就是一个Android应用，在一个工作空间里可以创建很多个工程，也就是很多个Android应用，在Eclipse中是这样，相信大家都很清楚。  

那么在Android Studio中是怎么样的呢，在Studio中，是Project和Module这两个概念，其实Studio中的Project相当于Eclispse中的Workspace，Studio中的Module相当于Eclipse中的Project，一个Module就是一个Android应用。接下来就看看实例吧：  

##创建Module

----------

上篇中，我创建了一个Project，接下来开始创建一个Module 
 
1、菜单栏File->New->New Module  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/201.png)  

2、选择Phone&Tablet Module,然后Next。
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/202.png)  

3、给自己的应用娶个好名字。
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/203.png)

4、选择一个适当的模版。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/204.png)  

5、直接Finish吧  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/205.png)  

6、Module创建中。。。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/206.png)

7、创建完成。其实跟创建工程的时候没啥区别，很简单吧，关键概念要清晰 
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/207.png) 

8、稍稍编辑下，呵呵 
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/208.png) 

9、打开模拟器，运行看看效果。不会用的请看[Genymotion模拟器安装与使用](http://blog.csdn.net/kjunchen/article/details/49979489)  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/209.png)  

10、模拟器打开了，运行应用。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/210.png)  

11、选择模拟器，运行应用也可以用真机，和Eclipse一样。 
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/211.png)  

12、编译构建中。。。状态显示在工具底部状态栏  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/212.png)  

13、项目运行成功。又一次大功告成 
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/213.png)

14、Project面板，一般开发使用Android。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/214.png)  

15、我们切换到Project看看  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/215.png)  

16、文件夹图标上有个小手机的就是一个应用。到这里，相信大家应该明白Android Studio中Project与Module的关系了吧。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/216.png)  

##Android Studio常用设置补充

----------
1、设置SDK  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/217.png)  

2、更新SDK，Android SDK Tools，Android SDK Platform-tools必须安装，Android SDK Build-tools选择一般版本安装即可，多安装也没关系，其他的看下图，源代码下载后，在代码编辑时可以查看源码实现的过程，若没有下载，看到的是字节码。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/218.png)  

3、代码提示设置,Studio中输入一个词就会有提示的，这点跟Eclipse不一样，用起来那是相当爽的。  
![https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/219.png](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/219.png)  

4、Android Studio软件更新，安装后，在软件里就可以检查更新升级，如果不是很想折腾的，网速不行的那就不要更新了，但是新版本一般会有些新的特性，用着应该是更好用。这个根据自己情况，更新后项目工程的某些配置会更改，有的人折腾好长时间都弄不好，当对于一个程序员来说，这好像不应该是个问题吧。总之自个儿看着办吧。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/220.png)  

好了，本篇到此结束。本人英语差，过程中，有的翻译不当，勿笑勿喷请见谅。  
如有任何问题欢迎加群讨论：  **Android开发交流群：365532949** 

看[Android Studio安装与使用（一）](http://blog.csdn.net/kjunchen/article/details/49980931)   

**个人主页**：[http://junkchen.com](http://junkchen.com)