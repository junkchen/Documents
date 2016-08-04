# **Android 开发关于Button或TextView控件英文字符全部显示大小写问题** #

在较新的 sdk 版本中，开发中我们会看到按钮的英文显示全为大写的。像下面这样：  

图片展位  

其布局代码为：  

```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Hello World!" />

<Button
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Button"/>
```

可以看到在布局代码中有明显的大小写，但是Button控件显示的却全是大写。其原因是在新的 API 中 Button 的 textAllCaps 属性被设置为 true 导致的，接下来将其设置为 false 在看看效果：  

布局代码：  

```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textAllCaps="true"
    android:text="Hello World!" />

<Button
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:textAllCaps="false"
    android:text="Button"/>
```

其显示结果为：  

图片展位  

可以看到 TextView 的 textAllCaps 属性设置为 true 后，无论设置的文本是大写还是小写，显示全部为大写， Button 的 textAllCaps 设置为 false 后，则根据设置的文本进行显示。  

textAllCaps 属性是控制文本是否全部显示为大写。    

**欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)**  