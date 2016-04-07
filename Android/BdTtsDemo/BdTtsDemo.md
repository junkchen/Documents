# *Android Studio下Android应用开发集成百度语音合成使用方法样例*

首先，语音合成是指将文本信息转换成声音。意思就是将文本转化为声音，让你的应用开口说话。国内在业内比较有名的第三方语音合成平台有百度语音和科大讯飞。  

本博文集成的是百度语音合成，其主要特点是：  

- **完全永久免费**  
业界首创完全永久免费新形式，为开发者提供最流畅最自然的语音合成服务。完全免费，永久使用，彻底摆脱限制。

- **离线在线融合模式**  
SDK可以根据当前网络状况，自动判断使用本地引擎还是云端引擎进行语音合成，再也不用担心流量消耗!

- **多语言多音色可选**  
中文普通话、中英文混读、男声、女声任你选，更支持语速、音调、音量、音频码率设置，让你的应用拥有最甜美和最磁性的声音！

- **流畅自然的合成效果**  
语音合成技术业界领先，合成效果接近真人发声，流畅自然，且极具表现力，给你最舒适的听觉体验！

百度语音官方的Demo是在Eclipse环境下编写的，而在Android Studio中则有点小区别，下面请看百度语音合成使用详细步骤(一步一步操作不要跳跃心急吃不了热豆腐)：  

## *1、注册百度语音开发者平台* ##

