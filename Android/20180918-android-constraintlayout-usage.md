# Android 约束布局 ConstraintLayout 的使用

## ConstraintLayout 简介

*ConstraintLayout* 是一个可以灵活地放置和排列控件（Widget）的容器（ViewGroup）。

可译为约束布局，在2016年Google I/O大会上发布。当布局嵌套过多时会出现一些性能问题。之前我们可以去通过RelativeLayout或者GridLayout来减少这种布局嵌套的问题。现在，我们可以改用ConstraintLayout来减少布局的层级结构。ConstraintLayout相比RelativeLayout，其性能更好，也更容易使用，结合Android Studio的布局编辑器可以实现拖拽控件来编写布局等等。    

## 相对位置

要在ConstraintLayout中确定view的位置,必须至少添加一个水平和垂直的约束。每一个约束表示到另一个view，父布局，或者不可见的参考线的连接或者对齐。如果水平或者垂直方向上没有约束，那么其位置就是0。  

### 相对位置的属性

```txt
layout_constraintLeft_toLeftOf // 该布局的左边位于指定布局的左边
layout_constraintLeft_toRightOf // 该布局的左边位于指定布局的右边
layout_constraintRight_toLeftOf // 该布局的右边边位于指定布局的左边
layout_constraintRight_toRightOf // 该布局的右边边位于指定布局的右边
layout_constraintTop_toTopOf
layout_constraintTop_toBottomOf
layout_constraintBottom_toTopOf
layout_constraintBottom_toBottomOf
layout_constraintBaseline_toBaselineOf //
layout_constraintStart_toEndOf
layout_constraintStart_toStartOf
layout_constraintEnd_toStartOf
layout_constraintEnd_toEndOf
```

这些属性的值即可以是parent，也可以是某个view的id。  

### 居中显示

```txt
app:layout_constraintLeft_toLeftOf="parent"
app:layout_constraintRight_toRightOf="parent"
```

即view的左边对齐父布局的左边，view的右边对齐父布局的右边，除非这个view的大小刚好充满整个父布局；否则的话，就是水平居中显示了。我们可以理解为有两个力，它们左右互搏，view只能给扯到中间了。  

## 间距

设置组件间的间距可以使用如下属性：  

- android:layout_marginStart
- android:layout_marginEnd
- android:layout_marginLeft
- android:layout_marginTop
- android:layout_marginRight
- android:layout_marginBottom

间距只能是一个正值或者0，也可以引用一个 *Dimension* 值。  

## 尺寸约束

view 中使用 warp_content 或者固定值等等是没有问题的。但是 ConstraintLayout 中不支持 MATCH_PARENT 这个值，如果需要实现跟 MATCH_PARENT 同样的效果，可以使用 0dp 来代替，其表示 MATCH_CONSTRAINT,即适应约束。其跟 MATCH_PARENT 还是有区别的。  

```xml
<Button
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent" />
```

如上面的 Button 的宽度可充满父布局。  

## 宽高比

在ConstraintLayout中，还可以将宽定义成高的一个比例或者高定义成宽的比率。首先，需要将宽或者高设置为0dp（即MATCH_CONSTRAINT），即要适应约束条件。然后通过layout_constraintDimensionRatio属性设置一个比率即可。这个比率可以是一个浮点数，表示宽度和高度之间的比率；也可以是“宽度：高度”形式的比率。  

```xml
<Button
    android:layout_width="wrap_content"
    android:layout_height="0dp"
    android:text="---width:height=2:1---"
    app:layout_constraintDimensionRatio="2:1"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent" />
```

如果宽和高都设置为0dp（MATCH_CONSTRAINT），那么layout_constraintDimensionRatio的值需要先加一个"W,"或者"H,"来表示约束宽度或高度。如下：  

```xml
<Button
    android:layout_width="0dp"
    android:layout_height="0dp"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintDimensionRatio="H,16:9"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent" />
```

这里例子是说，首先宽度将满足父布局的约束，然后将按照16：9的比例设置高度。  

## 百分比宽高

ConstraintLayout还能使用百分比来设置view的宽高。
要使用百分比，宽或高同样要设置为0dp（MATCH_CONSTRAINT）。
然后设置以下属性即可：

