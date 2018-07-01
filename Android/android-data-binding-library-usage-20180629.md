# Data Binding 库的使用

The Data Binding Library is a support library that allows you to bind UI components in your layouts to data sources in your app using a declarative format rather than programmatically.  

Binding components in the layout file lets you remove many UI framework calls in your activities, making them simpler and easier to maintain. This can also improve your app's performance and help prevent memory leaks and null pointer exceptions.  

## Get started

### 环境配置

如果你的应用中要使用 data binding，进行配置即可，在 Module 下的 **build.gradle*** 中添加 **dataBinding** 元素，如下所示：  

```gradle
android {
	...
	dataBinding {
		enabled = true
	}
}
```

### 新 data binding 编译器配置

在 *gradle.properties* 文件中添加如下配置，打开新的 data binding 编译器：  

```
android.databinding.enableV2=true
```

## 布局和绑定表达式

Data Binding 布局文件以 **root*** 为根节点，包含 **data** 和 **view** 两个子节点。示例布局如下所示：  

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"/>
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.lastName}"/>
   </LinearLayout>
</layout>
```

**data** 中的 **user** 变量描述一个属性，可以在布局中使用。  

```xml
<variable name="user" type="com.example.User" />
```

布局中的属性表达式语法使用“*@{}*”。如，给 TextView 的 text 设置了一个 user 变量的 firstName 属性。  

```xml
<TextView android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:text="@{user.firstName}" />
```

### 数据对象

创建一个 User 实体：  

```java
public class User {
	public final String firstName;
	public final String lastName;
	public User(String firstName, String lastName) {
	  this.firstName = firstName;
	  this.lastName = lastName;
	}
}
```

### 绑定数据

每一个 layout 文件会生成一个 binding 类。通常，类名以 layout 文件名为基础。如 layout 文件名为 *activity_main.xml* ，则生成的类名为 *ActivityMainBinding* 。这个类持有从布局属性（如 user 变量）到布局视图所有的绑定。推荐创建一个 binding 去加载布局，如下所示：  

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
	User user = new User("Test", "User");
	binding.setUser(user);
}
```

在运行时，app 会在 UI 里显示 Test user。也可以通过 *LayoutInflater* 获得 view 。  

```java
ActivityMainBinding binding = ActivityMainBinding.inflate(getLayoutInflater());
```

如果你使用 data binding 时包含 Fragment，ListView，RecyclerView 适配器，你可能更愿意使用 binding 类的 inflate() 方法。如下：  

```java
ListItemBinding binding = ListItemBinding.inflate(layoutInflater, viewGroup, false);
// or
ListItemBinding binding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, viewGroup, false);
```

### 表达式语法

#### 公共特性

你可以在表达式里使用下面的操作符和关键字：  

- Mathematical + - / * %
- String concatenation +
- Logical && ||
- Binary & | ^
- Unary + - ! ~
- Shift >> >>> <<
- Comparison == > < >= <=
- *instanceof*
- Grouping ()
- Literals - character, String, numeric, null
- Cast
- Method calls
- Field access
- Array access []
- Ternary operator ?:

例：  

```xml
android:text="@{String.valueOf(index + 1)}"
android:visibility="@{age < 13 ? View.GONE : View.VISIBLE}"
android:transitionName='@{"image_" + id}'
```

#### Null 操作符

如果一个对象不为空，那么 null 操作符 （*??*）会选择左边的对象， 否则选择右边的对象。  

```xml
android:text="@{user.displayName ?? user.lastName}"
```

这个功能也可以使用三目运算实现：  

```xml
android:text="@{user.displayName != null ? user.displayName : user.lastName}"
```

#### 属性引用

一个表达式可以通过下面的格式应用一个类的属性。  

```xml
android:text="@{user.lastName}"
```

#### 避免空指针异常

生成的 data binding 会自动检查 null 值从而避免空指针异常。例如，对于表达式 *@{user.name}*，如果 user 是 null ，user.name 会默认它的值是 null 。如果 user.age 类型是 int ，那么它的值默认是 0。  

