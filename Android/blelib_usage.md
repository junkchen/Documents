# *使用BleLib库的轻松搞定低功耗蓝牙Ble 4.0开发详解*
 
----------
- ***Github***: [https://github.com/junkchen/BleLib](https://github.com/junkchen/BleLib)

BleLib是Android低功耗蓝牙4.0及以上开发的辅助库，一行代码解决Ble初始化、扫描、连接、特性读写、设置通知等操作。  

BleLib中的关键类：
  
- BleService是单个Ble连接操作的服务类  
- GattAttributes类中包含了蓝牙联盟规定的服务和特征的UUID值
- MultipleBleService类是可多个蓝牙设备同时连接的服务类


##*使用方法*

###*第一步：添加BleLib库依赖*

因此，在你项目Module中的build.gradle文件中添加库依赖即可，如下：  

	dependencies {
	    compile 'com.junkchen.blelib:blelib:1.0.4'
	}

只此一句即可使用BleLib库，方便吧，要的就是这效果。  
使用Android Studio时按照如下方式添加依赖比较好,获取的是最新的版本,结果和上面是一样的，进入模块的库依赖设置，搜索blelib即可获取：  
![](https://github.com/junkchen/Documents/raw/master/Android/img/as_lib_dependency.png)

###*第二步：绑定BleLib服务*

BleLib库中的Ble服务类继承了Service，因此建议绑定服务进行使用。  
  

	private BleService mBleService;
    private boolean mIsBind;
	private ServiceConnection serviceConnection = new ServiceConnection() {
	        @Override
	        public void onServiceConnected(ComponentName name, IBinder service) {
	            mBleService = ((BleService.LocalBinder) service).getService();
	            if (mBleService.initialize()) {
	                if (mBleService.enableBluetooth(true)) {
	                    mBleService.scanLeDevice(true);
	                    Toast.makeText(BleScanActivity.this, "Bluetooth was opened", Toast.LENGTH_SHORT).show();
	                }
	            } else {
	                Toast.makeText(BleScanActivity.this, "not support Bluetooth", Toast.LENGTH_SHORT).show();
	            }
	        }
	
	        @Override
	        public void onServiceDisconnected(ComponentName name) {
	            mBleService = null;
	            mIsBind = false;
	        }
	    };

	private void doBindService() {
        Intent serviceIntent = new Intent(this, BleService.class);
        bindService(serviceIntent, serviceConnection, Context.BIND_AUTO_CREATE);
    }

    private void doUnBindService() {
        if (mIsBind) {
            unbindService(serviceConnection);
            mBleService = null;
            mIsBind = false;
        }
    }


###*第三步：初始化操作*

当服务绑定后可进行初始化操作，判断该机是否支持蓝牙，调用如下方法：  

	mBleService.initialize();//Ble初始化操作  

该方法会返回一个boolean值，返回true表示初始化成功，支持蓝牙；返回false表示初始化操作失败，则后续的所有操作都不能进行。  


###*第四步：打开蓝牙*  

当初始化操作成功后就可以打开蓝牙了，调用如下方法：

    mBleService.enableBluetooth(boolean enable);//打开或关闭蓝牙 

该方法需要传入一个boolean类型的参数，true表示打开蓝牙，false表示关闭蓝牙；并返回boolean参数，返回true表示蓝牙打开，否则关闭。  


###*第五步：扫描Ble设备*

当蓝牙打开后可以进行Ble设备的扫描了，调用如下方法：
 
    mBleService.scanLeDevice(boolean enable, long scanPeriod);//启动或停止扫描Ble设备  

调用该方法需要传入一个boolean参数，true表示开始进行扫描Ble设备，false表示停止扫描，默认扫描10秒钟后停止，如果想要自己设定扫描的时间，可以输入一个long型参数，表示时间单位为毫秒，如3000表示3秒后停止扫描，扫描结束是会发出广播。  
  
扫描的结果可以从扫描监听或者广播接收器两种方式获取，设置方法如下：

####*监听方式接收扫描到的Ble设备*  
 
	//Ble扫描回调
	mBleService.setOnLeScanListener(new BleService.OnLeScanListener() {
	    @Override
	    public void onLeScan(BluetoothDevice device, int rssi, byte[] scanRecord) {
	        //每当扫描到一个Ble设备时就会返回，（扫描结果重复的库中已处理）
	    }
	}); 

（***注意***：设置监听一定要等到BleService服务绑定之后才进行，否则会造成空指针异常） 

####*广播接收扫描到的Ble设备*  

	private BroadcastReceiver bleReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            if (intent.getAction().equals(BleService.ACTION_BLUETOOTH_DEVICE)) {
                String tmpDevName = intent.getStringExtra("name");
                String tmpDevAddress = intent.getStringExtra("address");
                Log.i(TAG, "name: " + tmpDevName + ", address: " + tmpDevAddress);
            } else if (intent.getAction().equals(BleService.ACTION_SCAN_FINISHED)) {
                //扫描Ble设备停止
            }
        }
    };

