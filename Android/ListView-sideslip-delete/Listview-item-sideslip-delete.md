# **仿QQ消息列表item横向滑动删除ListView中item侧滑删除** #

在最近的项目中，我的ListView中item选项是长按删除的效果（Android的通常做法长按或点击），老板觉得这个效果不好，提出要求做类似QQ消息列表横向滑动时删除消息的效果，这种效果确实感觉挺爽的，但是我也没做过，于是就在网上搜索了一番，找到了很多例子，实现了这个效果，但在这个过程中不尽如人意，遇到一些曲折，大多数例子确实实现了效果，但是也存在一些问题没有解决，用着有问题，如item点击无效、item有点击事件时滑动item结束后执行了item的点击事件、item中有按钮时若手指按下在按钮位置时滑动无效等等，自己查了很多终于解决了这些问题，现整理如下供大家参考，如有问题请多多包含。  

本博文实现思路是在滑动item的过程中修改item的leftMargin实现的。先看下最终效果：  

<img src="sideslip-listview.gif" width="450"/>

下面就一步步来实现吧。  


## **一、item的布局** ##

ListView中的item布局有讲究，最外层使用的LinearLayout，设置其orientation属性为horizontal。然后里面主要包含两个视图组件，一个是我们正常显示时的布局，另外一个是滑动时出现的视图如删除按钮。正常显示的视图初始的layout_width设置为match_parent，并且滑动时出现的视图必须指定其layout_width，不要使用match_parent或wrap_parent，否则会获取不到宽度。稍后你会明白的。看本博文完整的item布局。   

### **本例完整的item布局文件item.xml**   ###

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
	android:descendantFocusability="blocksDescendants"
    android:orientation="horizontal">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="8dp">

        <TextView
            android:id="@+id/textView"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="18sp"
            android:text="text" />

        <Button
            android:layout_marginLeft="8dp"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Button"/>
    </LinearLayout>

    <!--必须指定宽度-->
    <TextView
        android:id="@+id/txtv_delete"
        android:layout_width="96dp"
        android:layout_height="match_parent"
        android:gravity="center"
        android:textSize="18sp"
        android:background="#e53935"
        android:textColor="#eeeeee"
        android:text="删除" />
</LinearLayout>
```  

## **二、自定义ListView** ##

需要自定义ListView，继承于ListView。如：    

```java
public class SideslipListView extends ListView {
	...
	public SideslipListView(Context context) {
        super(context);
    }

    public SideslipListView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public SideslipListView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }
	...
}
```  

首先获取屏幕宽度。  

```java
private int mScreenWidth;//屏幕的宽度
...
// 获取屏幕宽度
WindowManager wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
DisplayMetrics dm = new DisplayMetrics();
wm.getDefaultDisplay().getMetrics(dm);
mScreenWidth = dm.widthPixels;
```  

接着重写onTouchEvent(MotionEvent ev)方法,对手指按下、移动、离开分别进行相应处理。如  

```java
@Override
public boolean onTouchEvent(MotionEvent ev) {//事件响应
    switch (ev.getAction()) {
        case MotionEvent.ACTION_DOWN:
            performActionDown(ev);
            break;
        case MotionEvent.ACTION_MOVE:
            performActionMove(ev);
            break;
        case MotionEvent.ACTION_UP:
            performActionUp(ev);
            break;
    }
    return super.onTouchEvent(ev);
}
```  

当手指按下时我们需要获取到按下时所在的item，得到正常显示时的视图，并设置其宽度为屏幕的宽度；另外还需得到删除组件的宽度。如：  

```java
private int mDownX;//手指初次按下的X坐标
private int mDownY;//手指初次按下的Y坐标
private boolean isDeleteShow;//删除组件是否显示
private ViewGroup mPointChild;//手指按下位置的item组件
private int mDeleteWidth;//删除组件的宽度
private LinearLayout.LayoutParams mItemLayoutParams;//手指按下时所在的item的布局参数

...