```txt
app:layout_constraintWidth_default="percent" //设置宽为百分比
app:layout_constraintWidth_percent="0.3" //0到1之间的值
或
app:layout_constraintHeight_default="percent" //设置高为百分比
app:layout_constraintHeight_percent="0.3" //0到1之间的值
```

eg.:  

```xml
<Button
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintWidth_default="percent"
    app:layout_constraintWidth_percent="0.5" />
```

## 位置偏向

如果想让view的位置偏向某一侧，可以使用以下的两个属性来设置：  

```txt
layout_constraintHorizontal_bias  //水平偏向
layout_constraintVertical_bias  //竖直偏向
```

其值同样也是0到1之间。
比如，以下例子为横向偏向左侧30%，默认的居中效果就是50％：  

```xml
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:layout_constraintHorizontal_bias="0.3"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent" />
```

## 权重

LinearLayout中可以设置权重，ConstraintLayout同样也有这玩意。
通过设置以下两个属性：  

```txt
app:layout_constraintHorizontal_weight //水平权重
app:layout_constraintVertical_weight //竖直权重
```

然后将相连的view两两约束好即可。如下：  

```xml
<Button
    android:id="@+id/button1"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:text="weight=1"
    app:layout_constraintHorizontal_weight="1"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toLeftOf="@id/button2" />
<Button
    android:id="@+id/button2"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:text="weight=2"
    app:layout_constraintHorizontal_weight="2"
    app:layout_constraintLeft_toRightOf="@id/button1"
    app:layout_constraintRight_toLeftOf="@id/button3" />
<Button
    android:id="@+id/button3"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:text="weight=2"
    app:layout_constraintHorizontal_weight="2"
    app:layout_constraintLeft_toRightOf="@id/button2"
    app:layout_constraintRight_toRightOf="parent" />
```

## 链

上面这种将相连的view两两约束好的实际上就形成了链。在ConstraintLayout中可以实现各种不同的链，权重链是其中一种。整个链由链中的第一个view（链头）上设置的属性控制。

可以通过以下属性来设置链的样式：  

```txt
app:layout_constraintHorizontal_chainStyle="spread|spread_inside|packed"
```

1. *Spread Chain*

	默认样式就是spread，所以可以不用设置。然后宽或高非0即可。

2. *Spread Inside chain*

	这里设置以下属性
	```txt
	app:layout_constraintHorizontal_chainStyle="spread_inside"
	```

3. *Weighted Chain*

	即权重链，具体可以查看上一小节。
	Weighter Chain = spread + 宽或高为0 + 权重值

4. *Packed Chain*

	Packed是一种view聚拢起来的效果，这里设置以下属性
	```txt
	app:layout_constraintHorizontal_chainStyle="packed"
	```

5. *Packed Chain with Bias*

	就是Packed Chain再加一个偏向属性，偏向属性可以看下前面的内容。

## Guideline 辅助线

Guideline可以用来辅助布局，通过Guideline能创建出一条条的水平线或者垂直线，该线不会显示到界面上，但是能够利用这些线条来添加约束去完成界面的布局。  

Guideline主要的属性有：  

```txt
// 参考线是水平或垂直
android:orientation="horizontal|vertical"
// 参考线距离左边（垂直线）或顶部（水平线）的距离
app:layout_constraintGuide_begin="30dp"
// 参考线距离右边（垂直线）或底部（水平线）的距离 
app:layout_constraintGuide_end="30dp"
// 参考线距离顶部（水平线）或左边（垂直线）的比例
app:layout_constraintGuide_percent="0.5"
```

## 圆形放置

ConstraintLayout 可以约束一个组件的中心相对于另一个组件的中心的角度和距离。这可以在一个圆上放置控件。可以使用如下属性：  

- layout_constraintCircle: 另一个控件 id 的引用
- layout_constraintCircleRadius： 到另一个控件中心的距离
- layout_constraintCircleAngle： 位于另一个控件的角度，从 0 到 360 


```xml
<Button
    android:id="@+id/button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent" />
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:layout_constraintCircle="@id/button"
    app:layout_constraintCircleAngle="45"
    app:layout_constraintCircleRadius="96dp" />
```


