#Android 低功耗蓝牙开发

----------
原文： [https://developer.android.com/guide/topics/connectivity/bluetooth-le.html](https://developer.android.com/guide/topics/connectivity/bluetooth-le.html)  
官方示例Demo：[http://download.csdn.net/detail/kjunchen/9363233](http://download.csdn.net/detail/kjunchen/9363233)    


在Android4.3（API等级18）平台上开始支持低功耗蓝牙中央设备角色，而且提供可供应用去发现服务、查询服务和读写特性的相关API接口。与传统蓝牙相比，低功耗蓝牙的设计对电量消耗更低，这允许Android应用与其他的低功耗设备通信时对电量的需求更低，如距离传感器、心率监视器和医疗健康设备等等。  

##一、关键术语和概念

----------
这是一些关于低功耗蓝牙的关键术语及概念的概述：  
  
- **通用属性配置 (GATT)**：GATT协议是关于发送和接收短数据块的通用规格，在低功耗链路中被称为属性。当前所有的低功耗蓝牙应用协议都是以GATT为基础的。

	- 蓝牙联盟为低功耗设备定义了很多[配置](https://www.bluetooth.org/en-us/specification/adopted-specifications)。在一个指定的应用中一种配置说明一个设备如何工作。一个设备可以实现多种配置。如一个设备可以包含一个心率监视器和一个电池电量检测器。    
	
	 
- **属性协议（ATT）**： GATT构建于ATT上面的。每一个属性都是由通用唯一识别码（UUID）唯一确定。这是一个标准的128位的格式化字符串ID用来唯一标识信息。属性通过ATT传输格式化为特征和服务。  

- **特征（Characteristic）**：一个特征包含一个单一的值和0-n个描述符，描述符用于描述特征的值。一个特征可以被认为是一种类型，类似于一个类。 
  
- **描述符（Descriptor）**：描述符被定义成属性用于描述特征的值。例如,一个描述符可以指定一个人类可读的类型，一个特征值可接受的范围，或者特征值的确定的测量单位。  

- **服务（Service）**： 服务是特征的集合。例如，你可以有一个服务叫“心率监视器”，包含的特征叫“心率测量”。你可以在[bluetooth.org](https://www.bluetooth.org)中找到已存在的GATT基础配置和服务列表。  

##二、角色和职责  

----------
这是Android设备与低功耗蓝牙设备交互时应用的角色与职责：  

- 中央与周边： 作为中央角色的设备可进行扫描、搜索广播。作为周边角色的设备可发送广播。  

- GATT服务器与GATT客户端： 这个决定了两个设备一旦建立连接后如何通信。  

为了理解他们的区别，假设你有一部Android手机和一个活动跟踪器的低功耗蓝牙设备。手机支持中央角色，活动跟踪器支持周边角色（为了建立稳定的低功耗蓝牙连接，若两个设备仅支持周边或中央角色是不能进行通信的）。  

一旦手机和活动跟踪器建立了连接，他们开始相互传输GATT元数据。根据他们传输的数据，其中一个设备可能作为服务器。例如，如果活动跟踪器想要发送传感器数据到手机，他可能把活动跟踪器作为服务器，如果活动跟踪器想要从手机接收更新数据，他可能认为手机作为服务器。  

在本文档的例子中，Android app作为GATT客户端。app从GATT服务器中获取数据，心率监视器支持[心率配置](https://developer.bluetooth.org/TechnologyOverview/Pages/HRP.aspx)。但是你也可以将Android app设计成担任GATT服务器的角色。更多信息查看[BluetoothGattServer](https://developer.android.com/reference/android/bluetooth/BluetoothGattServer.html)类。  

##三、声明BLE权限 

----------
为了在你的应用中使用蓝牙功能，你必须声明蓝牙权限“android.permission.BLUETOOTH”。你需要使用这个权限如执行所有的蓝牙通信，如请求连接，接受连接和传输数据。  

如果想要你的应用去初始化设备发现或者操纵蓝牙设置，你还必须声明“android.permission.BLUETOOTH_ADMIN”权限。  

在应用的AndroidManifest.xml文件中声明蓝牙权限。如：

    <uses-permission android:name="android.permission.BLUETOOTH"/>
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>

如果想要声明你的应用仅对低功耗蓝牙是有效的，在app的manifest中还应包含下面这句：  

	<uses-feature android:name="android.hardware.bluetooth_le"
        android:required="true" />  

当然，如果想要使你的应用对可获得设备不支持低功耗蓝牙，你仍然应该包含这个元素，但要设置required="false"。然后你可以在运行的时候通过使用PackageManager.hasSystemFeature()检查决定是否BLE有效。  

	// Use this check to determine whether BLE is supported on the device. Then
	// you can selectively disable BLE-related features.
	if (!getPackageManager().hasSystemFeature(PackageManager.FEATURE_BLUETOOTH_LE)) {
	    Toast.makeText(this, R.string.ble_not_supported, Toast.LENGTH_SHORT).show();
	    finish();
	}  

##四、设置BLE

----------
应用在可以通过BLE通信之前，你需要验证在设备上BLE是否支持，如果支持，确保他已经打开。  

如果不支持BLE，你应该关闭所有的BLE功能。如果支持BLE，但没有使能，你可以不关闭应用并请求用户使能蓝牙。这个设置有两步,使用BluetoothAdapter。  

1、 获得[BluetoothAdapter](https://developer.android.com/reference/android/bluetooth/BluetoothAdapter.html)
   
   BluetoothAdapter对所有的蓝牙活动是必须的。BluetoothAdapter代表设备自身蓝牙适配器（蓝牙无线设备）。整个系统只有一个蓝牙适配器，你的应用可以使用这个对象进行交互。下面的片段展示了如何获得适配器。注意这个方法使用getSystemService()去返回一个[BluetoothManager](https://developer.android.com/reference/android/bluetooth/BluetoothManager.html)实例，然后使用他去获得BluetoothAdapter。Android 4.3（API等级18）才支持BluetoothManager。   
 

	// Initializes Bluetooth adapter.
	final BluetoothManager bluetoothManager =
	        (BluetoothManager) getSystemService(Context.BLUETOOTH_SERVICE);
	mBluetoothAdapter = bluetoothManager.getAdapter();  

2、使能蓝牙 
 
   接下来，你需要确保蓝牙已经打开。调用isEnabled()去检查蓝牙当前是否使能。如果该方法返回false，说明蓝牙未使能。下面这段代码检查蓝牙是否使能。如果没有使能，这段代码会显示一个错误提示用户去设置启用蓝牙：  

	private BluetoothAdapter mBluetoothAdapter;
	...
	// Ensures Bluetooth is available on the device and it is enabled. If not,
	// displays a dialog requesting user permission to enable Bluetooth.
	if (mBluetoothAdapter == null || !mBluetoothAdapter.isEnabled()) {
	    Intent enableBtIntent = new Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE);
	    startActivityForResult(enableBtIntent, REQUEST_ENABLE_BT);
	}  

##五、扫描BLE设备  

----------

为了搜索BLE设备，使用startLeScan()方法。这个方法携带一个[BluetoothAdapter.LeScanCallback](https://developer.android.com/reference/android/bluetooth/BluetoothAdapter.LeScanCallback.html)作为一个参数。你必须实现这个接口，因为这里会返回扫描的结果。因为扫描是耗电的，你应该遵守以下原则：  
  
- 只要发现期望的设备，立即停止扫描。
- 永远不要循环扫描，而是设置扫描时间限制。以前可用的设备可能已经移出范围，继续扫描只会耗尽电量。  

下面代码展示如何开始和停止扫描： 

	/**
	 * Activity for scanning and displaying available BLE devices.
	 */
	public class DeviceScanActivity extends ListActivity {
	
	    private BluetoothAdapter mBluetoothAdapter;
	    private boolean mScanning;
	    private Handler mHandler;
	
	    // Stops scanning after 10 seconds.
	    private static final long SCAN_PERIOD = 10000;
	    ...
	    private void scanLeDevice(final boolean enable) {
	        if (enable) {
	            // Stops scanning after a pre-defined scan period.
	            mHandler.postDelayed(new Runnable() {
	                @Override
	                public void run() {
	                    mScanning = false;
	                    mBluetoothAdapter.stopLeScan(mLeScanCallback);
	                }
	            }, SCAN_PERIOD);
	
	            mScanning = true;
	            mBluetoothAdapter.startLeScan(mLeScanCallback);
	        } else {
	            mScanning = false;
	            mBluetoothAdapter.stopLeScan(mLeScanCallback);
	        }
	        ...
	    }
	...
	}

如果你想要扫描指定类型的周边设备，你可以调用startLeScan(UUID[], BluetoothAdapter.LeScanCallback)，提供一个UUID对象数组，指定你的应用支持的GATT服务。  

这里实现BluetoothAdapter.LeScanCallback，这个接口用于发送BLE的扫描结果：  

	private LeDeviceListAdapter mLeDeviceListAdapter;
	...
	// Device scan callback.
	private BluetoothAdapter.LeScanCallback mLeScanCallback =
	        new BluetoothAdapter.LeScanCallback() {
	    @Override
	    public void onLeScan(final BluetoothDevice device, int rssi,
	            byte[] scanRecord) {
	        runOnUiThread(new Runnable() {
	           @Override
	           public void run() {
	               mLeDeviceListAdapter.addDevice(device);
	               mLeDeviceListAdapter.notifyDataSetChanged();
	           }
	       });
	   }
	};  
  
> **注意**：你只可以扫描低功耗蓝牙设备或者传统蓝牙设备，不能在同一时间既扫描低功耗设备有扫描传统蓝牙设备。  


##六、连接到一个GATT服务器

----------
与BLE设备交互的第一步是连接到它，更具体地说，连接到GATT服务器设备。连接到一个BLE设备GATT服务器，使用connectGatt()方法。这个方法需要三个参数：一个Context对象，autoConnect(布尔值表明当BLE设备可用时是否自动连接)，和一个[BluetoothGattCallback](https://developer.android.com/reference/android/bluetooth/BluetoothGattCallback.html)引用：  

	mBluetoothGatt = device.connectGatt(this, false, mGattCallback);  

通过BLE设备连接到GATT服务器时，会返回一个BluetoothGatt实例，你可以使用它控制GATT客户端操作。Android app是GATT客户端。BluetoothGattCallback用于发送结果到客户端，如连接状态，以及GATT客户端的进一步操作。  

在这个例子中，BLE app提供一个Activity（DeviceControlActivity）去连接，显示数据，显示设备支持的GATT服务和特征。基于用户的输入，这个Activity调用BluetoothLeService与Service通信，BluetoothLeService中包含了BLE设备与Android BLE交互的接口：  

	// A service that interacts with the BLE device via the Android BLE API.
	public class BluetoothLeService extends Service {
	    private final static String TAG = BluetoothLeService.class.getSimpleName();
	
	    private BluetoothManager mBluetoothManager;
	    private BluetoothAdapter mBluetoothAdapter;
	    private String mBluetoothDeviceAddress;
	    private BluetoothGatt mBluetoothGatt;
	    private int mConnectionState = STATE_DISCONNECTED;
	
	    private static final int STATE_DISCONNECTED = 0;
	    private static final int STATE_CONNECTING = 1;
	    private static final int STATE_CONNECTED = 2;
	
	    public final static String ACTION_GATT_CONNECTED =
	            "com.example.bluetooth.le.ACTION_GATT_CONNECTED";
	    public final static String ACTION_GATT_DISCONNECTED =
	            "com.example.bluetooth.le.ACTION_GATT_DISCONNECTED";
	    public final static String ACTION_GATT_SERVICES_DISCOVERED =
	            "com.example.bluetooth.le.ACTION_GATT_SERVICES_DISCOVERED";
	    public final static String ACTION_DATA_AVAILABLE =
	            "com.example.bluetooth.le.ACTION_DATA_AVAILABLE";
	    public final static String EXTRA_DATA =
	            "com.example.bluetooth.le.EXTRA_DATA";
	
	    public final static UUID UUID_HEART_RATE_MEASUREMENT =
	            UUID.fromString(SampleGattAttributes.HEART_RATE_MEASUREMENT);
	
	    // Various callback methods defined by the BLE API.
	    private final BluetoothGattCallback mGattCallback =
	            new BluetoothGattCallback() {
	        @Override
	        public void onConnectionStateChange(BluetoothGatt gatt, int status,
	                int newState) {
	            String intentAction;
	            if (newState == BluetoothProfile.STATE_CONNECTED) {
	                intentAction = ACTION_GATT_CONNECTED;
	                mConnectionState = STATE_CONNECTED;
	                broadcastUpdate(intentAction);
	                Log.i(TAG, "Connected to GATT server.");
	                Log.i(TAG, "Attempting to start service discovery:" +
	                        mBluetoothGatt.discoverServices());
	
	            } else if (newState == BluetoothProfile.STATE_DISCONNECTED) {
	                intentAction = ACTION_GATT_DISCONNECTED;
	                mConnectionState = STATE_DISCONNECTED;
	                Log.i(TAG, "Disconnected from GATT server.");
	                broadcastUpdate(intentAction);
	            }
	        }
	
	        @Override
	        // New services discovered
	        public void onServicesDiscovered(BluetoothGatt gatt, int status) {
	            if (status == BluetoothGatt.GATT_SUCCESS) {
	                broadcastUpdate(ACTION_GATT_SERVICES_DISCOVERED);
	            } else {
	                Log.w(TAG, "onServicesDiscovered received: " + status);
	            }
	        }
	
	        @Override
	        // Result of a characteristic read operation
	        public void onCharacteristicRead(BluetoothGatt gatt,
	                BluetoothGattCharacteristic characteristic,
	                int status) {
	            if (status == BluetoothGatt.GATT_SUCCESS) {
	                broadcastUpdate(ACTION_DATA_AVAILABLE, characteristic);
	            }
	        }
	     ...
	    };
	...
	}  

当一个回调被触发，调用适当的辅助方法broadcastUpdate()，传入一个action。注意这部分的数据解析是依据[蓝牙心率测量配置规格](https://developer.bluetooth.org/gatt/characteristics/Pages/CharacteristicViewer.aspx?u=org.bluetooth.characteristic.heart_rate_measurement.xml)进行的。  

	private void broadcastUpdate(final String action) {
	    final Intent intent = new Intent(action);
	    sendBroadcast(intent);
	}
	
	private void broadcastUpdate(final String action,
	                             final BluetoothGattCharacteristic characteristic) {
	    final Intent intent = new Intent(action);
	
	    // This is special handling for the Heart Rate Measurement profile. Data
	    // parsing is carried out as per profile specifications.
	    if (UUID_HEART_RATE_MEASUREMENT.equals(characteristic.getUuid())) {
	        int flag = characteristic.getProperties();
	        int format = -1;
	        if ((flag & 0x01) != 0) {
	            format = BluetoothGattCharacteristic.FORMAT_UINT16;
	            Log.d(TAG, "Heart rate format UINT16.");
	        } else {
	            format = BluetoothGattCharacteristic.FORMAT_UINT8;
	            Log.d(TAG, "Heart rate format UINT8.");
	        }
	        final int heartRate = characteristic.getIntValue(format, 1);
	        Log.d(TAG, String.format("Received heart rate: %d", heartRate));
	        intent.putExtra(EXTRA_DATA, String.valueOf(heartRate));
	    } else {
	        // For all other profiles, writes the data formatted in HEX.
	        final byte[] data = characteristic.getValue();
	        if (data != null && data.length > 0) {
	            final StringBuilder stringBuilder = new StringBuilder(data.length);
	            for(byte byteChar : data)
	                stringBuilder.append(String.format("%02X ", byteChar));
	            intent.putExtra(EXTRA_DATA, new String(data) + "\n" +
	                    stringBuilder.toString());
	        }
	    }
	    sendBroadcast(intent);
	}  

在DeviceControlActivity返回，使用BroadcastReceiver进行处理：  

	// Handles various events fired by the Service.
	// ACTION_GATT_CONNECTED: connected to a GATT server.
	// ACTION_GATT_DISCONNECTED: disconnected from a GATT server.
	// ACTION_GATT_SERVICES_DISCOVERED: discovered GATT services.
	// ACTION_DATA_AVAILABLE: received data from the device. This can be a
	// result of read or notification operations.
	private final BroadcastReceiver mGattUpdateReceiver = new BroadcastReceiver() {
	    @Override
	    public void onReceive(Context context, Intent intent) {
	        final String action = intent.getAction();
	        if (BluetoothLeService.ACTION_GATT_CONNECTED.equals(action)) {
	            mConnected = true;
	            updateConnectionState(R.string.connected);
	            invalidateOptionsMenu();
	        } else if (BluetoothLeService.ACTION_GATT_DISCONNECTED.equals(action)) {
	            mConnected = false;
	            updateConnectionState(R.string.disconnected);
	            invalidateOptionsMenu();
	            clearUI();
	        } else if (BluetoothLeService.
	                ACTION_GATT_SERVICES_DISCOVERED.equals(action)) {
	            // Show all the supported services and characteristics on the
	            // user interface.
	            displayGattServices(mBluetoothLeService.getSupportedGattServices());
	        } else if (BluetoothLeService.ACTION_DATA_AVAILABLE.equals(action)) {
	            displayData(intent.getStringExtra(BluetoothLeService.EXTRA_DATA));
	        }
	    }
	};  

##七、读取BLE属性

----------
一旦你的Android应用连接到GATT服务器并且发现服务，就可以对支持的服务进行属性的读取和写入。例如，这段代码遍历了服务器的服务和特征并显示在UI中：  

	public class DeviceControlActivity extends Activity {
	    ...
	    // Demonstrates how to iterate through the supported GATT
	    // Services/Characteristics.
	    // In this sample, we populate the data structure that is bound to the
	    // ExpandableListView on the UI.
	    private void displayGattServices(List<BluetoothGattService> gattServices) {
	        if (gattServices == null) return;
	        String uuid = null;
	        String unknownServiceString = getResources().
	                getString(R.string.unknown_service);
	        String unknownCharaString = getResources().
	                getString(R.string.unknown_characteristic);
	        ArrayList<HashMap<String, String>> gattServiceData =
	                new ArrayList<HashMap<String, String>>();
	        ArrayList<ArrayList<HashMap<String, String>>> gattCharacteristicData
	                = new ArrayList<ArrayList<HashMap<String, String>>>();
	        mGattCharacteristics =
	                new ArrayList<ArrayList<BluetoothGattCharacteristic>>();
	
	        // Loops through available GATT Services.
	        for (BluetoothGattService gattService : gattServices) {
	            HashMap<String, String> currentServiceData =
	                    new HashMap<String, String>();
	            uuid = gattService.getUuid().toString();
	            currentServiceData.put(
	                    LIST_NAME, SampleGattAttributes.
	                            lookup(uuid, unknownServiceString));
	            currentServiceData.put(LIST_UUID, uuid);
	            gattServiceData.add(currentServiceData);
	
	            ArrayList<HashMap<String, String>> gattCharacteristicGroupData =
	                    new ArrayList<HashMap<String, String>>();
	            List<BluetoothGattCharacteristic> gattCharacteristics =
	                    gattService.getCharacteristics();
	            ArrayList<BluetoothGattCharacteristic> charas =
	                    new ArrayList<BluetoothGattCharacteristic>();
	           // Loops through available Characteristics.
	            for (BluetoothGattCharacteristic gattCharacteristic :
	                    gattCharacteristics) {
	                charas.add(gattCharacteristic);
	                HashMap<String, String> currentCharaData =
	                        new HashMap<String, String>();
	                uuid = gattCharacteristic.getUuid().toString();
	                currentCharaData.put(
	                        LIST_NAME, SampleGattAttributes.lookup(uuid,
	                                unknownCharaString));
	                currentCharaData.put(LIST_UUID, uuid);
	                gattCharacteristicGroupData.add(currentCharaData);
	            }
	            mGattCharacteristics.add(charas);
	            gattCharacteristicData.add(gattCharacteristicGroupData);
	         }
	    ...
	    }
	...
	}  

##八、接收GATT通知

----------
对于BLE应用它是通用的，当设备指定的特征改变时请求通知。下面这段代码展示了如何为一个特征设置通知，使用setCharacteristicNotification()方法。  

	private BluetoothGatt mBluetoothGatt;
	BluetoothGattCharacteristic characteristic;
	boolean enabled;
	...
	mBluetoothGatt.setCharacteristicNotification(characteristic, enabled);
	...
	BluetoothGattDescriptor descriptor = characteristic.getDescriptor(
	        UUID.fromString(SampleGattAttributes.CLIENT_CHARACTERISTIC_CONFIG));
	descriptor.setValue(BluetoothGattDescriptor.ENABLE_NOTIFICATION_VALUE);
	mBluetoothGatt.writeDescriptor(descriptor);  

当一个特征的通知被打开，如果远程设备的特征改变时onCharacteristicChanged()回调方法就会被触发。  

	@Override
	// Characteristic notification
	public void onCharacteristicChanged(BluetoothGatt gatt,
	        BluetoothGattCharacteristic characteristic) {
	    broadcastUpdate(ACTION_DATA_AVAILABLE, characteristic);
	}  

##九、关闭GATT客户端

----------
一旦你的应用结束使用BLE设备，应该调用close()方法，让系统可以适当的释放资源。  

```

	public void close() {
	    if (mBluetoothGatt == null) {
	        return;
	    }
	    mBluetoothGatt.close();
	    mBluetoothGatt = null;
	}
  
```

##小结

----------
小结纯属个人自由发挥（非官方），另外，由于个人英语水平极其有限，翻译难免有误，读者若发现问题请看后面的联系方式，然后反馈给我进行修改，不胜感谢，希望可以帮助到更多的Android低功耗蓝牙开发者。  

Android低功耗蓝牙开发基本步骤： 

**1、声明蓝牙权限  
2、设置BLE  
3、扫描BLE  
4、连接到GATT服务器（即低功耗蓝牙设备）  
5、读写BLE属性（即收发数据）  
6、接收GATT通知  
7、关闭GATT客户端**

一般Android手机作为中央角色，BLE设备作为周边角色。  

Android低功耗开发要求版本在4.3或以上（API等级18），蓝牙版本则要求4.0或更高。  

开发应当使用真机测试，Android模拟器不支持蓝牙。

当我们连接上BLE设备时，会得到一个BluetoothGatt，其可包含多个Service，然一个Service中又可包含多个Characteristic，而一个Characteristic中会包含一个值（数据），我们使用BLE进行通信时，主要就是对Characteristic进行操作，利用它接收和发送数据。 

Android BLE客户端与BLE周边设备进行通信时，必须知道Service和Characteristic的UUID，数据格式，双方要协商一致。否则无法对数据进行准确解析。  

如有问题欢迎加群讨论：**365532949**  
也可发邮件：**junkchen@vip.qq.com**  
欢迎留言评论...