private void performActionDown(MotionEvent ev) {
	mDownX = (int) ev.getX();
    mDownY = (int) ev.getY();
    if (isDeleteShow) {
        ViewGroup tmpViewGroup = (ViewGroup) getChildAt(pointToPosition(mDownX, mDownY) - getFirstVisiblePosition());
        Log.i(TAG, "*********mPointChild.equals(tmpViewGroup): " + mPointChild.equals(tmpViewGroup));
        if (!mPointChild.equals(tmpViewGroup)) {
            turnNormal();
        }
    }
    //获取当前的item
    mPointChild = (ViewGroup) getChildAt(pointToPosition(mDownX, mDownY) - getFirstVisiblePosition());

    mDeleteWidth = mPointChild.getChildAt(1).getLayoutParams().width;//获取删除组件的宽度
    Log.i(TAG, "**********pointToPosition(x,y): " + pointToPosition(mDownX, mDownY)
            + ", getFirstVisiblePosition() = " + getFirstVisiblePosition()
            + ", mDeleteWidth = " + mDeleteWidth);
    mItemLayoutParams = (LinearLayout.LayoutParams) mPointChild.getChildAt(0).getLayoutParams();

    mItemLayoutParams.width = mScreenWidth;
    mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
}
```  

当手指移动时动态修改正常是显示视图组件的leftMargin,如果删除按钮没有显示，手指向左滑动删除按钮就会根据滑动的距离显示相应的可见宽度。如：    

```java
private boolean performActionMove(MotionEvent ev) {
    int nowX = (int) ev.getX();
    int nowY = (int) ev.getY();
    int diffX = nowX - mDownX;
    if (Math.abs(diffX) > Math.abs(nowY - mDownY) && Math.abs(nowY - mDownY) < 20) {
        if (!isDeleteShow && nowX < mDownX) {//删除按钮未显示时向左滑
            if (-diffX >= mDeleteWidth) {//如果滑动距离大于删除组件的宽度时进行偏移的最大处理
                diffX = -mDeleteWidth;
            }
            mItemLayoutParams.leftMargin = diffX;
            mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
			isAllowItemClick = false;
        } else if (isDeleteShow && nowX > mDownX) {//删除按钮显示时向右滑
            if (diffX >= mDeleteWidth) {
                diffX = mDeleteWidth;
            }
            mItemLayoutParams.leftMargin = diffX - mDeleteWidth;
            mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
			isAllowItemClick = false;
        }
        return true;
    }
    return super.onTouchEvent(ev);
}
```  

当手指离开时，根据移动的距离判断是否显示删除按钮。如：   

```java
private void performActionUp(MotionEvent ev) {
    //如果向左滑出超过隐藏的二分之一就全部显示
    if (-mItemLayoutParams.leftMargin >= mDeleteWidth / 2) {
        mItemLayoutParams.leftMargin = -mDeleteWidth;
        isDeleteShow = true;
        mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
    } else {
        turnNormal();
    }
}
```  

到这里基本上就实现了侧滑时删除按钮的显示和隐藏了。不过还有些问题。 

- **ListView设置了点击事件时左右滑动会触发改事件冲突处理** 

当ListView中的item设置了点击事件时，我们左右滑动时就会触发这个点击事件，那么如何避免呢？在自定义的ListView中设置标志isAllowItemClick，当手指按下时设置其值为isAllowItemClick = true，在手指移动的过程中，若是发生左右移动，则设置其值为isAllowItemClick = false，另外向外公布方法：  

```java
public boolean isAllowItemClick() {
    return isAllowItemClick;
}
```  

当为Listview设置点击事件时这样做就可以避免了。  

```java
mSideslipListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        if (mSideslipListView.isAllowItemClick()) {
            Log.i(TAG, mDataList.get(position) + "被点击了");
            Toast.makeText(MainActivity.this, mDataList.get(position) + "被点击了",
                    Toast.LENGTH_SHORT).show();
        }
    }
});
```  

- **当item中有按钮时手指按下在改位置时滑动无效处理**  

这个跟Android的事件分发机制有关系，不知道可以去仔细看看查查资料。我们可以重写onInterceptTouchEvent(MotionEvent ev)这个方法来解决，若手指按下并且是左右移动时则最该Touch事件进行拦截。如：  

```java
@Override
public boolean onInterceptTouchEvent(MotionEvent ev) {//事件拦截
    switch (ev.getAction()) {
        case MotionEvent.ACTION_DOWN: {
            isAllowItemClick = true;

            //侧滑删除
            mDownX = (int) ev.getX();
            mDownY = (int) ev.getY();
            mPointPosition = pointToPosition(mDownX, mDownY);
            Log.i(TAG, "*******pointToPosition(mDownX, mDownY): " + mPointPosition);
            if (mPointPosition != -1) {
                if (isDeleteShow) {
                    ViewGroup tmpViewGroup = (ViewGroup) getChildAt(mPointPosition - getFirstVisiblePosition());
                    if (!mPointChild.equals(tmpViewGroup)) {
                        turnNormal();
                    }
                }
                //获取当前的item
                mPointChild = (ViewGroup) getChildAt(mPointPosition - getFirstVisiblePosition());

                mDeleteWidth = mPointChild.getChildAt(1).getLayoutParams().width;
                mItemLayoutParams = (LinearLayout.LayoutParams) mPointChild.getChildAt(0).getLayoutParams();

                Log.i(TAG, "*********mItemLayoutParams.height: " + mItemLayoutParams.height +
                        ", mDeleteWidth: " + mDeleteWidth);
                mItemLayoutParams.width = mScreenWidth;
                mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
            }
            break;
        }
        case MotionEvent.ACTION_MOVE: {
            int nowX = (int) ev.getX();
            int nowY = (int) ev.getY();
            int diffX = nowX - mDownX;
            Log.i(TAG, "******dp2px(4): " + dp2px(8) + ", dp2px(8): " + dp2px(8) +
                    ", density: " + getContext().getResources().getDisplayMetrics().density);
            if (Math.abs(diffX) > dp2px(4) || Math.abs(nowY - mDownY) > dp2px(4)) {
                return true;//避免子布局中有点击的控件时滑动无效
            }
            break;
        }
    }
    return super.onInterceptTouchEvent(ev);
}

