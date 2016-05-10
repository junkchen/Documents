# **动态请求权限** #

从Android 6.0（API 23）开始，允许用户在应用运行时决定是否允许权限，而不是在应用安装的时候。这种方法简化了应用的安装过程，因为用户在安装或更新应用的时候不需要允许权限。他也让用户对应用的功能有更多的控制；例如，用户可以选择给予相机应用相机的权限但是不允许使用设备位置的权限。用户可进入应用设置随时撤销权限。  

*系统权限被分为两种类型，正常的（normal）和敏感的（dangerous）：*  

- *正常的权限不会直接让用户的隐私处于危险中。如果你的应用在清单文件中列入了正常的权限，系统会自动允许这些权限。*  

- *敏感权限给予应用方位用户的机密数据。如果你的应用在清单文件中列入危险类权限，会明确地让用户对你的应用允许权限。* 

更多信息请看   ***[Normal and Dangerous Permissions](http://developer.android.com/guide/topics/security/permissions.html#normal-dangerous)***.

在所有的Android版本中，你的应用需要在清单文件中去申明它需要的正常的和危险的权限。然而，声明的影响是不同的，依赖于系统版本和你应用的目标SDK等级：  

- 如果设备运行在Android 5.1或更低，或者你的应用的 target SDK是22或者更低；如果你在清单文件中加入了敏感权限，当他们在安装应用的时候必须同意权限；如果他们不同意权限，系统则不会安装应用。  

- 如果设备运行在Android 6.0或更高的版本，或者你的应用的 target SDK是23或者更高。应用必须在manifest文件中加入权限，而且在应用运行过程中必须在它需要的时候请求每一个危险的权限。用户可以允许或者拒绝每一个权限，即使用户拒绝了一个权限的请求而应用可以在限制功能地继续运行。

**注意：** 从Android6.0（API 23）开始，用户可以在任何时候撤销任何应用的权限，即使应用目标SDK低于23。你应该测试你的用用验证它正常的表现当它缺少需要的权限，不管你的应用的target SDK等级是多少。  

## **检查权限** ##

如果你的应用需要一个敏感权限，你必须检查是否具有权限当你每次执行一个操作需要一个权限时。用户总是可以自由地撤销权限，所以，即使应用昨天使用了相机，但是今天可能没有相机权限。  

为了检查你是否有该权限，调用 ***[ContextCompat.checkSelfPermission()](http://developer.android.com/reference/android/support/v4/content/ContextCompat.html#checkSelfPermission(android.content.Context, java.lang.String))***方法。例如，下面片段展示了如何检查一个Activity是否具有日历权限：  

```java
// Assume thisActivity is the current activity
int permissionCheck = ContextCompat.checkSelfPermission(thisActivity,
        Manifest.permission.WRITE_CALENDAR);
```

如果一个用拥有权限，这个方法会返回 ***[PackageManager.PERMISSION_GRANTED](http://developer.android.com/reference/android/content/pm/PackageManager.html#PERMISSION_GRANTED)*** ，应用可以继续进行操作。如果应用没有权限，方法会返回 ***[PERMISSION_DENIED](http://developer.android.com/reference/android/content/pm/PackageManager.html#PERMISSION_DENIED)*** ，应用必须去想用户请求该权限。  

## **请求权限** ##

如果你的应用需要一个敏感权限加入到manifest文件中，他必须征求用户允许权限。Android提供了一些方法让你可以去请求一个权限。调用这些方法会弹出一个标准的Android弹出框，你不能自定义。  

### **解释你的应用为什么需要权限** ###

在某些情况中，你可能想要帮助用户理解你的应用个为什么需要一个权限。例如，如果一个用户运行一个相机应用，应用请求使用相机权限，用户对此并不会感到惊奇，但是，用户并不理解为什么应用想要访问用户的位置或者联系人。在你请求一个权限之前，你应该考虑向用户提供一个解释。记住，如果你不想用解释说服用户；如果你提供太多的解释，用户可能发现应用令他沮丧并卸载它。  

如果用户已经关闭了权限请求，你可能需要提供一个解释。如果用户坚持尝试使用一个功能但需要请求一个权限，但总是关闭权限请求，可能说明用户没有理解应用为什么需要该功能这个权限。在这种情况下，去展示一个解释可能是一个好主意。  

为了帮助这种情况，用户可能需要一个解释，Android提供了一个实用的方法，***[shouldShowRequestPermissionRationale()](http://developer.android.com/reference/android/support/v4/app/ActivityCompat.html#shouldShowRequestPermissionRationale(android.app.Activity, java.lang.String))*** 。如果应用以前请求过这个权限并且被用户拒绝了那么这个方法会返回 *true* 。  

**注意：** 如果用户在权限请求系统弹出框关闭权限请求而且选择不在提示，这个方法返回 *false* 。如果一个设备已经禁止了应用的那个权限这个方法也会返回 *false*。  

### **请求你需要的权限** ###

如果你的应用没有你需要的权限，你必须调用 **[requestPermissions()](http://developer.android.com/reference/android/support/v4/app/ActivityCompat.html#requestPermissions(android.app.Activity, java.lang.String[], int))** 方法去请求合适的权限。你的应用通过权限请求，会有一个整型的请求码唯一标识这个权限请求。这个方法是异步的，当用户操作这个弹出框后他会立即返回，系统调用应用的回调方法跟结果。  

下面代码是检查应用是否有权限去阅读用的联系人，如果有需要就请求权限：  

```java
// Here, thisActivity is the current activity
if (ContextCompat.checkSelfPermission(thisActivity,
                Manifest.permission.READ_CONTACTS)
        != PackageManager.PERMISSION_GRANTED) {

    // Should we show an explanation?
    if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
            Manifest.permission.READ_CONTACTS)) {

        // Show an expanation to the user *asynchronously* -- don't block
        // this thread waiting for the user's response! After the user
        // sees the explanation, try again to request the permission.

    } else {

        // No explanation needed, we can request the permission.

        ActivityCompat.requestPermissions(thisActivity,
                new String[]{Manifest.permission.READ_CONTACTS},
                MY_PERMISSIONS_REQUEST_READ_CONTACTS);

        // MY_PERMISSIONS_REQUEST_READ_CONTACTS is an
        // app-defined int constant. The callback method gets the
        // result of the request.
    }
}
```

**注意：** 当你的应用调用 **requestPermissions()**方法时，系统会向用户展示标准的对话框。你不能配置或者修对话框。如果你需要想用提供任何信息或者解释，你应该在调用 **requestPermissions()** 前去进行操作，正如 **解释你的应用为什么需要权限** 中的描述。  

### **处理权限请求响应** ###

当你的应用请求权限时，系统会想用展示一个对话框。当用户做出回复后，系统会调用你应用的 **[onRequestPermissionsResult()](http://developer.android.com/reference/android/support/v4/app/ActivityCompat.OnRequestPermissionsResultCallback.html#onRequestPermissionsResult(int, java.lang.String[], int[]))** 方法。你的应用必须重写这个方法，去找出权限是否被允许。这个回调接口跟**requestPermissions()**方法有一个相同的请求码。例如，如果一个应用请求 **[READ_CONTACTS](http://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS)** 访问，他的回调方法可能像下面这样：  

```java
@Override
public void onRequestPermissionsResult(int requestCode,
        String permissions[], int[] grantResults) {
    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
            // If request is cancelled, the result arrays are empty.
            if (grantResults.length > 0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {

                // permission was granted, yay! Do the
                // contacts-related task you need to do.

            } else {

                // permission denied, boo! Disable the
                // functionality that depends on this permission.
            }
            return;
        }

        // other 'case' lines to check for other
        // permissions this app might request
    }
}
```

对话框会展示你的应用需要的权限所述的权限组，它不会列出具体的权限。例如，如果你请求 **READ_CONTACTS** 权限，系统对话框仅仅说你的应用需要访问设备的联系人。对于每个权限组用户只需要允许一次。如果你的应用请求任何其他的权限在那个组里，系统会自动地允许他们。当你请求权限时，如果用户在系统对话框中已经明确的允许你的请求，系统调用**onRequestPermissionsResult()** 方法，并且通过 **PERMISSION_GRANTED**。  

**注意：** 你的应用仍然需要明确地请求每一个它需要的权限，即使用户已经允许了同组里的另一个权限。另外，在未来的Android版本中群组的权限分组可能会有改变。你的代码不应该依赖特定的权限是不是在一个组里。  

例如，假设你应用的manifest文件中既有 **READ_CONTACTS** 权限又有 **WRITE_CONTACTS** 权限。如果你请求 **READ_CONTACTS** 并且用户允许了改权限，然后你请求 **WRITE_CONTACTS** ，系统会立即允许权限而不和用户交互。  

如果用户拒绝一个权限请求，你的应用应该采取适当的处理。例如，你的应用可能展示一个对话框，解释它为什么需要权限去执行用户请求的操作。  

当系统询问用户允许权限时，用户可以有选择的告诉系统不用在此询问权限。在这种情况下，任何时候，应用使用 **requestPermissions()** 方法去在次请求权限时，系统会立即拒绝请求。如果用户已经明确的拒绝了你的请求，系统会调用 **onRequestPermissionsResult()** 方法并且通过**PERMISSION_DENIED** 。这意味着当你调用 **requestPermissions()** 方法时，你不能在用户操作的地方产生预期的效果。  

## **正常权限（Normal Permissions）** ##

在API 23中，下面权限被定义为正常权限

- ACCESS_LOCATION_EXTRA_COMMANDS
- ACCESS_NETWORK_STATE
- ACCESS_NOTIFICATION_POLICY
- ACCESS_WIFI_STATE
- BLUETOOTH
- BLUETOOTH_ADMIN
- BROADCAST_STICKY
- CHANGE_NETWORK_STATE
- CHANGE_WIFI_MULTICAST_STATE
- CHANGE_WIFI_STATE
- DISABLE_KEYGUARD
- EXPAND_STATUS_BAR
- GET_PACKAGE_SIZE
- INSTALL_SHORTCUT
- INTERNET
- KILL_BACKGROUND_PROCESSES
- MODIFY_AUDIO_SETTINGS
- NFC
- READ_SYNC_SETTINGS
- READ_SYNC_STATS
- RECEIVE_BOOT_COMPLETED
- REORDER_TASKS
- REQUEST_IGNORE_BATTERY_OPTIMIZATIONS
- REQUEST_INSTALL_PACKAGES
- SET_ALARM
- SET_TIME_ZONE
- SET_WALLPAPER
- SET_WALLPAPER_HINTS
- TRANSMIT_IR
- UNINSTALL_SHORTCUT
- USE_FINGERPRINT
- VIBRATE
- WAKE_LOCK
- WRITE_SYNC_SETTINGS

## **敏感权限(Dangerous permissions)** ##

| Permission Group | Permissions |
| ---------------- | ----------- |
| [CALENDAR](http://developer.android.com/reference/android/Manifest.permission_group.html#CALENDAR) | <li> [READ_CALENDAR](http://developer.android.com/reference/android/Manifest.permission.html#READ_CALENDAR) <li> [WRITE_CALENDAR](http://developer.android.com/reference/android/Manifest.permission.html#WRITE_CALENDAR) |
| [CAMERA](http://developer.android.com/reference/android/Manifest.permission_group.html#CAMERA) | <li> [CAMERA](http://developer.android.com/reference/android/Manifest.permission.html#CAMERA) |
| CONTACTS | <li>READ_CONTACTS <li>WRITE_CONTACTS <li> GET_ACCOUNTS |
| LOCATION | <li>ACCESS_FINE_LOCATION <li>ACCESS_COARSE_LOCATION |
| MICROPHONE | <li>RECORD_AUDIO |
| PHONE | <li>READ_PHONE_STATE <li>CALL_PHONE <li>READ_CALL_LOG <li>WRITE_CALL_LOG <li>ADD_VOICEMAIL <li>USE_SIP <li>PROCESS_OUTGOING_CALLS |
| SENSORS | <li>BODY_SENSORS |
| SMS | <li>SEND_SMS <li>RECEIVE_SMS <li>READ_SMS <li>RECEIVE_WAP_PUSH <li>RECEIVE_MMS |
| STORAGE | <li>READ_EXTERNAL_STORAGE <li>WRITE_EXTERNAL_STORAGE |  

## **原文链接** ##

- [http://developer.android.com/training/permissions/requesting.html#perm-request](http://developer.android.com/training/permissions/requesting.html#perm-request)

- [http://developer.android.com/guide/topics/security/permissions.html#normal-dangerous](http://developer.android.com/guide/topics/security/permissions.html#normal-dangerous)

- [http://developer.android.com/guide/topics/security/normal-permissions.html](http://developer.android.com/guide/topics/security/normal-permissions.html)  

鄙人英语能力有限，翻译难免有误，请读者多多包含！  

欢迎加QQ群交流： **365532949 **
**Homepage:** [http://junkchen.com](http://junkchen.com)