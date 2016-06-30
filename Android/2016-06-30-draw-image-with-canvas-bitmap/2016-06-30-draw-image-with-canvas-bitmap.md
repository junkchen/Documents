# **Android使用Bitmap、Canvas制作图片** #

先看下效果图：  

在开发中有时候需要生成报告图，我们就可以使用Bitmap、Canvas进行文字图片的绘制，从而生成各种各样的图片或报告。看具体例子：  

## **1、使用Bitmap、Canvas绘制图片并保存** ##

```java
public File drawImage() {
    //1、创建一个bitmap，并放入画布。
    Bitmap bitmap = Bitmap.createBitmap(400, 800, Bitmap.Config.ARGB_8888);
    Canvas canvas = new Canvas(bitmap);

    //2、设置画笔
    Paint paint = new Paint();
    paint.setColor(Color.RED);//设置画笔颜色
    paint.setStrokeWidth(20.0f);// 设置画笔粗细
    paint.setTextSize(50f);//设置文字大小

    //3.在画布上绘制内容
    canvas.drawColor(Color.WHITE);//默认背景是黑色的
    canvas.drawText("Junk Chen", 100, 150, paint);
    canvas.drawLine(100, 300, 300, 300, paint);
    canvas.drawCircle(200, 500, 100, paint);

    //4、保存绘制的内容
    File imgFile = new File(Environment.getExternalStorageDirectory(),
            "IMG-" + System.currentTimeMillis() + ".png");//创建一个文件
    try {
        OutputStream os = new FileOutputStream(imgFile);//创建输出流
        bitmap.compress(Bitmap.CompressFormat.PNG, 100, os);//通过输出流将图片保存
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    }
    return imgFile;
}
```

## **2、加的添加写入文件的权限** ##

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

欢迎加QQ群交流： **365532949**  
**Homepage:** [http://junkchen.com](http://junkchen.com)