# Android Studio 设置代理

Android Studio 在构建项目的时候会去下载一些资源，由于大部分资源仓库在国外，需要翻墙才能下载下来，可以通过设置代理。需要知道代理的地址和端口。启动Android Studio后，点击 File -> Settings -> Appearance & Behavior -> System Settings -> HTTP Proxy 可以进入代理这只界面。然后选择 Manual proxy configuration ，设置 Host name 和 Port number ，最后点击 OK 。之后还会弹一个确认框，点击确定即可。  

# 如何查看应用程序所占的 IP 和端口号

可以通过命令行的方式查看。在电脑的任务管理器中找到对应程序的 PID 号，然后在 Windows 命令行窗口中输入如下命令即可查看该应用程序的IP和端口号：  

```txt
netstat -ano | findstr 7116  # 7116 为该应用程序在进程中所占的端口号
```

用于设置代理。  

参考资料：  
[如何查看程序所占端口号和IP](https://www.cnblogs.com/kangjianwei101/p/5639328.html)