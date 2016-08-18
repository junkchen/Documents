# **Android USB 主机（Host）和配件（Accessory）** #

原文地址： [https://developer.android.com/guide/topics/connectivity/usb/host.html](https://developer.android.com/guide/topics/connectivity/usb/host.html)

**《英语水平有限，翻译必定有误，方便自己阅读，读者小心观看》**

Android 支持多种 USB 外设和 Android USB 配件（硬件实现 Android 配件协议），通过两种模式： USB 配件和 USB 主机。在 USB 配件模式中，外部 USB 硬件担当 USB 主机。在 USB 主机模式中，Android 设备担当主机。这样的设备包括数码相机、键盘、鼠标和游戏控制器等。  

图片位置

USB 配件和主机模式在 Android 3.1（API 等级12） 或者更高的版本中支持。  

> **注意：** 对于支持 USB 主机和配件模式最终依赖于设备的硬件配置，不管平台的API等级是多少。你可以设置 <uses-feature> 元素过滤设备是否支持 USB 主机和配件的使用。  


# **USB 主机** #

当你的 Android 设备在主机模式中，它担当 USB 主机，提供电源，罗列连接的 USB 设备。 USB 主机模式在 Android 3.1 或更高的版本中支持。  

## **API 概述** ##

在开发之前，去理解开发中将要用到的类是很重要的。下表描述了在 android.hardware.usb 包中的 USB 主机的 API。  

类|描述|
:--|:--|
UsbManager | 允许你罗列已连接的 USB 设备并与其通信 |
UsbDevice  | 代表一个已连接的 USB 设备，且包含访问它的识别信息、 interface 和 endpoint 的方法 |
UsbInterface  | 代表一个 USB 设备的 interface，设备功能的一个集合。一个连接的设备有一个或多个 interface |
UsbEndpoint  | 代表一个 interface 的 endpoint ，它是这个 interface 的通信的信道。一个 interface 可以有一个或多个 endpoint 。通常在双向通信设备中有输入和输出的 endpoint |
UsbDeviceConnection | 代表设备的一个连接，在 endpoint 上传输数据。这个类允许你发送和接收同步或异步返回的数据 |
UsbRequest  | 代表通过 UsbDeviceConnection 和一个设备通信的异步请求 |
UsbConstants  | 定义的 USB 常量和 Linux 内核中 linux/usb/ch9.h 文件中定义的一致 |

在大多数情况下，和一个 USB 通信时你需要使用以上这些类。通常，你得到一个 UsbManager 去获取一个期望的 UsbDevice 。你需要去寻找合适的 UsbInterface 以及通信接口的 UsbEndpoint 。一旦你获得正确的endpoint，就可以打开一个 UsbDeviceConnection 去和 USB 设备通信。  

## **Android Manifest 约束** ##

下面的描述是你在使用 USB 主机 API 开发时需要在你的应用的 Manifest 文件中添加的内容：  

- 因为不是所有的 Android 设备都保证支持 USB 主机 API ，在 <uses-feature> 元素中声明你的应用使用 android.hardware.usb.host 特性。

- 设置应用的最小 SDK 的 API 等级为 12 或者更高。 USB 主机 API 在之前的版本中没有提出。  

- 如果你想要你的应用在连接上 USB 设备时进行提示，在你的主 activity 中的 <intent-filter> 和 <meta-data> 元素对中添加 android.hardware.usb.action.USB_DEVICE_ATTACHED 意图。 <meta-data> 元素指向一个外部 XML 资源文件，它声明了你想要监测的设备的识别信息。

  在 XML 资源文件中，在 <usb-device> 元素中声明你想要过滤的 USB 设备。下面列表描述了 <usb-device> 的属性。通常，如果你想要过滤一个指定的设备可以使用供应商和产品编号，如果你想要过滤一类 USB 设备，如大量的存储设备或者数码相机，可以使用类、子类和协议。你可以不指定或者指定所有的属性。没有指定这些属性会匹配每一个 USB 设备，如果你的应用有需要只要指定这些：
  
  - vendor-id
  - product-id
  - class
  - subclass
  - protocol(device or interface)

  在 res/xml 目录中保存这个资源文件。资源文件的名字必须和你在 <meta-data> 元素中只指定的一样。

## **Manifest 和资源文件示例** ##

下面示例展示了一个 manifest 范例和它相应的资源文件：  

```xml
<manifest ...>
    <uses-feature android:name="android.hardware.usb.host" />
    <uses-sdk android:minSdkVersion="12" />
    ...
    <application>
        <activity ...>
            ...
            <intent-filter>
                <action android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED" />
            </intent-filter>

            <meta-data android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED"
                android:resource="@xml/device_filter" />
        </activity>
    </application>
</manifest>
```

在这种情况下，下面的资源文件应该保存为 <font color="green">res/xml/device-filter.xml</font> ，指定要过滤设备的属性：  

```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
    <usb-device vendor-id="1234" product-id="5678" class="255" subclass="66" protocol="1" />
</resources>
```

## **设备工作** ##

当用户的 USB 设备连接到 Android 设备时，Android 系统可以判定你的应用是否对连接的设备感兴趣。如果是这样，你可以和期望的设备建立通信。要这样做，你的应用必须：  

1. 去发现已连接的 USB 设备，可以使用一个 intent 过滤器在用户连接到一个 USB设备时进行通知，也可以通过枚举已连接的 USB 设备。

2. 对已连接的 USB 设备请求用户权限，是否已获得。

3. 与 USB 设备通信，在合适的 interface 上的 endpoint 的进行数据读写。

### **发现设备** ###

你的应用可以使用 intent 过滤器在用户连接上一个设备时进行通知或者枚举已连接的 USB 设备去发现 USB设备。如果你想要你的应用自动去检测期望的设备，那么使用 intent 过滤器是非常有用的。如果你想要获得已连接的设备列表或者如果你的应用没有 intent 过滤器，则枚举已连接的 USB 设备是游泳的。  

#### **使用 intent 过滤器** ####

想要你的应用发现一个指定的 USB 设备，你可以指定一个 intent 过滤器去进行过滤，添加 android.hardware.usb.action.USB_DEVICE_ATTACHED 。除了这个 intent 过滤器，你需要指定一个资源文件，这个文件指定 USB 设备设备的属性，如产品和供应商的ID。当用户连接一个设备匹配你的设备过滤器时，系统会出现一个提示框询问是否启动你的应用。如果用户接受，你的应用自动拥有访问设备的权限直到设备断开连接。  

下面示例展示如何声明 intent 过滤器：  

```xml
<activity ...>
...
    <intent-filter>
        <action android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED" />
    </intent-filter>

    <meta-data android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED"
        android:resource="@xml/device_filter" />
</activity>
```

下面的示例展示了如何声明相应的资源文件，指定你感兴趣的 USB 设备：  

```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
    <usb-device vendor-id="1234" product-id="5678" />
</resources>
```

在你的Activity中，你可以获得 UsbDevice ，它代表从 intent 过滤器连接的设备，像这样：  

```java
UsbDevice device = (UsbDevice) intent.getParcelableExtra(UsbManager.EXTRA_DEVICE);
```

#### **枚举设备** ####

在你的应用运行期间，如果你的应用对当前所有连接的 USB 设备感兴趣，它可以枚举设备。使用 getDeviceList() 方法获得一个所有已连接的 USB 设备的散列集合。这个集合的键是 USB 设备的名字，如果你想要从集合中获取一个设备。  

```java
UsbManager manager = (UsbManager) getSystemService(Context.USB_SERVICE);
...
HashMap<String, UsbDevice> deviceList = manager.getDeviceList();
UsbDevice device = deviceList.get("deviceName");
```

如果有需要，你也可以从哈希集合中获得一个迭代器然后一个一个列举设备：  

```java
UsbManager manager = (UsbManager) getSystemService(Context.USB_SERVICE);
...
HashMap<String, UsbDevice> deviceList = manager.getDeviceList();
Iterator<UsbDevice> deviceIterator = deviceList.values().iterator();
while(deviceIterator.hasNext()){
    UsbDevice device = deviceIterator.next();
    //your code
}
```

### **获得与设备通信的权限** ###

在与 USB 设备进行通信之前，你的应用必须要有来自用户的同意。  

> **注意：**如果你的应用使用 intent 过滤器发现设备作为他们的连接，如果用户允许你的应用去处理 intent 他会自动接受许可。如果没有，在连接设备前你必须在你的应用里明确地请求许可。  

明确请求许可在某些情况里是必需的，如当你的应用枚举已经连接的 USB 设备，然后想要与其中一个进行通信时。你必须在尝试与其通信前检查访问设备设备的许可。如果没有得到许可，如果用户拒绝访问设备的许可你将会接收到一个运行时错误。  

为了明确地获取许可，首先创建一个广播接收器。当你调用 requestPermission() 方式时这个接收器监听获得的广播的意图。调用 requestPermission() 方法显示一个对话框去询问用户是否同意连接设备。下面的示例代码展示了如何创建广播接收器：  

```java
private static final String ACTION_USB_PERMISSION =
    "com.android.example.USB_PERMISSION";
private final BroadcastReceiver mUsbReceiver = new BroadcastReceiver() {

    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if (ACTION_USB_PERMISSION.equals(action)) {
            synchronized (this) {
                UsbDevice device = (UsbDevice)intent.getParcelableExtra(UsbManager.EXTRA_DEVICE);

                if (intent.getBooleanExtra(UsbManager.EXTRA_PERMISSION_GRANTED, false)) {
                    if(device != null){
                      //call method to set up device communication
                   }
                }
                else {
                    Log.d(TAG, "permission denied for device " + device);
                }
            }
        }
    }
};
```

注册广播接收器，添加如下代码到你的 activity 的 onCreate() 方法中：  

```java
UsbManager mUsbManager = (UsbManager) getSystemService(Context.USB_SERVICE);
private static final String ACTION_USB_PERMISSION =
    "com.android.example.USB_PERMISSION";
...
mPermissionIntent = PendingIntent.getBroadcast(this, 0, new Intent(ACTION_USB_PERMISSION), 0);
IntentFilter filter = new IntentFilter(ACTION_USB_PERMISSION);
registerReceiver(mUsbReceiver, filter);
```

显示对话框询问用户是否同意连接到设备，调用 requestPermisson() 方法：  

```java
UsbDevice device;
...
mUsbManager.requestPermission(device, mPermissionIntent);
```

当用户对窗口做出响应后，你的广播接收器会接收到 intent 且包含  EXTRA_PERMISSION_GRANTED 的附加值，一个表示回复的布尔型的值。连接设备前检查这个值是否为 true 。  

### **和一个设备通信** ###

和一个 USB 设备通信可以是同步的也可以是异步的。无论那种情况，你都应该创建一个新的线程去进行所有的数据传输，这样就不会阻塞 UI 线程。要正确的和设备建立通信，你需要获得你想要进行通信的设备的合适的 USBInterface 和 UsbEndpoint ，然后在这个 endpoint 上用一个 UsbDeviceConnection 发送请求。通常，你的编码应该这样：  

- 检查 UsbDevice 对象的属性，如产品 ID ，供应商 ID ，或者设备类，关于是否想要进行通信的设备。

- 当你确定想要和一个设备通信时，找到合适的 UsbInterface ，并且在该 interface 中找到合适的 UsbEndpoint 。 interface 可以有一个或多个 endpoint ，通常，在双向通信中会有一个输入和出书的 endpoint 。

- 当你找到正确的 endpoint ，在这个 endpoint 上打开 一个 UsbDeviceConnection 。

- 提供你想要在 endpoint 上用 bulkTransfer() 或者 controlTransfer() 方法传输的数据。你应该在另一个线程中执行这个步骤，以避免阻塞 UI 主线程。

下面的代码片段是做一个同步数据传输的一般方式。你的代码应该有更多的逻辑去寻找适合通信的 interface 和 endpoint ，而且你做任何数据传输应该在其他的线程中执行而不是 UI 主线程：  

```java
private Byte[] bytes;
private static int TIMEOUT = 0;
private boolean forceClaim = true;

...

UsbInterface intf = device.getInterface(0);
UsbEndpoint endpoint = intf.getEndpoint(0);
UsbDeviceConnection connection = mUsbManager.openDevice(device);
connection.claimInterface(intf, forceClaim);
connection.bulkTransfer(endpoint, bytes, bytes.length, TIMEOUT); //do in another thread
```

发送异步数据使用 UsbRequest 类去初始化和排队等候一个异步请求，然后在 requestWait() 中等待结果。  

### **结束与设备的通信** ###

当你完成和设备的通信时或者设备已经断开，应该调用 releaseInterface() 和 close() 方法去关闭 USsbInterface 和 UsbDeviceConnection 。要监听分离事件，应该创建如下的广播接收器：  

```java
BroadcastReceiver mUsbReceiver = new BroadcastReceiver() {
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();

      if (UsbManager.ACTION_USB_DEVICE_DETACHED.equals(action)) {
            UsbDevice device = (UsbDevice)intent.getParcelableExtra(UsbManager.EXTRA_DEVICE);
            if (device != null) {
                // call your method that cleans up and closes communication with the device
            }
        }
    }
};
```

在应用里创建广播接收器，而不是在 manifest 中，仅仅允许你的应用在运行的时候去处理分离事件。这种方式，分离事件仅在应用运行的时候发送而不是对所有的应用广播。  

**欢迎加QQ群交流： 365532949**  
**Homepage: [http://junkchen.com](http://junkchen.com)** 