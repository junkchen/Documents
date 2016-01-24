# RoundedImageViewDemo #

目前在应用开发中，矩形的头像基本没有了，大多是圆形或圆角矩形，本文简简单单轻轻松松帮你搞定圆形或圆角矩形的头像。  

可以自定义控件实现，而本文使用的是第三方开源控件RoundedImageView，改控件支持圆形、椭圆、圆角矩形等，使用非常方便。  

----------

## 添加RoundedImageView依赖 ##

使用RoundedImageView有两种操作方法，实质都是添加库依赖。


方法一： 在Android Studio中，可进入模块设置中添加库依赖。


方法二： 在Moudle的build.gradle中添加如下代码，添加完之后在Build中进行下Make Module操作（编译下Module），使自己添加的依赖生效。     
 
	repositories {
	    mavenCentral()
	}
	
	dependencies {
	    compile 'com.makeramen:roundedimageview:2.2.1'
	}


----------


## Layout中使用 ##

添加了库依赖之后，我们就可以使用该控件了。  

控件属性：  
riv_border_width： 边框宽度  
riv_border_color： 边框颜色  
riv_oval： 是否圆形  
riv_corner_radius： 圆角弧度  
riv_corner_radius_top_left：左上角弧度   
riv_corner_radius_top_right： 右上角弧度  
riv_corner_radius_bottom_left：左下角弧度  
riv_corner_radius_bottom_right：右下角弧度 


	<com.makeramen.roundedimageview.RoundedImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@mipmap/avatar"
            app:riv_border_color="#333333"
            app:riv_border_width="2dp"
            app:riv_oval="true" />

        <com.makeramen.roundedimageview.RoundedImageView
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:scaleType="fitCenter"
            android:src="@mipmap/avatar"
            app:riv_border_color="#333333"
            app:riv_border_width="2dp"
            app:riv_corner_radius="10dp"
            app:riv_mutate_background="true"
            app:riv_oval="false"
            app:riv_tile_mode="repeat" />

        <com.makeramen.roundedimageview.RoundedImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:scaleType="fitCenter"
            android:src="@mipmap/avatar"
            app:riv_border_color="#333333"
            app:riv_border_width="2dp"
            app:riv_corner_radius_top_left="25dp"
            app:riv_corner_radius_bottom_right="25dp"
            app:riv_mutate_background="true"
            app:riv_oval="false"
            app:riv_tile_mode="repeat" />

        <com.makeramen.roundedimageview.RoundedImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:scaleType="fitCenter"
            android:src="@mipmap/avatar"
            app:riv_border_color="#333333"
            app:riv_border_width="2dp"
            app:riv_corner_radius_top_right="25dp"
            app:riv_corner_radius_bottom_left="25dp"
            app:riv_mutate_background="true"
            app:riv_oval="false"
            app:riv_tile_mode="repeat" />

        <com.makeramen.roundedimageview.RoundedImageView
            android:layout_width="96dp"
            android:layout_height="72dp"
            android:scaleType="center"
            android:src="@mipmap/avatar"
            app:riv_border_color="#333333"
            app:riv_border_width="2dp"
            app:riv_corner_radius="25dp"
            app:riv_mutate_background="true"
            app:riv_oval="true"
            app:riv_tile_mode="repeat" />