#### 集合

通用集合，如 array，list，sparse list，map，可以使用 [] 操作符方便的进行访问。  

```xml
<data>
    <import type="android.util.SparseArray"/>
    <import type="java.util.Map"/>
    <import type="java.util.List"/>
    <variable name="list" type="List&lt;String&gt;"/>
    <variable name="sparse" type="SparseArray&lt;String&gt;"/>
    <variable name="map" type="Map&lt;String, String&gt;"/>
    <variable name="index" type="int"/>
    <variable name="key" type="String"/>
</data>
…
android:text="@{list[index]}"
…
android:text="@{sparse[index]}"
…
android:text="@{map[key]}"
```

#### String 值

你可以使用单引号去包含一个属性值，在表达式中就可以使用双引号，像下面这样：  

```xml
android:text='@{map["firstName"]}'
```

也可以使用双引号包含属性值，表达式的字符串则应该使用 ` 包围：  

```xml
android:text="@{map[`firstName`]}"
```

#### 资源

在表达式中可以使用如下语法访问资源：  

```xml
android:padding="@{large? @dimen/largePadding : @dimen/smallPadding}"
```

格式化字符串：  

```xml
android:text="@{@string/nameFormat(firstName, lastName)}"
android:text="@{@plurals/banana(bananaCount)}"

Have an orange
Have %d oranges
android:text="@{@plurals/orange(orangeCount, orangeCount)}"
```

类型 | 正常引用 | 表达式引用 
------- | ------- | -------
String[] | @array | @stringArray
int[] | @array | @intArray
TypedArray | @array | @typedArray
Animator | @animator | @animator
StateListAnimator | @animator | @stateListAnimator
color int | @color | @color
ColorStateList | @color | @colorStateList

### 事件处理

Data Binding 允许你写一个表达式来处理 view 分发的事件（如，onClick() 方法）。属性名有监听器的方法名决定，如，View.OnClickListener 有一个方法 onClick() ，所以这个事件的属性为 android:onClick 。  

有一些专门的点击事件处理器用于区别 android:onClick，避免冲突。你可以使用如下这些类型来避免冲突：  

类 | 监听器 | 属性
------- | ------- | -------
SearchView | setOnSearchClickListener(View.OnClickListener) | android:onSearchClick
ZoomControls | setOnZoomInClickListener(View.OnClickListener) | android:onZoomIn
ZoomControls | setOnZoomOutClickListener(View.OnClickListener) | android:onZoomOut

你可以使用如下机制去处理一个事件：  

- **方法引用**： 
- **监听器绑定**：

#### 方法引用

事件可以直接封装到一个处理器方法中，就像 android:onClick 可以直接声明到一个 Activity 中。如果方法不存在或者不正确会接收到一个编译时错误。  

方法引用与监听器绑定最主要的区别是，实际上监听器是在数据绑定时创建，而不是事件触发时创建。如果你喜欢在事件发生时计算表达式，你应该使用监听器绑定。  

声明一个事件，使用正常的表达式，使用值方法名调用。  

```java
public class MyHandlers {
    public void onClickFriend(View view) { ... }
}

// 并在 onCreate() 中绑定
binding.setHandlers(new MyHandlers());
```

绑定表达式可以为一个 view 的点击监听器声明一个 onClickFriend() 方法，如下所示：  

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="handlers" type="com.example.MyHandlers"/>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"
           android:onClick="@{handlers::onClickFriend}"/>
   </LinearLayout>
</layout>
```

#### 监听器绑定

监听器绑定的表达式是在事件发生的时候才运行。  

在方法引用中，方法的参数必须与事件监听器的参数参数一致。而在监听器绑定中，只需要你期望的返回值与监听器返回的值一致即可（除非期望是空）。如下例：  

```java
public class Presenter {
    public void onSaveClick(Task task){}
}
```

