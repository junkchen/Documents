# **Android 应用开发支持不同的语言** #

## **创建区域目录和字符串文件** ##

要支持更多的语言，在 *res/* 下创建额外的 *values* 目录以一个连字符 ‘-’ 和 ISO 语言代码结尾命名。例如， *values-es* 目录包含了一些简单的资源，区域语言代码为 “*es*” 。Android设备在运行时，会根据语言环境的设置加载相应的资源。  

一旦你决定支持某种语言，需要创建资源子目录和字符串资源文件。如：  

```dir
MyProject/
    res/
       values/
           strings.xml
       values-es/
           strings.xml
       values-fr/
           strings.xml
```

为每一种语言添加字符串值到相应的文件中。  

在运行时，Android系统会根据用户设备当前的语言设置加载相应的字符串资源文件。  

例如，下面是一些不同语言的字符串资源文件。  

英语（默认语言）， */values/strings.xml* :    

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="title">My Application</string>
    <string name="hello_world">Hello World!</string>
</resources>
```

西班牙语， */values-es/strings.xml* :  

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="title">Mi Aplicación</string>
    <string name="hello_world">Hola Mundo!</string>
</resources>
```

法语， */values-fr/strings.xml* :  

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="title">Mon Application</string>
    <string name="hello_world">Bonjour le monde !</string>
</resources>
```

## **使用字符串资源** ##

我们可以在源代码和其他的XML文件中通过 *< string >* 元素中的 *name* 属性来引用字符串资源。  

在源代码中可以使用 *R.string.< string_name >* 语法来引用资源。大多数方法都接受这种方式引用字符串资源。  

例如：  

```java
// Get a string resource from your app's Resources
String hello = getResources().getString(R.string.hello_world);

// Or supply a string resource to a method that requires a string
TextView textView = new TextView(this);
textView.setText(R.string.hello_world);
```

在其他的 XML 文件中，只要 XML 属性接受字符串值时都可以使用 *@string/< string_name >* 语法来引用字符串资源。  

例如：  

```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/hello_world" />
```

## **Android 多语言文件夹** ##

常见语言文件夹如下：  

- values-zh  中文
	- values-zh-rCN  中文（中国）
	- values-zh-rHK  中文（香港）
- values-en  英文
	- values-en-rUS  英文（美国）
	- values-en-rGB  英文（英国）
- values-de  德文
- values-fr  法文
- values-ja  日文
- values-ko  韩文
- values-es  西班牙文
- values-it  意大利文
- values-ar  阿拉伯文
