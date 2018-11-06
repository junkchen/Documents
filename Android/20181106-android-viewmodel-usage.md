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