然后你可以绑定点击事件到 onSaveClick() 方法中，如：  

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable name="task" type="com.android.example.Task" />
        <variable name="presenter" type="com.android.example.Presenter" />
    </data>
    <LinearLayout android:layout_width="match_parent" android:layout_height="match_parent">
        <Button android:layout_width="wrap_content" android:layout_height="wrap_content"
        android:onClick="@{() -> presenter.onSaveClick(task)}" />
    </LinearLayout>
</layout>
```

在上面的示例中，我们没有定义传入 onClick(View) 的 view 参数。监听器绑定中为监听器参数提供了来个两种选择：你可以忽略方法的所有参数也可以为方法的所有参数命名。如果你命名了参数，那么你可以在表达式中使用他们。上面的例子可以写成下面这样：  

```xml
android:onClick="@{(view) -> presenter.onSaveClick(task)}"
```  

如果你想要使用参数，也可以这样做：  

```java
public class Presenter {
    public void onSaveClick(View view, Task task){}
}
```

```xml
android:onClick="@{(theView) -> presenter.onSaveClick(theView, task)}"
```

多个参数也可以使用 Lambda 表达式：  

```java
public class Presenter {
    public void onCompletedChanged(Task task, boolean completed){}
}
```

```xml
<CheckBox android:layout_width="wrap_content" 
		 android:layout_height="wrap_content"
      android:onCheckedChanged="@{(cb, isChecked) -> presenter.completeChanged(task, isChecked)}" />
```

如果你监听的事件返回值不是 void，你的表达式必须返回一个相同类型的值。如，你监听一个 long click 事件，你的表达式必须返回 boolean 。  

```java
public class Presenter {
    public boolean onLongClick(View view, Task task){}
}
```

```xml
android:onLongClick="@{(theView) -> presenter.onLongClick(theView, task)}"
```

如果表达式因为 null 对象无法进行估值，data binding 会返回该类型的默认值。如，引用类型是 null ， int 是 0，boolean 是 false 等。  

如果你需要通过一个判定去使用一个表达式，你可以使用 void 标记。  

```xml
android:onClick="@{(v) -> v.isVisible() ? doSomething() : void}"
```

### Imports(导入), variables(变量), and includes(包含)

Data Binding 库提供了如Imports, variables, 和 includes 等特性。Imports 可以使你很方便地在布局文件中使用一个类的引用；Variables 允许你在绑定的表达式中描述一个属性；Includes 可以让你重用复杂的布局。  

#### Import

data 结点中可以包含0个或多个 import 结点。如下面的例子在表达式中国呢引用 View 的 VISIBLE 和 GONE 常量。  

```xml
<data>
    <import type="android.view.View"/>
</data>

<TextView
   android:text="@{user.lastName}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"
   android:visibility="@{user.isAdult ? View.VISIBLE : View.GONE}"/>

```

也可以给类型起别名。  

```xml
<import type="android.view.View"/>
<import type="com.example.real.estate.View"
        alias="Vista"/>
```

访问静态域。  

```xml
<data>
    <import type="com.example.MyStringUtils"/>
    <variable name="user" type="com.example.User"/>
</data>
…
<TextView
   android:text="@{MyStringUtils.capitalize(user.lastName)}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

#### Variable

data 内可以使用多个 variable 。  

```xml
<data>
    <import type="android.graphics.drawable.Drawable"/>
    <variable name="user" type="com.example.User"/>
    <variable name="image" type="Drawable"/>
    <variable name="note" type="String"/>
</data>
```

#### Includes

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:bind="http://schemas.android.com/apk/res-auto">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <include layout="@layout/name"
           bind:user="@{user}"/>
       <include layout="@layout/contact"
           bind:user="@{user}"/>
   </LinearLayout>
</layout>
```

## 使用可观察的数据对象

Data Binding 库有三种可观察的类：objects，fields 和 collections。当这三种类型的数据绑定到 UI 上后，数据属性发生变化，UI 也会自动更新。  

### 可观察的 field

```java
private static class User {
    public final ObservableField<String> firstName = new ObservableField<>();
    public final ObservableField<String> lastName = new ObservableField<>();
    public final ObservableInt age = new ObservableInt();
}

