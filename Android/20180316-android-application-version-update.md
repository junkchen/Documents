# **Android application version update** #

Android 应用版本更新：  

1. 客户端请求版本检查
2. 下载新版本客户端
3. 安装客户端

## **新版本检查** ##

开发人员将应用的最新版本上传至服务器，在客户端中通过上传当前版本的 versionCode 到服务器中，服务器根据 versionCode 判断客户端是否需要更新，如需更新则将新版本的必要信息（如versionCode、versionName、downloadUrl等）反馈至客户端。  

获取当前应用的 versionCode ：  

```.java
try {
    PackageManager packageManager = getPackageManager();
    PackageInfo packageInfo = packageManager.getPackageInfo(
			getPackageName(), PackageManager.GET_ACTIVITIES);
    int versionCode = packageInfo.versionCode;
    String versionName = packageInfo.versionName;
} catch (PackageManager.NameNotFoundException e) {
    e.printStackTrace();
}
```

当获取到 versionCode 就可以向服务器发送检查新版本的请求，如果有新版本服务器会返回新版本的下载地址。  

## **下载新版本** ##

下载新版本应用可以使用系统的 DownloadManager 。 DownloadManager 可以通过 Context 的 getSystemService() 方法获取，需要传入参数 Context.DOWNLOAD_SERVICE 。 DownloadManager 下载结束时会发送广播 DownloadManager.ACTION_DOWNLOAD_COMPLETE ，因此可以注册该广播监听下载结果。

获取 DownloadManager ：  

```.java
mDownloadManager = (DownloadManager) mContext.getSystemService(Context.DOWNLOAD_SERVICE);
```

下载 apk 文件：  

```.java
Uri downUri = Uri.parse(url);
Request request = new Request(downUri);
//Notify after download completed
request.setNotificationVisibility(Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
String subPath = downUri.getLastPathSegment();
request.setDestinationInExternalPublicDir(Environment.DIRECTORY_DOWNLOADS, subPath);
mDownloadId = mDownloadManager.enqueue(request);
```

广播监听下载结果：  

```.java
mContext.registerReceiver(mDownloadReceiver, new IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE));

private BroadcastReceiver mDownloadReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            if (DownloadManager.ACTION_DOWNLOAD_COMPLETE.equals(intent.getAction())) {
//                long downloadId = intent.getLongExtra(DownloadManager.EXTRA_DOWNLOAD_ID, -1);
                DownloadManager.Query query = new DownloadManager.Query();
                query.setFilterById(mDownloadId);
                Cursor cursor = mDownloadManager.query(query);
                if (cursor.moveToFirst()) {
				    int status = cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_STATUS));
				    switch (status) {
				        case DownloadManager.STATUS_PAUSED://下载暂停
				            Log.i(TAG, "checkStatus: download paused");
				            break;
				        case DownloadManager.STATUS_PENDING://下载延迟
				            Log.i(TAG, "checkStatus: download pending");
				            break;
				        case DownloadManager.STATUS_RUNNING://正在下载
				            Log.i(TAG, "checkStatus: download running");
				            break;
				        case DownloadManager.STATUS_SUCCESSFUL://下载完成
				            Log.i(TAG, "checkStatus: download successful");
				            installApk();
				            break;
				        case DownloadManager.STATUS_FAILED://下载失败
				            Log.i(TAG, "checkStatus: download failed");
				            Toast.makeText(mContext, "Download failed", Toast.LENGTH_SHORT).show();
				            break;
				    }
				}

				mContext.unregisterReceiver(mDownloadReceiver);
            }
        }
    };

```

## **安装 apk** ##

使用 DownloadManager 下载时的 id 可以获取到下载文件的 uri ，然后调用系统安装界面开始安装。  

```.java
private void installApk() {
    Uri downloadFileUri = mDownloadManager.getUriForDownloadedFile(mDownloadId);
    if (downloadFileUri != null) {
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setDataAndType(downloadFileUri, "application/vnd.android.package-archive");
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        mContext.startActivity(intent);
    }
}
```

下载并安装可以封装一个工具类 VersionUpdateUtils ：  

