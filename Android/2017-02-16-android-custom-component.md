# **Android 自定义控件** #

## **Android 控件的架构** ##

Android中的每个控件都会在界面中占得一块矩形区域，在 Android 中控件大致被分为两类，即 ViewGroup 控件和 View 控件。 ViewGroup 控件作为父控件可以包含多个 View 控件，并管理其包含的 View 控件。通过 ViewGroup 在整个界面上形成了一个树形结构，即控件树，上层控件负责下层子控件的测量与绘制，并传递交互事件。  

每个 Activity 都包含一个 Window 对象，在 Android 中 Window 对象通常由 PhoneWindow 来实现。PhoneWindow 将一个 DecorView 设置为整个应用窗口的根 View。　DecorView 作为窗口界面的顶层视图，封装了一些窗口操作的通用方法。它将要显示的具体内容呈现在了 PhoneWindow 上，里面所有的 View 监听事件都通过 WindowManagerService 来进行接收，并通过 Activity 对象来回调相应的 onClickListener。 在显示上，它将屏幕分成两个部分，一个是 TitleView，另一个是 ContentView。ContentView 是一个 ID 为 content 的 FrameLayout，activity_main.xml 就是设置在这样一个 FrameLayout 里。当程序在 onCreate() 方法中调用 setContentView() 方法后，ActivityManagerService 会回调 onResume() 方法，此时系统才会把整个 DecorView 添加到 PhoneWindow 中，并让其显示出来，最终完成界面的绘制。  

## **View 的测量** ##

Android 系统在绘制 View 前，必须对 View 进行测量，即告诉系统该画一个多大的 View 。这个过程在 onMeasure() 方法中进行。  

Android 系统提供了强大的 MeasureSpec 类去测量 View 。MeasureSpec 是一个 32 位的 int 值，其中高 2 位为测量的模式，低 30 位为测量的大小，在计算中使用位运算是为了提高并优化效率。  

测量模式有以下三种：  

- **EXACTLY**
  精确值模式，为控件的 layout_width 属性或 layout_height 属性指定的具体数值，为 match_parent 时是占据父 View 的大小，系统使用的是 EXACTLY 模式。

- **AT_MOST**
  最大值模式，当控件的 layout_width 或 layout_height 属性指定为 wrap_content 时，控件大小一般随着控件的子控件或内容的变化而变化，此时控件的尺寸只要不超过父控件的最大尺寸即可。  

- **UNSPECIFIED**
  不指定大小测量模式，View 想多大就多大，通常情况下绘制自定义 View 时才会使用。  

View 类默认的 onMeasure() 方法只支持 EXACTLY 模式，不重写只能使用该模式。控件可以响应你指定的具体宽高值或者 match_parent 属性，如果想让自定义 View 支持 wrap_content 属性则必须重写该方法来指定其 wrap_content 时的大小。  

```.java
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    super.onMeasure(widthMeasureSpec, heightMeasureSpec);
}
```

查看 super.onMeasure() 方法可以知道系统最终会调用  setMeasuredDimension(int measuredWidth, int measuredHeight) 方法将测量后的宽高值设置进去，完成测量工作。  

在 onMeasure() 方法中可以对宽高重新定义，参数是宽和高的 MeasureSpec 对象，该对象包含了测量的模式和测量值的大小。自定义测量值步骤，首先从 MeasureSpec 对象中提取具体的测量模式和大小；接着通过判断测量的模式，给出不同的测量值；最后将测量值传入 setMeasuredDimension() 方法完成测量。自定义测量值模板如下所示：  

```.java
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    //1.获取测量模式和大小
    int widthMode = MeasureSpec.getMode(widthMeasureSpec);
    int heightMode = MeasureSpec.getMode(heightMeasureSpec);
    int widthSize = MeasureSpec.getSize(widthMeasureSpec);
    int heightSize = MeasureSpec.getSize(heightMeasureSpec);
    //2.根据测量模式指定大小
    int finalWidth, finalHeight;
    if (widthMode == MeasureSpec.EXACTLY) {
        finalWidth = widthSize;
    } else {
        finalWidth = 200;
        if (widthMode == MeasureSpec.AT_MOST) {
            finalWidth = Math.min(finalWidth, widthSize);
        }
    }
    if (heightMode == MeasureSpec.EXACTLY) {
        finalHeight = heightSize;
    } else {
        finalHeight = 200;
        if (heightMode == MeasureSpec.AT_MOST) {
            finalHeight = Math.min(finalHeight, heightSize);
        }
    }
    //3.将指定的测量值进行最终大小的设置
    setMeasuredDimension(finalWidth, finalHeight);
}
```