// 访问值
user.firstName.set("Google");
int age = user.age.get();
```

### 可观察的 collection

```java
ObservableArrayMap<String, Object> user = new ObservableArrayMap<>();
user.put("firstName", "Google");
user.put("lastName", "Inc.");
user.put("age", 17);
```

```xml
<data>
    <import type="android.databinding.ObservableMap"/>
    <variable name="user" type="ObservableMap<String, Object>"/>
</data>
…
<TextView
    android:text="@{user.lastName}"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"/>
<TextView
    android:text="@{String.valueOf(1 + (Integer)user.age)}"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"/>
```

### 可观察的 object

一个类可以继承 Observable ，Data Binding 库的 BaseObservable 类实现监听器的注册机制，一个类实现了它就具备了当属性改变时进行通知的能力，在 getter 上声明 Bindable 注解，在 setter 方法上调用 notifyPropertyChanged() 方法。  

```java
private static class User extends BaseObservable {
    private String firstName;
    private String lastName;

    @Bindable
    public String getFirstName() {
        return this.firstName;
    }

    @Bindable
    public String getLastName() {
        return this.lastName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
        notifyPropertyChanged(BR.firstName);
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
        notifyPropertyChanged(BR.lastName);
    }
}
```

Data Binding 会生成一个名为 BR 的类，包含所有绑定的资源id。  

## 生成绑定类

Data Binding 库生成绑定的类用于访问布局中变量和视图。生成的类可以自定义，都继承自 ViewDataBinding 类。  

### 创建绑定对象

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  MyLayoutBinding binding = MyLayoutBinding.inflate(getLayoutInflater());
}

MyLayoutBinding binding = MyLayoutBinding.inflate(getLayoutInflater(), viewGroup, false);

MyLayoutBinding binding = MyLayoutBinding.bind(viewRoot);

ViewDataBinding binding = DataBindingUtil.inflate(LayoutInflater, layoutId,
    parent, attachToParent);
ViewDataBinding binding = DataBindingUtil.bindTo(viewRoot, layoutId);

ListItemBinding binding = ListItemBinding.inflate(layoutInflater, viewGroup, false);
// or
ListItemBinding binding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, viewGroup, false);
```

### 自定义绑定的类名

绑定的类名默认以布局文件名为基础，首字母大写，去掉下划线，以 Binding 结尾。生成的类放在当前模块的 databinding 包下。如布局文件 contact_item.xml 生成的相应的类的名为 ContactItemBinding 。  

绑定的类名和存储位置可以通过 data 元素中的 class 属性进行调整。如下面的布局在前的模块的 databinding 包下生成一个 ContactItem 绑定类。  

```xml
<data class="ContactItem">
    …
</data>
```

也可以指定包名，相对路径或绝对路径。  

```xml
// 当前模块下
<data class=".ContactItem">
    …
</data>

// 指定全包名
<data class="com.example.ContactItem">
    …
</data>
```

## 绑定 Adapter

#### 指定自定义的方法名

一些属性没有匹配的名字，可以使用 BindingMethods 注解指定方法名。  

```xml
@BindingMethods({
       @BindingMethod(type = "android.widget.ImageView",
                      attribute = "android:tint",
                      method = "setImageTintList"),
})
```

一般不需要自己指定，Android 已经自动生成了相应的名字。  

#### 提供自定义的逻辑

一些属性需要自定义的逻辑，可以使用 BindingAdapter 注解来完成。  

```java
@BindingAdapter("android:paddingLeft")
public static void setPaddingLeft(View view, int padding) {
  view.setPadding(padding,
                  view.getPaddingTop(),
                  view.getPaddingRight(),
                  view.getPaddingBottom());
}
```