```.java
public class VersionUpdateUtils {
    private static final String TAG = VersionUpdateUtils.class.getSimpleName();

    volatile private static VersionUpdateUtils mInstance;
    private Context mContext;
    private DownloadManager mDownloadManager;
    private long mDownloadId;

    private VersionUpdateUtils(Context context) {
        this.mContext = context;
        mDownloadManager = (DownloadManager) mContext.getSystemService(Context.DOWNLOAD_SERVICE);
    }

    public static VersionUpdateUtils getInstance(Context context) {
        if (null == mInstance) {
            synchronized (VersionUpdateUtils.class) {
                if (null == mInstance) {
                    mInstance = new VersionUpdateUtils(context);
                }
            }
        }
        return mInstance;
    }

    public void downloadApk(String url) {
        Uri downUri = Uri.parse(url);
        Request request = new Request(downUri);
        //Notify after download completed
        request.setNotificationVisibility(Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
        String subPath = downUri.getLastPathSegment();
        request.setDestinationInExternalPublicDir(Environment.DIRECTORY_DOWNLOADS, subPath);
        mDownloadId = mDownloadManager.enqueue(request);
        mContext.registerReceiver(mDownloadReceiver, new IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE));
    }

    private BroadcastReceiver mDownloadReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            if (DownloadManager.ACTION_DOWNLOAD_COMPLETE.equals(intent.getAction())) {
//                long downloadId = intent.getLongExtra(DownloadManager.EXTRA_DOWNLOAD_ID, -1);
                checkStatus();
                mContext.unregisterReceiver(mDownloadReceiver);
                mInstance = null;
                System.gc();
            }
        }
    };

    private void checkStatus() {
        DownloadManager.Query query = new DownloadManager.Query();
        query.setFilterById(mDownloadId);
        Cursor cursor = mDownloadManager.query(query);
        if (cursor.moveToFirst()) {
            int status = cursor.getInt(cursor.getColumnIndex(DownloadManager.COLUMN_STATUS));
            switch (status) {
                case DownloadManager.STATUS_PAUSED://下载暂停
                    Log.i(TAG, "checkStatus: download paused");
                    break;
                case DownloadManager.STATUS_PENDING://下载延迟
                    Log.i(TAG, "checkStatus: download pending");
                    break;
                case DownloadManager.STATUS_RUNNING://正在下载
                    Log.i(TAG, "checkStatus: download running");
                    break;
                case DownloadManager.STATUS_SUCCESSFUL://下载完成
                    Log.i(TAG, "checkStatus: download successful");
                    installApk();
                    break;
                case DownloadManager.STATUS_FAILED://下载失败
                    Log.i(TAG, "checkStatus: download failed");
                    Toast.makeText(mContext, "Download failed", Toast.LENGTH_SHORT).show();
                    break;
            }
        }
    }

    private void installApk() {
        Uri downloadFileUri = mDownloadManager.getUriForDownloadedFile(mDownloadId);
        if (downloadFileUri != null) {
            Intent intent = new Intent(Intent.ACTION_VIEW);
            intent.setDataAndType(downloadFileUri, "application/vnd.android.package-archive");
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            mContext.startActivity(intent);
        }
    }

}
```

## **自动点击安装** ##

上面所有操作结束后，应用就打开了安装界面，但接下来就需要人为点击才能真正开始安装。要实现模拟点击可以使用 AccessibilityService 。需要自定义一个 service 继承自 AccessibilityService ，然后再在资源文件夹下 res/xml/ 定义一个服务的配置文件 ，最后在 AndroidManifest 文件中注册 service 。  

自定义 service ：  

```.java
public class LiKangAccessibilityService extends AccessibilityService {
	@Override
    public void onAccessibilityEvent(AccessibilityEvent event) {
	
	}

	@Override
    public void onInterrupt() {

    }
}
```

定义 service 配置文件  

```.xml
<?xml version="1.0" encoding="utf-8"?>
<accessibility-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:accessibilityEventTypes="typeNotificationStateChanged|typeWindowStateChanged|typeWindowContentChanged|typeWindowsChanged"
    android:accessibilityFeedbackType="feedbackGeneric"
    android:accessibilityFlags="flagDefault"
    android:canRetrieveWindowContent="true"
    android:description="@string/app_name"
    android:notificationTimeout="100"
    android:packageNames="com.android.packageinstaller" />
```

在 AndroidManifest 文件中注册  

```.xml
<service
    android:name="com.lkyiliao.healthcare.community.service.LiKangAccessibilityService"
    android:enabled="true"
    android:exported="true"
    android:label="@string/app_name"
    android:permission="android.permission.BIND_ACCESSIBILITY_SERVICE">
    <intent-filter>
        <action android:name="android.accessibilityservice.AccessibilityService" />
    </intent-filter>
    <meta-data
        android:name="android.accessibilityservice"
        android:resource="@xml/accessibility_service_config" />
</service>
```

