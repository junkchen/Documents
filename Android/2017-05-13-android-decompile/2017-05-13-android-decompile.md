# **Android 反编译** #

反编译属于逆向工程中的一种，有很多高级的手段和工具。  

在 Android 开发中，一般的反编译工具有：  

- **Apktool**: [https://github.com/iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool)
  解包 Android apk 获取资源文件，如 AndroidManifest.xml、res、lib、asset 等；还可进行二次打包。

- **dex2jar**: [https://github.com/pxb1988/dex2jar](https://github.com/pxb1988/dex2jar)
  将 Android 的 .dex 文件转换为 Java 的 .class 文件的工具

- **jd-gui**: [https://github.com/java-decompiler/jd-gui](https://github.com/java-decompiler/jd-gui)
  用于显示 Java 源文件（jar包）的图形化工具。


## **一、使用 Apktool 获取资源文件** ##

- **Windows**:
  1. 下载封装脚本 [apktool.bat](https://raw.githubusercontent.com/iBotPeaches/Apktool/master/scripts/windows/apktool.bat)
  2. 下载 apktool （[找最新版本](https://bitbucket.org/iBotPeaches/apktool/downloads/)）
  3. 重命名下载的 jar 文件为 **apktool.jar**
  4. 将 apktool.jar 和 apktool.bat 两个文件移动到Windows目录（通常是 ***C://Windows***）
  5. 如果不访问 ***C://Windows***，可以吧这两个文件放在任何地方，然后将目录添加到系统环境变量 PATH 中。
  6. 尝试通过命令提示符运行 **apktool** 。

Apktool 使用方法：  
在命令提示符中进行

```
$ apktool d app.apk
//将反编译 app.apk 得到的文件保存到 app 文件夹中

$ apktool decode app.apk
//将反编译 app.apk 得到的文件保存到 app 文件夹中

$ apktool d E:\app.apk -o F:\apps
//将反编译E盘的 app.apk 得到的文件保存到F盘的 app 文件夹中
```

- **d** ： 表示进行反编译解包
- **-o** ： 指定输出文件保存的位置
- **app.apk** ： 要进行反编译的文件
- **apps** ： 指定输出的目录的文件夹名

使用 apktool 进行二次打包  
```
$ apktool b bar -o new_bar.apk
// builds bar folder into new_bar.apk
```

## **二、使用 dex2jar 反编译成 Java 源代码** ##

在官网下载后，在命令提示符中定位到 dex2jar 目录，进行命令行操作： 

```
$ d2j-dex2jar.bat app.apk
//将 app.apk 反编译为 app-dex2jar.jar 文件

$ d2j-dex2jar.bat E:\app.apk -o F:\app.jar
将反编译E盘的 app.apk 得到的jar文件保存到F盘并取名为 app.jar

$ d2j-dex2jar.bat --force E:\app.apk -o F:\app.jar
//效果同上 
```


## **三、使用 jd-gui 查看源代码** ##

jd-gui是一个图形化的工具，使用很简单，使用 dex2jar 工具会得到源码的 .jar 文件，可以使用 jd-gui 工具打开即可查看源代码。