public float dp2px(int dp) {
    return TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, dp,
            getContext().getResources().getDisplayMetrics());
}
```  

到此，我们的自定义ListView基本上就搞定了。最后贴上我的自定义SideslipListView的完整源码。  

### **完整的自定义 SideslipListView.java** ###

```java
package com.junkchen.toucheventtest;

import android.content.Context;
import android.util.AttributeSet;
import android.util.DisplayMetrics;
import android.util.Log;
import android.util.TypedValue;
import android.view.MotionEvent;
import android.view.ViewGroup;
import android.view.WindowManager;
import android.widget.LinearLayout;
import android.widget.ListView;

/**
 * Created by JunkChen on 2016/3/29 0029.
 */
public class SideslipListView extends ListView {
    private static final String TAG = "SideslipListView";

    private int mScreenWidth;//屏幕的宽度
    private boolean isDeleteShow;//删除组件是否显示
    private ViewGroup mPointChild;//手指按下位置的item组件
    private int mDeleteWidth;//删除组件的宽度
    private LinearLayout.LayoutParams mItemLayoutParams;//手指按下时所在的item的布局参数
    private int mDownX;//手指初次按下的X坐标
    private int mDownY;//手指初次按下的Y坐标

    private int mPointPosition;//手指按下位置所在的item位置
    private boolean isAllowItemClick;//是否允许item点击

    public SideslipListView(Context context) {
        super(context);
        init(context);
    }

