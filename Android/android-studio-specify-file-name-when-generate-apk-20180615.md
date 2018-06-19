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

## Android 打包 apk 文件命名规则

常见的 apk 打包命名规则：  

```
[应用名称]-[版本号]-[版本类型]-[打包时间]-[渠道号].apk
```

### 应用名称
 
填写 app 的名称，如 Myapp。 


### 版本号
 
填写当前应用的版本号，目前主流的格式为x.x.x(主版本号.次版本号.修改编号)，如0.0.1、1.5.11。  
 
- 主版本号  
  主版本号一般以0位起始数字(有些以1为起始数字)  
  第一位数字表示我们应用的主版本号为1，当次版本号>9时，主版本号+1，次版本号重新从0开始; 

- 次版本号  
  第一个小数点后的数字表示我们的次版本号，当版本内容发生较大改动(功能、界面)发生改变时，次版本号+1，修改编号重新从0开始;  
 
- 修改编号  
  第二个小数点后的数字表示我们应用的修改编号,当我们在当前次要版本进行相关的BUG修复、性能提升而对产品的功能、界面没有较大的影响时(即用户主观上不能明显感受到),一般是修改编号在原有基础上+1累计即可。  

### 版本类型
 
版本类型一般分为alpha(内部测试版)、beta(公测版)、rc(Release Candidate/预上线版本)、release(线上版本)四种版本。  
 
- alpha  
  内部测试版，属于刚完成研发阶段未进行测试且潜在BUG较多的版本，该版本一般部署测试环境，主要提供给内部人员、专业的测试人员进行体验验证。 

- beta  
  公共测试版，在alpha版的基础上做过一些修改，但任然存在部分缺陷且后期功能方面还会有所增加，该版本已经可以部署预上线/线上环境了，主要用于提供给特定的忠实用户体验，内部演示汇报，产品、测试人员二次验证等。 

- rc(Release Candidate)  
  预上线(候选)版本，该版本的功能不会发生改变，在beta版本的基础上，完成了当前版本全部功能的设计并修复了大部分的BUG，是一个比较稳定的版本，已经可以用来放在应用市场上供用户使用，在这个版本以及下面的release版本中，我们可以放心的部署线上环境了。 

- release   
  线上(发行)版本，该版本已经完成对所有BUG的修复及功能模块优化，基本上不会再发生改动，是适用于所有用户群体的稳定版本。

为了简化版本类型分类，也可以分为alpha、beta、release三个版本类型标记。  

### 打包时间 

apk的打包时间我们使用年月日时分组成，其中年份取值后两位(如2016取值16)，月、日各占两位不足两位补零(如1月2日应取值0102),时、分取值与月、日相似。  

如当前时间为2016年1月2日6时5分，打包时间值为1601020605。  

### 渠道号
 
在应用中增加渠道号的标识可以有效帮助我们快速了解应用在各个渠道(应用市场--区分数据统计来源)上的统计状态(下载量、收益、日志信息等)，一般情况下，在产品官网发布的版本渠道号一般为空。    

其它应用市场的渠道号可以自己编辑设置，示例如下:  
百度应用市场的渠道号可以为 Baidu，豌豆荚的为 Wandoujia。