## **View 的绘制** ##

当测量好了一个 View 之后，就可以简单地重写 onDraw() 方法，并在 Canvas 对象上绘制所需要的图形。在 Android 界面上绘制相应的图像必须在 Canvas 上进行绘制，Canvas 是绘制时的画板，而 Paint 是画笔。重写 View 类中的 onDraw() 方法，其中有一个参数就是 Canvas 对象，而画笔 Paint 则需要自己创建。不管多么复杂、精美的控件，都可以被拆分成一个个小的图形单元。    

```.java
@Override
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
	//需要什么就绘制什么
}
```

## **ViewGroup 的测量** ##

ViewGroup 会去管理其子 View，其中一个管理就是负责子 View 的显示大小。当 ViewGroup 的大小为 wrap_content 时，ViewGroup 就需要对子 View 进行遍历，以便获得所有子 View 的大小，从而来决定自己的大小；而其他模式下则会通过具体的指定值来设置自身的大小。 ViewGroup 在测量时通过遍历所有子 View，从而调用子 View 的 Measure 方法来获得每一个子 View 的测量结果，View的测量就是在这时进行的。  

当子 View 测量完毕后，就需要将子 View 放到合适的位置，这个过程就是 View 的 Layout 过程。 ViewGroup 在执行 Layout 过程时，同样是使用遍历来调用子 View 的 Layout 方法，并指定其具体显示的位置，从而来决定其布局位置。自定义 ViewGroup 时，通常会重写 onLayout() 方法来控制其子 View 显示位置的逻辑。同样，如果需要支持 wrap_content 属性，那么它还必须重写 onMeasure() 方法，与 View 相同。  

## **ViewGroup 的绘制** ##

ViewGroup 通常情况下不需要绘制，因为它本身没有需要绘制的东西，如果不是指定了 ViewGroup 的背景颜色，那么 ViewGroup 的 onDraw() 方法都不会调用。但是， ViewGroup 会使用 dispatchDraw() 方法来绘制其子 View，其过程同样是遍历所有的子 View ，并调用子 View 的绘制方法来完成绘制工作。  

## **自定义 View** ##

在自定义 View 时，通常会去重写 onDraw() 方法来绘制 View 的显示内容。如果该 View 还需要使用 wrap_content 属性那么还必须重写 onMeasure() 方法。另外，通过自定义 attrs 属性，还可以设置新的属性配置值。  

View 中一些重要的回调方法：  

- onFinishInflate(): 从 XML 加载组件后回调；
- onSizeChanged()： 组件大小改变时回调；
- onMeasure()： 回调该方法来进行测量；
- onLayout()： 回调该方法来确定显示的位置；
- onTouchEvent(): 监听到触摸事件时回调；

创建自定义 View 的时候并不需要重写所有的方法，只需要重写特定条件的回调方法即可。  

通常有以下三种方法来实现自定义控件：  

- 继承控件；
- 组合控件；
- 自绘控件。

### **继承控件** ###

继承控件就是继承已有的控件，创建新控件，保留继承的父控件的特性，并且还可以引入新的特性，是一个非常重要的自定义 View 方法，它可以在原生控件的基础上极性拓展，增加新的功能、修改显示的 UI 等。一般来说，可以在 onDraw() 方法中对原生控件行为进行拓展。  

### **组合控件** ###

组合控件就是将系统自带的一些控件组合起来形成新的控件。这种方式通常要继承一个合适的 ViewGroup ，在给它添加指定功能的控件，从而组合成新的复合控件。通过这种方式创建的控件，一般会给它指定一些可以配置的属性，让其具有更强的拓展性。  

### **自绘控件** ###

自绘控件是一个全新的控件，它继承 View 类，并重写它的 onDraw()、onMeasure() 等方法来实现绘制逻辑，同时通过重写 onTouchEvent() 等触控事件来是实现交互逻辑。可引入自定义属性，丰富自定义 View 的可定制性。  

## **自定义 ViewGroup** ##

ViewGroup 存在的目的就是为了对其子 View 进行管理，为其子 View 添加显示、响应的规则。因此，自定义 ViewGroup 通常需要重写 onMeasure() 方法来对子 View 进行测量，重写 onLayout() 方法来确定子 View 的位置，重写 onTouchEvent() 方法来增加响应事件。  

