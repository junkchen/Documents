# **Android error: "Apostrophe not preceded by \"错误解决办法** #

android开发中，在资源文件 values 文件中报特殊字符没有被转义的错误，信息如下：  

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	...
    <string name="faq">What's the Bluetooth version?</string>
</resources>
```
结果在编译时报如下错误：  

```txt
Error:(117, 5) Apostrophe not preceded by \ (in What's the Bluetooth version?
```

解决办法是对特殊字符使用转义字符进行转义（即在特殊字符前加上我们常用的反斜杠 “ \ ” ）。  

修改后：  

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	...
    <string name="faq">What\'s the Bluetooth version?</string>
</resources>
```

然后再次编译就顺利通过了。  

> **欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  