两种方式获取都可以，但是监听方式获取的信息会多一些，可根据自己的需求进行选择使用那种方式。   
 

###*第六步：连接Ble服务*

当扫描到Ble设备后就可以进行连接操作了，调用如下方法： 

    mBleService.connect(String address);//连接Ble  
    mBleService.disconnect();//取消连接  

连接需要传入要连接的Ble设备的地址。  

连接状态可以从连接监听或者广播接收器两种方式获取，设置方法如下：  

####*监听获取Ble连接状态*  
 
	//Ble连接回调
	mBleService.setOnConnectListener(new BleService.OnConnectListener() {
	    @Override
	    public void onConnect(BluetoothGatt gatt, int status, int newState) {
	        if (newState == BluetoothProfile.STATE_DISCONNECTED) {
	            //Ble连接已断开
	        } else if (newState == BluetoothProfile.STATE_CONNECTING) {
	            //Ble正在连接
	        } else if (newState == BluetoothProfile.STATE_CONNECTED) {
	            //Ble已连接
	        } else if (newState == BluetoothProfile.STATE_DISCONNECTING) {
	            //Ble正在断开连接
	        }
	    }
	}); 

####*广播接收Ble连接状态* 

	private BroadcastReceiver bleReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            if (intent.getAction().equals(BleService.ACTION_GATT_CONNECTED)) {
                //Ble已连接
            } else if (intent.getAction().equals(BleService.ACTION_GATT_DISCONNECTED)) {
                //Ble连接已断开
            } 
        }
    };

当连接上Ble后会进行服务的获取，如果服务和特性不能发现，那么就不能进行特性的读写和设置GATT通知。服务发现监听设置如下：  

	//Ble服务发现回调
	mBleService.setOnServicesDiscoveredListener(new BleService.OnServicesDiscoveredListener() {
	    @Override
	    public void onServicesDiscovered(BluetoothGatt gatt, int status) {
	
	    }
	});

###*第七步：读写Ble特性和接收GATT通知*

连接上Ble并获取服务之后就可以对特性进行读写，设置GATT通知，操作如下：
 
    mBleService.setCharacteristicNotification();//设置通知  
    mBleService.readCharacteristic();//读取数据  
    mBleService.writeCharacteristic();//写入数据 

特性读取的数据和GATT通知接收数据设置OnDataAvailableListener监听获取，设置如下： 

	//Ble数据回调
	mBleService.setOnDataAvailableListener(new BleService.OnDataAvailableListener() {
	    @Override
	    public void onCharacteristicRead(BluetoothGatt gatt, BluetoothGattCharacteristic characteristic, int status) {
	        //处理特性读取返回的数据
	    }
	
	    @Override
	    public void onCharacteristicChanged(BluetoothGatt gatt, BluetoothGattCharacteristic characteristic) {
	        //处理通知返回的数据
	    }
	});
	
	
##*总结*	
	
最后小小总结下使用BleLib库进行Android低功耗蓝牙Ble的开发步骤：  

1. 添加BleLib库依赖
2. 绑定BleLib服务
3. 初始化操作
4. 打开蓝牙
5. 扫描Ble设备
6. 连接Ble服务
7. 读写Ble特性和接收GATT通知


##*Author*
 
- **Name**： Junk Chen  
- **Blog**： https://github.com/junkchen
- **Email**: junkchen@vip.qq.com
- **QQ群**： 365532949

2016/3/16 21:49:15 