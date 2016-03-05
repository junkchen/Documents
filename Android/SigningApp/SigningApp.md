# Signing Your Applications #

Android要求所有的应用在被安装之前进行数字签名认证。Android通过这个数字证书确定应用的作者，然这个证书并不需要特定机构的签字。Android一般使用自己签名的证书，开发者自己持有证书的密钥。  

你可以在调试或发布模式下对应用签名，一般情况下，在调试模式下，Android SDK会为应用自动生成一个签名证书，但是在发布模式下签名应用，你需要生成自己的证书。调试模式下的签名的应用不能进行对外分发。  

----------

## 发布模式下签名应用 ##

1、 创建keystore，keystore是一个包含私人密钥集合的二进制文件，请保存在安全且秘密的地方。   

2、 创建私人密钥。私人密钥代表标识应用的组织或团体，如个人或公司。  

3、 在app Module的build文件中添加签名配置：  

	...
	android {
	    ...
	    defaultConfig { ... }
	    signingConfigs {
	        release {
	            storeFile file("myreleasekey.keystore")
	            storePassword "password"
	            keyAlias "MyReleaseKey"
	            keyPassword "password"
	        }
	    }
	    buildTypes {
	        release {
	            ...
	            signingConfig signingConfigs.release
	        }
	    }
	}
	...  

4、从Android Studio中请求assembleRelease构建任务。  

在包中app/build/apk/app-release.apk 文件就是发布签名打包的。  

**注意**：在build文件中包含密码是不安全的，因此你要在build文件中配置的密码可以从系统环境变量或者进程提示中获取这些密码。  

从环境变量中获取：  

	storePassword System.getenv("KSTOREPWD")
	keyPassword System.getenv("KEYPWD")  

从进程提示的命令行中获取：  
	
	storePassword System.console().readLine("\nKeystore password: ")
	keyPassword System.console().readLine("\nKey password: ")  

当你完成这些操作后，就可以在应用市场中发布自己的应用了。  

**警告**： 你必须确保keystore和私人密钥的安全和私密，确保安全备份。如果你在应用市场中发布后，丢失了签名改应用的密钥库，你将不能进行任何更新，因为你对该应用的所有版本进行签名都必须用相同的密钥。  

----------

## Android Studio中签名应用 ##

1、在菜单栏点击 **Build > Generate Signed APK**.  

2、在Generate Signed APK Wizard窗口，点击**Create new**去创建一个新的签名证书。如果已经有keystore可以直接进入第4步。  

3、在**New Key Store**窗口中，提供相应的信息。如果你的密钥的有效期设置为25年，那么你的应用在有效期限内更新都必须使用同一个密钥。  

4、在**Generate Signed APK Wizard**窗口中，选择一个密钥库，一个密钥，输入密码，然后点击**Next**。  

5、在这个窗口，选择一个签名应用的保存位置然后点击**Finish**。