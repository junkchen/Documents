# Android 架构组件

## 添加组件到项目中

架构组件在 Google 的 Maven 仓库中。

### 添加 Google Maven 仓库

在 project 的 *build.gradle* 文件中添加 *google()*，如下所示：

```gradle
allprojects {
    repositories {
        jcenter()
        google()
    }
}
```

### 声明依赖

在 module 的 build.gradle 文件中添加 AndroidX 依赖。  

```gradle
# AndroidX
implementation "androidx.lifecycle:lifecycle-viewmodel:$lifecycle_version" // use -ktx for Kotlin

# kotlin
implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:$lifecycle_version"
```

### Lifecycle

对于 Livecycle 依赖，包含 LiveData 和 ViewModel 。  

```gradle
dependencies {
    def lifecycle_version = "1.1.1"

    // ViewModel and LiveData
    implementation "android.arch.lifecycle:extensions:$lifecycle_version"
    // alternatively - just ViewModel
    implementation "android.arch.lifecycle:viewmodel:$lifecycle_version" // use -ktx for Kotlin
    // alternatively - just LiveData
    implementation "android.arch.lifecycle:livedata:$lifecycle_version"
    // alternatively - Lifecycles only (no ViewModel or LiveData).
    //     Support library depends on this lightweight import
    implementation "android.arch.lifecycle:runtime:$lifecycle_version"

    annotationProcessor "android.arch.lifecycle:compiler:$lifecycle_version"
    // alternately - if using Java8, use the following instead of compiler
    implementation "android.arch.lifecycle:common-java8:$lifecycle_version"

    // optional - ReactiveStreams support for LiveData
    implementation "android.arch.lifecycle:reactivestreams:$lifecycle_version"

    // optional - Test helpers for LiveData
    testImplementation "android.arch.core:core-testing:$lifecycle_version"
}
```

AndroidX  

```gradle
dependencies {
    def lifecycle_version = "2.0.0-alpha1"

    // ViewModel and LiveData
    implementation "androidx.lifecycle:lifecycle-extensions:$lifecycle_version"
    // alternatively - just ViewModel
    implementation "androidx.lifecycle:lifecycle-viewmodel:$lifecycle_version" // use -ktx for Kotlin
    // alternatively - just LiveData
    implementation "androidx.lifecycle:lifecycle-livedata:$lifecycle_version"
    // alternatively - Lifecycles only (no ViewModel or LiveData). Some UI
    //     AndroidX libraries use this lightweight import for Lifecycle
    implementation "androidx.lifecycle:lifecycle-runtime:$lifecycle_version"

    annotationProcessor "androidx.lifecycle:lifecycle-compiler:$lifecycle_version"
    // alternately - if using Java8, use the following instead of lifecycle-compiler
    implementation "androidx.lifecycle:lifecycle-common-java8:$lifecycle_version"

    // optional - ReactiveStreams support for LiveData
    implementation "androidx.lifecycle:lifecycle-reactivestreams:$lifecycle_version" // use -ktx for Kotlin

    // optional - Test helpers for LiveData
    testImplementation "androidx.arch.core:core-testing:$lifecycle_version"
}
```

### Room

```gradle
dependencies {
    def room_version = "1.1.1"

    implementation "android.arch.persistence.room:runtime:$room_version"
    annotationProcessor "android.arch.persistence.room:compiler:$room_version"

    // optional - RxJava support for Room
    implementation "android.arch.persistence.room:rxjava2:$room_version"

    // optional - Guava support for Room, including Optional and ListenableFuture
    implementation "android.arch.persistence.room:guava:$room_version"

    // Test helpers
    testImplementation "android.arch.persistence.room:testing:$room_version"
}
```

AndroidX  

```gradle
dependencies {
    def room_version = "2.0.0-alpha1"

    implementation "androidx.room:room-runtime:$room_version"
    annotationProcessor "androidx.room:room-compiler:$room_version"

    // optional - RxJava support for Room
    implementation "androidx.room:room-rxjava2:$room_version"

    // optional - Guava support for Room, including Optional and ListenableFuture
    implementation "androidx.room:room-guava:$room_version"

    // Test helpers
    testImplementation "androidx.room:room-testing:$room_version"
}
```

### Paging

```gradle
dependencies {
    def paging_version = "1.0.0"

    implementation "android.arch.paging:runtime:$paging_version"

    // alternatively - without Android dependencies for testing
    testImplementation "android.arch.paging:common:$paging_version"

    // optional - RxJava support, currently in release candidate
    implementation "android.arch.paging:rxjava2:1.0.0-rc1"
}
```

