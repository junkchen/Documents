# Android Studio 生成 APK 时指定文件名

Android Studio 编译 APK 时默认文件名为 app-debug.apk 或者 app-release.apk 。我们可以在 Module 的 build.gradle 文件中进行配置指定生成的文件名。  

After Android Studio 3.0 and Gradle 4.1  

```gradle
apply plugin: 'com.android.application'
...
android {
	...
	defaultConfig {...}
	// 指定生成应用的文件名
	applicationVariants.all { variant ->
	    variant.outputs.all {
	        outputFileName = "app_name" +
	                "-v" + defaultConfig.versionName +
	                "-" + defaultConfig.versionCode +
	                "-" + buildType.name +
	                "-" + new Date().format("yyMMddHHmm") +
	                ".apk"
	    }
	}
}
```

Before Android Studio 3.0 and Gradle 4.1   

```gradle
apply plugin: 'com.android.application'

android {
	...
	defaultConfig {...}
	// 指定生成应用的文件名
	applicationVariants.all { variant ->
        variant.outputs.each { output ->
            def outputFile = output.outputFile
            def filename
            if (outputFile != null && outputFile.name.endsWith(".apk")) {
                if (variant.buildType.name.equals("release")) {
                    filename = "myapp-${defaultConfig.versionName}-${defaultConfig.versionCode}.apk"
                } else if (variant.buildType.name.equals("debug")) {
                    filename = "myapp-${defaultConfig.versionName}-${defaultConfig.versionCode}-debug.apk"
                }
                output.outputFile = new File(outputFile.parent, filename)
            }
        }
    }
}
```