    public SideslipListView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(context);
    }

    public SideslipListView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(context);
    }

    private void init(Context context) {
        // 获取屏幕宽度
        WindowManager wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
        DisplayMetrics dm = new DisplayMetrics();
        wm.getDefaultDisplay().getMetrics(dm);
        mScreenWidth = dm.widthPixels;
        Log.i(TAG, "***********mScreenWidth: " + mScreenWidth);
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {//事件拦截
        switch (ev.getAction()) {
            case MotionEvent.ACTION_DOWN: {
                isAllowItemClick = true;

                //侧滑删除
                mDownX = (int) ev.getX();
                mDownY = (int) ev.getY();
                mPointPosition = pointToPosition(mDownX, mDownY);
                Log.i(TAG, "*******pointToPosition(mDownX, mDownY): " + mPointPosition);
                if (mPointPosition != -1) {
                    if (isDeleteShow) {
                        ViewGroup tmpViewGroup = (ViewGroup) getChildAt(mPointPosition - getFirstVisiblePosition());
                        if (!mPointChild.equals(tmpViewGroup)) {
                            turnNormal();
                        }
                    }
                    //获取当前的item
                    mPointChild = (ViewGroup) getChildAt(mPointPosition - getFirstVisiblePosition());

                    mDeleteWidth = mPointChild.getChildAt(1).getLayoutParams().width;
                    mItemLayoutParams = (LinearLayout.LayoutParams) mPointChild.getChildAt(0).getLayoutParams();

                    Log.i(TAG, "*********mItemLayoutParams.height: " + mItemLayoutParams.height +
                            ", mDeleteWidth: " + mDeleteWidth);
                    mItemLayoutParams.width = mScreenWidth;
                    mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
                }
                break;
            }
            case MotionEvent.ACTION_MOVE: {
                int nowX = (int) ev.getX();
                int nowY = (int) ev.getY();
                int diffX = nowX - mDownX;
                Log.i(TAG, "******dp2px(4): " + dp2px(8) + ", dp2px(8): " + dp2px(8) +
                        ", density: " + getContext().getResources().getDisplayMetrics().density);
                if (Math.abs(diffX) > dp2px(4) || Math.abs(nowY - mDownY) > dp2px(4)) {
                    return true;//避免子布局中有点击的控件时滑动无效
                }
                break;
            }
        }
        return super.onInterceptTouchEvent(ev);
    }

    public float dp2px(int dp) {
        return TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, dp,
                getContext().getResources().getDisplayMetrics());
    }

    @Override
    public boolean onTouchEvent(MotionEvent ev) {//事件响应
        switch (ev.getAction()) {
            case MotionEvent.ACTION_DOWN:
                performActionDown(ev);
                break;
            case MotionEvent.ACTION_MOVE:
                performActionMove(ev);
                break;
            case MotionEvent.ACTION_UP:
                performActionUp(ev);
                break;
        }
        return super.onTouchEvent(ev);
    }

    private void performActionDown(MotionEvent ev) {
        mDownX = (int) ev.getX();
        mDownY = (int) ev.getY();
        if (isDeleteShow) {
            ViewGroup tmpViewGroup = (ViewGroup) getChildAt(pointToPosition(mDownX, mDownY) - getFirstVisiblePosition());
            Log.i(TAG, "*********mPointChild.equals(tmpViewGroup): " + mPointChild.equals(tmpViewGroup));
            if (!mPointChild.equals(tmpViewGroup)) {
                turnNormal();
            }
        }
        //获取当前的item
        mPointChild = (ViewGroup) getChildAt(pointToPosition(mDownX, mDownY) - getFirstVisiblePosition());

        mDeleteWidth = mPointChild.getChildAt(1).getLayoutParams().width;//获取删除组件的宽度
        Log.i(TAG, "**********pointToPosition(x,y): " + pointToPosition(mDownX, mDownY)
                + ", getFirstVisiblePosition() = " + getFirstVisiblePosition()
                + ", mDeleteWidth = " + mDeleteWidth);
        mItemLayoutParams = (LinearLayout.LayoutParams) mPointChild.getChildAt(0).getLayoutParams();

        mItemLayoutParams.width = mScreenWidth;
        mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
    }

    private boolean performActionMove(MotionEvent ev) {
        int nowX = (int) ev.getX();
        int nowY = (int) ev.getY();
        int diffX = nowX - mDownX;
        if (Math.abs(diffX) > Math.abs(nowY - mDownY) && Math.abs(nowY - mDownY) < 20) {
            if (!isDeleteShow && nowX < mDownX) {//删除按钮未显示时向左滑
                if (-diffX >= mDeleteWidth) {//如果滑动距离大于删除组件的宽度时进行偏移的最大处理
                    diffX = -mDeleteWidth;
                }
                mItemLayoutParams.leftMargin = diffX;
                mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
                isAllowItemClick = false;
            } else if (isDeleteShow && nowX > mDownX) {//删除按钮显示时向右滑
                if (diffX >= mDeleteWidth) {
                    diffX = mDeleteWidth;
                }
                mItemLayoutParams.leftMargin = diffX - mDeleteWidth;
                mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
                isAllowItemClick = false;
            }
            return true;
        }
        return super.onTouchEvent(ev);
    }

    private void performActionUp(MotionEvent ev) {
        //如果向左滑出超过隐藏的二分之一就全部显示
        if (-mItemLayoutParams.leftMargin >= mDeleteWidth / 2) {
            mItemLayoutParams.leftMargin = -mDeleteWidth;
            isDeleteShow = true;
            mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
        } else {
            turnNormal();
        }
    }

    /**
     * 转换为正常隐藏情况
     */
    public void turnNormal() {
        mItemLayoutParams.leftMargin = 0;
        mPointChild.getChildAt(0).setLayoutParams(mItemLayoutParams);
        isDeleteShow = false;
    }

    /**
     * 是否允许Item点击
     *
     * @return
     */
    public boolean isAllowItemClick() {
        return isAllowItemClick;
    }
}
```
  

## **三、自定义SideslipListView使用** ##

在布局中的使用。

### ** 本例完整的布局文件 activity_main.xml ** ###  

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center_horizontal"
    tools:context="com.junkchen.toucheventtest.MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_margin="8dp"
        android:text="ListView item侧滑删除" />

    <!--使用自定义的SideslipListView-->
    <com.junkchen.toucheventtest.SideslipListView
        android:id="@+id/sideslipListView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
    </com.junkchen.toucheventtest.SideslipListView>
</LinearLayout>
```

