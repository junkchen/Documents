# Android按下返回键应用退向后台运行 #

我们日常使用的很多Android应用（如QQ、微信、微博），在应用的主界面按下返回键，应用并没有退出，而是进入后台运行。  

那么，开发中是如何实现的呢？我找到了两种方法：  

## 一、监测返回键 ##
1、在Activity中重写onBackPressed()方法。  

	@Override
	public void onBackPressed() {
		//此处写退向后台的处理
	}
  
2、重写onKeyDown()方法（有的应用提示再次点击返回键退出应用就是在这里做的文章）。

	@Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK) {//如果返回键按下
			//此处写退向后台的处理
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }
  

## 二、退向后台运行 ##

1、只需一句话搞定，调用moveTaskToBack()方法，这个方法需要设置一个boolean参数，ture 在任何Activity中按下返回键都退出并进入后台运行， false 只有在根Activity中按下返回键才会退向后台运行。  

	moveTaskToBack(false);  

2、使用Intent，返回手机主界面。  

	Intent intent = new Intent(Intent.ACTION_MAIN);
    intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    intent.addCategory(Intent.CATEGORY_HOME);
    startActivity(intent);  

----------

## 最后来个详细点儿的 ##

	@Override
    public void onBackPressed() {
        //方式一：将此任务转向后台
        moveTaskToBack(false);
        
        //方式二：返回手机的主屏幕
        /*Intent intent = new Intent(Intent.ACTION_MAIN);
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        intent.addCategory(Intent.CATEGORY_HOME);
        startActivity(intent);*/
    }  

欢迎加QQ群交流：**365532949**