如果有出现在服务配置文件中 packageNames 中的应用出现，则 自定义的服务会回调 onAccessibilityEvent() 方法，在该方法中可以根据 event.getType() 以及 event.getPackageName() 进行判断。  

可以使用 Tools|Android Device Monitor|Hierarchy View 查看安装窗口的信息。查看可以知：  

安装开始
package: com.android.packageinstaller, com.android.systemui
TextView 荔康医疗 com.android.packageinstaller:id/app_name
Button 安装 com.android.packageinstaller:id/ok_button
Button 取消 com.android.packageinstaller:id/cancel_button
<p>
安装完成且安装成功
TextView 荔康医疗 com.android.packageinstaller:id/app_name
Button 完成 com.android.packageinstaller:id/done_button
Button 打开 com.android.packageinstaller:id/launch_button

```.java
int eventType = event.getEventType();
String packageName = event.getPackageName().toString();//com.android.systemui
String className = event.getClassName().toString();//android.app.Dialog
Log.i(TAG, "onAccessibilityEvent: eventType: " + eventType
        + ", packageName: " + packageName + ", className: " + className);
switch (eventType) {
    case AccessibilityEvent.TYPE_WINDOW_STATE_CHANGED: {
        if (packageName.equals("com.android.packageinstaller")) {
            //应用安装开始时的窗口
            Log.i(TAG, "onAccessibilityEvent: Enter application install");
            AccessibilityNodeInfo nodeInfo = getRootInActiveWindow();
            if (null != nodeInfo) {
                List<AccessibilityNodeInfo> appNameNode =
                        nodeInfo.findAccessibilityNodeInfosByViewId("com.android.packageinstaller:id/app_name");//荔康医疗

                List<AccessibilityNodeInfo> okButtonNode =
                        nodeInfo.findAccessibilityNodeInfosByViewId("com.android.packageinstaller:id/ok_button");//安装
                List<AccessibilityNodeInfo> cancelButtonNode =
                        nodeInfo.findAccessibilityNodeInfosByViewId("com.android.packageinstaller:id/cancel_button");//取消

                String appName = appNameNode.get(0).getText().toString();
                String okText = okButtonNode.get(0).getText().toString();
                String cancelText = cancelButtonNode.get(0).getText().toString();
                Log.i(TAG, "onAccessibilityEvent: appName: " + appName +
                        ", okText: " + okText + ", cancelText: " + cancelText);

                if (appName.equals("荔康医疗")) {
                    try {
                        Thread.sleep(300);
                        okButtonNode.get(0).performAction(AccessibilityNodeInfo.ACTION_CLICK);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

                nodeInfo.recycle();
            }
        }
    }
    break;
    case AccessibilityEvent.TYPE_WINDOW_CONTENT_CHANGED: {
        if (packageName.equals("com.android.packageinstaller")) {
            //应用安装成功时的窗口
            Log.i(TAG, "onAccessibilityEvent: Enter application install finish");
            AccessibilityNodeInfo nodeInfo = getRootInActiveWindow();
            if (null != nodeInfo) {

                List<AccessibilityNodeInfo> appNameNode =
                        nodeInfo.findAccessibilityNodeInfosByViewId("com.android.packageinstaller:id/app_name");//荔康医疗
                List<AccessibilityNodeInfo> launchBtnNode =
                        nodeInfo.findAccessibilityNodeInfosByViewId("com.android.packageinstaller:id/launch_button");//打开
                List<AccessibilityNodeInfo> doneBtnNode =
                        nodeInfo.findAccessibilityNodeInfosByViewId("com.android.packageinstaller:id/done_button");//完成

                //安装有可能失败
                if (appNameNode.isEmpty() || launchBtnNode.isEmpty() || doneBtnNode.isEmpty()) {
                    nodeInfo.recycle();
                    return;
                }

                String appName = appNameNode.get(0).getText().toString();
                String launchText = launchBtnNode.get(0).getText().toString();
                String doneText = doneBtnNode.get(0).getText().toString();
                Log.i(TAG, "onAccessibilityEvent: appName: " + appName +
                        ", launchText: " + launchText + ", doneText: " + doneText);

                if (appName.equals("荔康医疗")) {
                    try {
                        Thread.sleep(300);
                        launchBtnNode.get(0).performAction(AccessibilityNodeInfo.ACTION_CLICK);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

                nodeInfo.recycle();
            }
        }
    }
    break;
}
```

最后记得在无障碍中开启该服务。  