### ** 本例完整的Java文件 MainActivity.java **  ###

```java
package com.junkchen.toucheventtest;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.view.View;
import android.view.ViewGroup;
import android.widget.AdapterView;
import android.widget.BaseAdapter;
import android.widget.TextView;
import android.widget.Toast;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    private static final String TAG = "MainActivity";
    private SideslipListView mSideslipListView;

    /**
     * 初始化数据
     */
    private ArrayList<String> mDataList = new ArrayList<String>() {
        {
            for (int i = 0; i < 50; i++) {
                add("ListView item  " + i);
            }
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mSideslipListView = (SideslipListView) findViewById(R.id.sideslipListView);
        mSideslipListView.setAdapter(new CustomAdapter());//设置适配器
        //设置item点击事件
        mSideslipListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                if (mSideslipListView.isAllowItemClick()) {
                    Log.i(TAG, mDataList.get(position) + "被点击了");
                    Toast.makeText(MainActivity.this, mDataList.get(position) + "被点击了",
                            Toast.LENGTH_SHORT).show();
                }
            }
        });
        //设置item长按事件
        mSideslipListView.setOnItemLongClickListener(new AdapterView.OnItemLongClickListener() {
            @Override
            public boolean onItemLongClick(AdapterView<?> parent, View view, int position, long id) {
                if (mSideslipListView.isAllowItemClick()) {
                    Log.i(TAG, mDataList.get(position) + "被长按了");
                    Toast.makeText(MainActivity.this, mDataList.get(position) + "被长按了",
                            Toast.LENGTH_SHORT).show();
                    return true;//返回true表示本次事件被消耗了，若返回
                }
                return false;
            }
        });
    }

    /**
     * 自定义ListView适配器
     */
    class CustomAdapter extends BaseAdapter {
        @Override
        public int getCount() {
            return mDataList.size();
        }

        @Override
        public Object getItem(int position) {
            return mDataList.get(position);
        }

        @Override
        public long getItemId(int position) {
            return position;
        }

        @Override
        public View getView(int position, View convertView, ViewGroup parent) {
            ViewHolder viewHolder;
            if (null == convertView) {
                convertView = View.inflate(MainActivity.this, R.layout.item, null);
                viewHolder = new ViewHolder();
                viewHolder.textView = (TextView) convertView.findViewById(R.id.textView);
                viewHolder.txtv_delete = (TextView) convertView.findViewById(R.id.txtv_delete);
                convertView.setTag(viewHolder);
            } else {
                viewHolder = (ViewHolder) convertView.getTag();
            }
            viewHolder.textView.setText(mDataList.get(position));
            final int pos = position;
            viewHolder.txtv_delete.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Toast.makeText(MainActivity.this, mDataList.get(pos) + "被删除了",
                            Toast.LENGTH_SHORT).show();
                    mDataList.remove(pos);
                    notifyDataSetChanged();
                    mSideslipListView.turnNormal();
                }
            });
            return convertView;
        }
    }

    class ViewHolder {
        public TextView textView;
        public TextView txtv_delete;
    }
}
```

上面这个应用就不详细介绍了，注释写得比较清楚。可以自行新建工程测试，想要完整源码请加群联系群主。  

如有问题欢迎加qq群讨论学习：**365532949**  
HomePage：**[http://junkchen.com](http://junkchen.com)**