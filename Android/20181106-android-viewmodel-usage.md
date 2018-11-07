# Android 架构组件 ViewModel 的使用

**ViewModel 概述**  

ViewModel 类是一个自动贮存和管理UI相关数据的类。它允许数据在配置改变（如屏幕旋转）的时候依然存活。  


## 实现一个 ViewModel

Android 架构组件为 UI 控制器提供了一个帮助类 ViewModel，用于为 UI 准备数据。 ViewModel 对象在配置改变时会自动保留，因此他们持有的数据在下一个 Activity 或者 Fragment 实例里可以立即使用。例如，如果你需要在 app 中展示用户列表，要确保获取和保存的用户列表已经分配到 ViewModel ，而不是一个 activity 或者 fragment。示例如下：  

```kt
class MyViewModel : ViewModel() {
    private lateinit var users: MutableLiveData<List<User>>

    fun getUsers(): LiveData<List<User>> {
        if (!::users.isInitialized) {
            users = MutableLiveData()
            loadUsers()
        }
        return users
    }

    private fun loadUsers() {
        // Do an asynchronous operation to fetch users.
    }
}
```

然后在 activity 中一个像下面这样访问用户列表：  

```kt
class MyActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        // Create a ViewModel the first time the system calls an activity's onCreate() method.
        // Re-created activities receive the same MyViewModel instance created by the first activity.

        val model = ViewModelProviders.of(this).get(MyViewModel::class.java)
        model.getUsers().observe(this, Observer<List<User>>{ users ->
            // update UI
        })
    }
}
```

如果 activity 重新创建，它会接收到该 activity 第一次昂创建时相同的 MyViewModel 实例。当 activity 销毁时，底层会调用 ViewModel 对象的 onCleared() 方法去清除资源。  

ViewModel 对象设计为比视图实例的生命周期要长。ViewModel 可以包含一个 LifeCycleObservers，如 LiveData。但 ViewModel 永远不需要关心观察者（如LiveData） 的变化。如果 ViewModel 需要 Application ，如获取系统服务，他可以基础和那个 AndroidViewModel 类，该类的构造器里会接收 Application 参数，而 Application 是继承自 Context 的。  

## ViewModel 的生命周期

ViewModel 会在提供该 ViewModel 的宿主的整个生命周期中保留在内存中，直到一个 activity finish，或者一个 fragment detach 。  

<img src="./img/viewmodel-lifecycle.png" />

你通常在系统第一次调用 Activity 的 onCreate() 方法时创建 ViewModel 。在 activity 的整个生命周期中可能多调用 onCreate() 方法，如屏幕旋转。 ViewModel 从第一次创建后就一直存在，直到 activity 被结束（finished）和销毁（destroyed）。  

## 在 fragment 之间共享数据

在一个 activity 中有两个或更多个 fragment 需要相互通信是很常见的。如，用户在一个列表 fragment 中选中一个 item ，然后在另外一个详情 fragment 中需要去展示选中的 item 的详细内容。这时通常会在所有的 fragment 中定义一些接口描述，然后必须要在 activity 中绑定它们，除此之外，所有的 fragment 必须处理其他 fragment 在未创建或者可见时的情况。  

这个痛点可以通过使用 ViewModel 处理，这些 fragment 可以共享它们的宿主 activity 的 ViewModel 来处理通信。如下示例代码所示：  

```kt
class SharedViewModel : ViewModel() {
    val selected = MutableLiveData<Item>()

    fun select(item: Item) {
        selected.value = item
    }
}

class MasterFragment : Fragment() {

    private lateinit var itemSelector: Selector

    private lateinit var model: SharedViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        model = activity?.run {
            ViewModelProviders.of(this).get(SharedViewModel::class.java)
        } ?: throw Exception("Invalid Activity")
        itemSelector.setOnClickListener { item ->
            // Update the UI
        }
    }
}

class DetailFragment : Fragment() {

    private lateinit var model: SharedViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        model = activity?.run {
            ViewModelProviders.of(this).get(SharedViewModel::class.java)
        } ?: throw Exception("Invalid Activity")
        model.selected.observe(this, Observer<Item> { item ->
            // Update the UI
        })
    }
}
```

注意所有的 fragment 获取 ViewModelProvider 时都是使用 **getActivity()** 。这样可以让所有的 fragment 都接收到同一个 ShareViewModel 实例。  

这种方式的好处如下：  

- 对于这个通信 activity 不需要去做任何事也不需要直到任何事
- 所有的 fragment 之间除了 ShareViewModel 的契约外不需要相互了解。如果一个 fragment 没有显示，其他的依然可以正常工作。
- 每个 fragment 都有自己的生命周期，并且也不会被其他的 fragment 的生命周期影响。如果一个 fragment 取代了另一个 fragment ，UI 也可以继续工作而不会出现任何问题。


## 使用 ViewModel 取代 Loader

Loader 类（如 CursorLoader）经常用于保存 UI 的数据与数据库同步。你可以使用 ViewModel 和一些其他的类去取代 Loader。使用一个 ViewModel 将数据加载从 UI 控制器分离出来，这样可以减少类之间的强引用。  

使用 Loader 的常用方法是，app 可能使用 CursorLoader 去观察数据的内容。当数据库的值改变时，loader 会自动触发数据重新加载，然后更新 UI：  

<img src="./img/viewmodel-loader.png" />

ViewModel 使用 Room 和 LiveData 去取代 Loader。 ViewModel 确保设备配置改变是数据依然保留。Room 在数据库改变时通知你的 LiveData，再然后使用改动后的数据更新 UI。  

<img src="./img/viewmodel-replace-loader.png" />

source： [https://developer.android.google.cn/topic/libraries/architecture/viewmodel](https://developer.android.google.cn/topic/libraries/architecture/viewmodel)
