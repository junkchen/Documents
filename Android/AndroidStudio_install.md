#Android Studio安装与使用
------

工欲善其事必先利其器！Android开发利剑之**Android Studio**。
  
好的开发工具可以加快我们的开发速度，编码更爽，编写更好用的应用。
  
目前，Android开发使用Android Studio已经越来越流行了，而且这也是官方推荐使用的，众所周知，以前我们一般使用Eclipse安装ADT插件进行开发，但是Google官方现在已经不在对Eclipse的ADT插件进行更新了，所以我们需要逐步转到Android　Studio上来。作为官方推荐，使用起来确实比Eclipse好用，用过之后定是爱不释手。
**建议安装之前先安装好Java的JDK（1.7版本或更高）和Android的SDK。**  

- Android Studio官方下载地址: [http://developer.android.com/sdk/index.html](http://developer.android.com/sdk/index.html)
- Android SDK官方下载地址: 同上
- Java JDK官方下载地址: [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

由于天朝网络限制，Android的官网大部分人可能进不去，需要翻墙。我在这里就不教大家翻了，安装包我已下好，上传至百度云分享给大家。

- Android Studio百度云下载：[http://pan.baidu.com/s/1kTB5Zkr](http://pan.baidu.com/s/1kTB5Zkr)
- Android SDK百度云下载：[http://pan.baidu.com/s/1gdFHTJ1](http://pan.baidu.com/s/1gdFHTJ1)  
注：压缩包解压密码为 junkchen.com  

下载好之后就可以安装了。

##Android Studio安装

1、解压之后打开  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/1.png)

2、基本上一直 **Next** 就好了  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/2.png)

![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/3.png)

3、选择软件安装位置，一般不建议安装在系统盘。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/4.png)  

4、选择Install  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/5.png) 

5、安装中。。。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/6.png)  

6、安装完成， Next  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/7.png)  

7、Finish，完成安装。到这里安装已经算是已经结束了，不过在使用之前还有一些基本配置，也看看吧。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/8.png)  

8、我已安装使用过， 所以电脑里会有以前使用的配置，可以直接导入，很方便，没有安装过的，就选择第三项，然后就该 OK 进入下一步。 
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/9.png)  

9、启动界面  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/10.png)  

10、设置Android SDK的位置，这里就是之前为什么建议大家先安装SDK的原因了，如果没有安装现在就得安装了，设置好后选择 **Finish**。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/11.png)  

11、继续 **Finish**。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/12.png)  

12、到这里就真的彻底安装好了，接下来就开始Coding吧，赶快整个Demo泡泡。 
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/13.png)  

## Android Studio使用、新建项目 ##

1、首先我们选择开始创建一个新的工程  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/1.png)  

2、设置工程的基本信息。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/2.png)  

3、接下来优势 **Next** 的时候了，个人最喜欢这个了，太简单了，不用思考。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/3.png)  

![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/4.png)  

4、好，**Finish**![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/5.png)  

5、项目创建过程中，第一次比较那个啥，大家懂得，耐心等等吧，现在的等待是为了更好的将来。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/6.png)  

6、守得云开见月明，哇，还在加载...  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/7.png)  

7、终于看见你的真面目了，看着挺不错嘛，是吧，选择绿箭头运行应用，最好先打开一个模拟器。我使用的是Genymotion模拟器，传说是最快的Android模拟器。 何方神圣马上去看看 ["Genymotion安装与使用"](http://blog.csdn.net/kjunchen/article/details/49979489) 。如果没有也可以选择Android原生的模拟器，**自己点开看**第一图标打开创建一个。我选择使用最快的Genymotion模拟器。
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/8.png)  

8、**OK**， 这个是我之前已经启动了Genymotion模拟器。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/9.png) 

9、项目运行成功啦,oh yeah,处女工程，Android Studio使用就是这么爽。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/10.png)  

接下来进行一些基本设置，让你用得更爽。  

10、Android Studio可设置主题，系统提供三种可选择，也支持第三方的，个人觉得系统就够用了。鄙人最喜欢的使这个炫酷黑的主体，看着很高大上啊有木有，这么高端大气上档次别忘了保存噢。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/11.png)  

11、设置字体及大小，这个需要自己另存一下。  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/12.png)  

![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/13.png)  

12、不说为什么，自己看吧。只可意会，不可言传!  
![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/14.png)  

![](https://github.com/junkchen/Documents/raw/master/Image/AndroidStudio_install/use/15.png)  

基本设置就说这么多了，Android Studio还有很多好用的亮点，自己慢慢琢磨去吧！冰冻三尺非一日之寒。  

#####个人网站：[http://junkchen.com](http://junkchen.com)
2015/11/22 