注册百度账号，注册开发者信息，创建应用，可以得到 APP ID、 API Key、和 Secret Key，在开发过程中会使用这三个值进行授权，开通语音合成服务，若需要使用离线合成功能还需要申请离线授权。详细步骤请看[百度语音接入流程](http://yuyin.baidu.com/docs/detail/147) 。  

![](https://github.com/junkchen/Documents/raw/master/Android/BdTtsDemo/3.png)  

Key值查看
![](https://github.com/junkchen/Documents/raw/master/Android/BdTtsDemo/2.png)  

## *2、下载资源* ##

下载百度语音SDK，根据自己的需要下载，本样例下载的是***离在线融合语音合成SDK_Android版*** ， 地址： [http://yuyin.baidu.com/tts/download](http://yuyin.baidu.com/tts/download)  


## *3、集成百度语音指南* ##

### 3.1添加 jar 包和 so 库到工程  ###
 
将开发包中的 libs 目录整体拷贝到工程目录(Eclipse的用户)，libs 目录包括了jar包和各平台的 SO 库，开发者视应用需要可以进行删减。galaxy_lite.jar 是百度 Android 公共基础库，如果项目中还集成了其它百度 SDK，
如 Push SDK，在打包过程中出现类似如下的错误信息：

```log  
[2013-10-22  11:02:57  -  Dex  Loader]  Unable  to  execute  dex:  Multiple  dex  files  define 
Lcom/baidu/android/common/logging/Configuration; 
[2013-10-22  11:02:57  -  VoiceRecognitionDemo]  Conversion  to  Dalvik  format  failed:  Unable  to 
execute dex: Multiple dex files define Lcom/baidu/android/common/logging/Configuration; 
```

请将此 Jar 包移除。对于使用Android Studio的用户，应将libs目录中的jar包放在libs目录下，然后添加依赖; 而 .SO 库则应该放在jniLibs目录下, jniLibs目录与java、res在相同目录下。若没有相应的目录就自己创建。整个结构如下图：   

![](https://github.com/junkchen/Documents/raw/master/Android/BdTtsDemo/1.png)


### 3.2 添加语音合成资源文件 ###

将开发包中的 data 目录下的 dat 文件放到工程的assets目录下，assets目录与java、res在同一目录下，以便设置资源文件参数时使用。

### 3.3 权限声明 ###

使用百度语音需要声明以下权限： 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_SETTINGS" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
```

## *4、语音合成功能代码* ##

### 4、1 Tts初始化 ###

```java 
//获取 tts 实例 
speechSynthesizer = SpeechSynthesizer.getInstance(); 
//设置 app 上下文（必需参数） 
speechSynthesizer.setContext(Context); 
//设置 tts 监听器 
speechSynthesizer.setSpeechSynthesizerListener(SpeechSynthesizerListener);
 
//文本模型文件路径，文件的绝对路径  (离线引擎使用) 
speechSynthesizer.setParam(SpeechSynthesizer.PARAM_TTS_TEXT_MODEL_FILE, 
TEXT_MODEL_FILE_FULL_PATH_NAME); 

//声学模型文件路径，文件的绝对路径  (离线引擎使用) 
speechSynthesizer.setParam(SpeechSynthesizer.PARAM_TTS_SPEECH_MODEL_FILE, 
SPEECH_MODEL_FILE_FULL_PATH_NAME); 

//  本 地 授 权 文 件 路 径 , 如 未 设 置 将 使 用 默 认 路 径 . 设 置 临 时 授 权 文 件 路 径 ，
//LICENCE_FILE_NAME 请替换成临时授权文件的实际路径，仅在使用临时 license 文件时需要进行设置，
//如果在[应用管理]中开通了离线授权，不需要设置该参数，建议将该行代码删除（离线引擎） 
speechSynthesizer.setParam(SpeechSynthesizer.PARAM_TTS_LICENCE_FILE, 
LICENSE_FILE_FULL_PATH_NAME); 

//请替换为语音开发者平台上注册应用得到的 App ID (离线授权) 
speechSynthesizer.setAppId("your_app_id"); 
//请替换为语音开发者平台注册应用得到的 apikey 和 secretkey (在线授权) 
speechSynthesizer.setApiKey("your_api_key", "your_secret_key"); 

//授权检测接口 
AuthInfo authInfo = speechSynthesizer.auth(TtsMode); 
//引擎初始化接口 
speechSynthesizer.initTts(TtsMode);  
```    

***注意***：在初始化设置之前先把assets文件夹中的资源文件拷贝到SD卡中，以便使用。另外，离线授权临时文件有效期只有30天，若要长久使用语音离线合成应在应用管理中开通离线授权。

### 4、2合成并播放 ###

```java
mSpeechSynthesizer.speak(text);
```

该接口比较耗时，采用排队策略，调用后将自动加入合成队列，并按调用顺序进行合成和播放。  

好了，到此你的语音合成就可以使用了，若想要进行更多参数设置，请看[百度语音合成官方开发文档](http://yuyin.baidu.com/docs/tts/155) 和开发手册。

## *5、源码* ##

最后贴上我的源码。

Manifest文件: AndroidManifest.xml 

```xml  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.junkchen.bdttsdemo">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

Layout布局文件: activity_main.xml  

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    tools:context="com.junkchen.bdttsdemo.MainActivity">

    <EditText
        android:id="@+id/edt_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="start"
        android:minLines="5"
        android:text="Hi 我是百度语音合成，请输入要合成的语音内容" />

    <Button
        android:id="@+id/btn_speak"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="语音合成并播放" />
</LinearLayout>

```  

Java: MainActivity.java   

```java
package com.junkchen.bdttsdemo;

import android.os.Bundle;
import android.os.Environment;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.baidu.tts.answer.auth.AuthInfo;
import com.baidu.tts.client.SpeechError;
import com.baidu.tts.client.SpeechSynthesizer;
import com.baidu.tts.client.SpeechSynthesizerListener;
import com.baidu.tts.client.TtsMode;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;

public class MainActivity extends AppCompatActivity implements SpeechSynthesizerListener {
    private static final String TAG = "MainActivity";

    private SpeechSynthesizer mSpeechSynthesizer;//百度语音合成客户端
    private String mSampleDirPath;
    private static final String SAMPLE_DIR_NAME = "baiduTTS";
    private static final String SPEECH_FEMALE_MODEL_NAME = "bd_etts_speech_female.dat";
    private static final String SPEECH_MALE_MODEL_NAME = "bd_etts_speech_male.dat";
    private static final String TEXT_MODEL_NAME = "bd_etts_text.dat";
    private static final String LICENSE_FILE_NAME = "temp_license_2016-04-05";
    private static final String ENGLISH_SPEECH_FEMALE_MODEL_NAME = "bd_etts_speech_female_en.dat";
    private static final String ENGLISH_SPEECH_MALE_MODEL_NAME = "bd_etts_speech_male_en.dat";
    private static final String ENGLISH_TEXT_MODEL_NAME = "bd_etts_text_en.dat";
    private static final String APP_ID = "7957876";//请更换为自己创建的应用
    private static final String API_KEY = "cVN31pILxBhRNdGdlNHyeuyq";//请更换为自己创建的应用
    private static final String SECRET_KEY = "84e6987b56f11e6ee97e02ef25a2b4f0";//请更换为自己创建的应用

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initialEnv();
        initialTts();
        initView();
    }

    @Override
    protected void onDestroy() {
        this.mSpeechSynthesizer.release();//释放资源
        super.onDestroy();
    }

    private EditText edt_content;
    private Button btn_speak;

    private void initView() {
        edt_content = (EditText) findViewById(R.id.edt_content);
        btn_speak = (Button) findViewById(R.id.btn_speak);
        btn_speak.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String content = edt_content.getText().toString();
                mSpeechSynthesizer.speak(content);
                Log.i(TAG, ">>>say: " + edt_content.getText().toString());
            }
        });
    }

    /**
     * 初始化语音合成客户端并启动
     */
    private void initialTts() {
        //获取语音合成对象实例
        this.mSpeechSynthesizer = SpeechSynthesizer.getInstance();
        //设置Context
        this.mSpeechSynthesizer.setContext(this);
        //设置语音合成状态监听
        this.mSpeechSynthesizer.setSpeechSynthesizerListener(this);
        //文本模型文件路径 (离线引擎使用)
        this.mSpeechSynthesizer.setParam(SpeechSynthesizer.PARAM_TTS_TEXT_MODEL_FILE, mSampleDirPath + "/"
                + TEXT_MODEL_NAME);
        //声学模型文件路径 (离线引擎使用)
        this.mSpeechSynthesizer.setParam(SpeechSynthesizer.PARAM_TTS_SPEECH_MODEL_FILE, mSampleDirPath + "/"
                + SPEECH_FEMALE_MODEL_NAME);
        //本地授权文件路径,如未设置将使用默认路径.设置临时授权文件路径，LICENCE_FILE_NAME请替换成临时授权文件的实际路径，
		//仅在使用临时license文件时需要进行设置，如果在[应用管理]中开通了离线授权，
		//不需要设置该参数，建议将该行代码删除（离线引擎）
        this.mSpeechSynthesizer.setParam(SpeechSynthesizer.PARAM_TTS_LICENCE_FILE, mSampleDirPath + "/"
                + LICENSE_FILE_NAME);
        //请替换为语音开发者平台上注册应用得到的App ID (离线授权)
        this.mSpeechSynthesizer.setAppId(APP_ID);
        // 请替换为语音开发者平台注册应用得到的apikey和secretkey (在线授权)
        this.mSpeechSynthesizer.setApiKey(API_KEY, SECRET_KEY);
        //发音人（在线引擎），可用参数为0,1,2,3。。。
		//（服务器端会动态增加，各值含义参考文档，以文档说明为准。0--普通女声，1--普通男声，2--特别男声，3--情感男声。。。）
        this.mSpeechSynthesizer.setParam(SpeechSynthesizer.PARAM_SPEAKER, "0");
        // 设置Mix模式的合成策略
        this.mSpeechSynthesizer.setParam(SpeechSynthesizer.PARAM_MIX_MODE, SpeechSynthesizer.MIX_MODE_DEFAULT);
        // 授权检测接口(可以不使用，只是验证授权是否成功)
        AuthInfo authInfo = this.mSpeechSynthesizer.auth(TtsMode.MIX);
        if (authInfo.isSuccess()) {
            Log.i(TAG, ">>>auth success.");
        } else {
            String errorMsg = authInfo.getTtsError().getDetailMessage();
            Log.i(TAG, ">>>auth failed errorMsg: " + errorMsg);
        }
        // 引擎初始化tts接口
        mSpeechSynthesizer.initTts(TtsMode.MIX);
        // 加载离线英文资源（提供离线英文合成功能）
        int result =
                mSpeechSynthesizer.loadEnglishModel(mSampleDirPath + "/" + ENGLISH_TEXT_MODEL_NAME, mSampleDirPath
                        + "/" + ENGLISH_SPEECH_FEMALE_MODEL_NAME);
        Log.i(TAG, ">>>loadEnglishModel result: " + result);
    }

    @Override
    public void onSynthesizeStart(String s) {
        //监听到合成开始
        Log.i(TAG, ">>>onSynthesizeStart()<<< s: " + s);
    }

    @Override
    public void onSynthesizeDataArrived(String s, byte[] bytes, int i) {
        //监听到有合成数据到达
        Log.i(TAG, ">>>onSynthesizeDataArrived()<<< s: " + s);
    }

    @Override
    public void onSynthesizeFinish(String s) {
        //监听到合成结束
        Log.i(TAG, ">>>onSynthesizeFinish()<<< s: " + s);
    }

    @Override
    public void onSpeechStart(String s) {
        //监听到合成并开始播放
        Log.i(TAG, ">>>onSpeechStart()<<< s: " + s);
    }

    @Override
    public void onSpeechProgressChanged(String s, int i) {
        //监听到播放进度有变化
        Log.i(TAG, ">>>onSpeechProgressChanged()<<< s: " + s);
    }

    @Override
    public void onSpeechFinish(String s) {
        //监听到播放结束
        Log.i(TAG, ">>>onSpeechFinish()<<< s: " + s);
    }

    @Override
    public void onError(String s, SpeechError speechError) {
        //监听到出错
        Log.i(TAG, ">>>onError()<<< description: " + speechError.description + ", code: " + speechError.code);
    }

    private void initialEnv() {
        if (mSampleDirPath == null) {
            String sdcardPath = Environment.getExternalStorageDirectory().toString();
            mSampleDirPath = sdcardPath + "/" + SAMPLE_DIR_NAME;
        }
        File file = new File(mSampleDirPath);
        if (!file.exists()) {
            file.mkdirs();
        }
        copyFromAssetsToSdcard(false, SPEECH_FEMALE_MODEL_NAME, mSampleDirPath + "/" + SPEECH_FEMALE_MODEL_NAME);
        copyFromAssetsToSdcard(false, SPEECH_MALE_MODEL_NAME, mSampleDirPath + "/" + SPEECH_MALE_MODEL_NAME);
        copyFromAssetsToSdcard(false, TEXT_MODEL_NAME, mSampleDirPath + "/" + TEXT_MODEL_NAME);
        copyFromAssetsToSdcard(false, LICENSE_FILE_NAME, mSampleDirPath + "/" + LICENSE_FILE_NAME);
        copyFromAssetsToSdcard(false, "english/" + ENGLISH_SPEECH_FEMALE_MODEL_NAME, mSampleDirPath + "/"
                + ENGLISH_SPEECH_FEMALE_MODEL_NAME);
        copyFromAssetsToSdcard(false, "english/" + ENGLISH_SPEECH_MALE_MODEL_NAME, mSampleDirPath + "/"
                + ENGLISH_SPEECH_MALE_MODEL_NAME);
        copyFromAssetsToSdcard(false, "english/" + ENGLISH_TEXT_MODEL_NAME, mSampleDirPath + "/"
                + ENGLISH_TEXT_MODEL_NAME);
    }

    /**
     * 将工程需要的资源文件拷贝到SD卡中使用（授权文件为临时授权文件，请注册正式授权）
     *
     * @param isCover 是否覆盖已存在的目标文件
     * @param source
     * @param dest
     */
    public void copyFromAssetsToSdcard(boolean isCover, String source, String dest) {
        File file = new File(dest);
        if (isCover || (!isCover && !file.exists())) {
            InputStream is = null;
            FileOutputStream fos = null;
            try {
                is = getResources().getAssets().open(source);
                String path = dest;
                fos = new FileOutputStream(path);
                byte[] buffer = new byte[1024];
                int size = 0;
                while ((size = is.read(buffer, 0, 1024)) >= 0) {
                    fos.write(buffer, 0, size);
                }
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (fos != null) {
                    try {
                        fos.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
                try {
                    if (is != null) {
                        is.close();
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```

以上就是主要部分的源码，大家可以自己去试试看,想要完整源码请加群找群主。  

将文本转化为声音，让你的应用开口说话。  

如有问题欢迎加qq群讨论学习：***365532949***   
HomePage：[http://junkchen.com](http://junkchen.com)