AndroidX  

```gradle
dependencies {
    def paging_version = "2.0.0-alpha1"

    implementation "androidx.paging:paging-runtime:$paging_version"

    // alternatively - without Android dependencies for testing
    testImplementation "androidx.paging:paging-common:$paging_version"

    // optional - RxJava support
    implementation "androidx.paging:paging-rxjava2:$paging_version"
}
```

### Navigation

```gradle
dependencies {
    def nav_version = "1.0.0-alpha02"

    implementation "android.arch.navigation:navigation-fragment:$nav_version" // use -ktx for Kotlin
    implementation "android.arch.navigation:navigation-ui:$nav_version" // use -ktx for Kotlin

    // optional - Test helpers
    androidTestImplementation "android.arch.navigation:navigation-testing:$nav_version" // use -ktx for Kotlin
}
```

### Safe args

在 Project 的 build.gradle 文件中：  

```gradle
buildscript {
    repositories {
        google()
    }
    dependencies {
        classpath "android.arch.navigation:navigation-safe-args-gradle-plugin:1.0.0-alpha02"
    }
}
```

在 module 的 build.gradle 文件中添加：  

```gradle
apply plugin: "androidx.navigation.safeargs"
```

### WorkManager

```gradle
dependencies {
    def work_version = "1.0.0-alpha04"

    implementation "android.arch.work:work-runtime:$work_version" // use -ktx for Kotlin

    // optional - Firebase JobDispatcher support
    implementation "android.arch.work:work-firebase:$work_version"

    // optional - Test helpers
    androidTestImplementation "android.arch.work:work-testing:$work_version"
}
```

## 使用 Lifecycle 组件管理生命周期

Lifecycle 组件可以执行一个操作去响应另一个组件（如Activity 或者 Fragment）的生命周期状态的变化。  

### Lifecycle

Lifecycle 持有一个组件（如 Activity 或者 Fragment）的生命周期的状态的类，它允许其他对象观察这个状态。  

Lifecycle 使用两个枚举对象来追踪生命周期的状态。  

- **Event**
  生命周期的事件是由系统和 Lifecycle 类分发的。这些事件映射到 Activity 或者 Fragment 的回调接口中。  
  
- **State**
  Lifecycle 对象跟踪的组件的当前状态。  

一个类可以通过在它的方法上添加注解来监听组件的生命周期的状态，然后你可以通过调用 Lifecycle 类的 addObserver() 方法并且传入一个观察者的实例去添加一个观察者。如下所示：  

```java
public class MyObserver implements LifecycleObserver {
    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
    public void connectListener() {
        ...
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
    public void disconnectListener() {
        ...
    }
}

myLifecycleOwner.getLifecycle().addObserver(new MyObserver());
```

### LifecycleOwner

LifecycleOwner 是一个只包含一个方法的接口。它只有一个 getLifecycle() 方法，必须由类来实现。  

#### 实现一个自定义的 LifecycleOwner

在 Support Library 26.1.0 或者更新版本中的 Activity 和 Fragment 已经实现了 LifecycleOwner 接口。  

如果你有一个自定义的类要实现 LifecycleOwner ，你可以使用 LifecycleRegistry ，而且需要需要向前把事件标记给那个类。像下面这样：  

```java
public class MyActivity extends Activity implements LifecycleOwner {
    private LifecycleRegistry mLifecycleRegistry;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mLifecycleRegistry = new LifecycleRegistry(this);
        mLifecycleRegistry.markState(Lifecycle.State.CREATED);
    }

    @Override
    public void onStart() {
        super.onStart();
        mLifecycleRegistry.markState(Lifecycle.State.STARTED);
    }

    @NonNull
    @Override
    public Lifecycle getLifecycle() {
        return mLifecycleRegistry;
    }
}
```

### Lifecyc 组件最佳实践

- 保持你的 UI 控制器（Activity 和 Fragment）尽可能简洁。
- 尝试去写数据驱动型的UI。
- 把数据逻辑放在 ViewModel 中。
- 使用 Data Binding 来保持 view 和 UI 控制器之间的接口的干净。
- 如果你的 UI 很复杂，可以考虑创建一个 presenter 来处理 UI 的修改。
- 避免在你的 ViewModel 中引用 View 和 Activity 的 context 。（ViewModel 中的 Activity 活的过长会导致内存泄漏）