```java
@BindingAdapter({"imageUrl", "error"})
public static void loadImage(ImageView view, String url, Drawable error) {
  Picasso.with(view.getContext()).load(url).error(error).into(view);
}
```

```xml
<ImageView app:imageUrl="@{venue.imageUrl}" app:error="@{@drawable/venueError}" />
```

#### 自定义转换

```xml
<View
   android:background="@{isError ? @color/red : @color/white}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

使用 BindingConversion 注解进行转换。  

```java
@BindingConversion
public static ColorDrawable convertColorToDrawable(int color) {
  return new ColorDrawable(color);
}
```

## 绑定视图布局到架构组件上

### 使用 LiveData 在数据变化时通知 UI

你可以使用 LiveData 在数据改变时自动通知 UI 更新。  

在绑定类中使用 LiveData，你需要指定 LiveData 对象的生命周期所有者。  

```java
class ViewModelActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        // Inflate view and obtain an instance of the binding class.
        UserBinding binding = DataBindingUtil.setContentView(this, R.layout.user);

        // Specify the current activity as the lifecycle owner.
        binding.setLifecycleOwner(this);
    }
}
```

使用 ViewModel 转换数据。  

```java
class ScheduleViewModel extends ViewModel {
    LiveData username;

    public ScheduleViewModel() {
        String result = Repository.userName;
        userName = Transformations.map(result, result -> result.value);
    }
}
```

### 使用 ViewModel 去管理 UI 相关的数据

在 Data Binding 中使用 ViewModel 组件，你必须实例化这个继承于 ViewModel 的子类，从绑定类中获取实例，并将 ViewModel 组件声明到绑定类的属性上。  

```java
class ViewModelActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        // Obtain the ViewModel component.
        UserModel userModel = ViewModelProviders.of(getActivity())
                                                  .get(UserModel.class);

        // Inflate view and obtain an instance of the binding class.
        UserBinding binding = DataBindingUtil.setContentView(this, R.layout.user);

        // Assign the component to a property in the binding class.
        binding.viewmodel = userModel;
    }
}
```

在你的布局中，使用 Lambda 表达式将你在 ViewModel 中的属性和方法应用到相应的 view 上。  

```xml
<CheckBox
    android:id="@+id/rememberMeCheckBox"
    android:checked="@{viewmodel.rememberMe}"
    android:onCheckedChanged="@{() -> viewmodel.rememberMeChanged()}" />
```

### 使用可观察的 ViewModel 在绑定的适配器上进行更多控制

要实现一个可观察的 ViewModel 组件，你必须创建一个类继承自 ViewModel 并且实现 Observable 接口。你可以提供自定义的逻辑，使用 addOnPropertyChangedCallback() 和 removeOnPropertyChangedCallback() 方法去订阅或取消订阅通知。也可以在 notifyPropertyChanged() 中提供属性变化时的运行逻辑。  

```java
/**
 * A ViewModel that is also an Observable,
 * to be used with the Data Binding Library.
 */
class ObservableViewModel extends ViewModel implements Observable {
    private PropertyChangeRegistry callbacks = new PropertyChangeRegistry();

    @Override
    protected void addOnPropertyChangedCallback(
            Observable.OnPropertyChangedCallback callback) {
        callbacks.add(callback);
    }

    @Override
    protected void removeOnPropertyChangedCallback(
            Observable.OnPropertyChangedCallback callback) {
        callbacks.remove(callback);
    }

    /**
     * Notifies observers that all properties of this instance have changed.
     */
    void notifyChange() {
        callbacks.notifyCallbacks(this, 0, null);
    }

    /**
     * Notifies observers that a specific property has changed. The getter for the
     * property that changes should be marked with the @Bindable annotation to
     * generate a field in the BR class to be used as the fieldId parameter.
     *
     * @param fieldId The generated BR id for the Bindable field.
     */
    void notifyPropertyChanged(int fieldId) {
        callbacks.notifyCallbacks(this, fieldId, null);
    }
}
```














