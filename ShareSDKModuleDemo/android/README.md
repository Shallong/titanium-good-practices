这是一个快速利用ShareSDK制作android平台的Titanium Module作为第三方分享的教程
====================================================================

### 前提条件
- 开发过titanium module，对titanium 及 module 有一定了解。参考官方文档[iOS_Module_Development](http://docs.appcelerator.com/titanium/3.0/#!/guide/iOS_Module_Development_Guide)。
- 对iphone开发具备基础的了解。
- 有一台ios设备，如itouch,iphone。若您没有设备，也可以使用模拟器。(但是模拟器不能够真正切到微信app去分享)
- 对alloy有一定了解。

接下来所有的内容都默认你具备了基础知识。

### 环境
- 系统:OSX
- export PATH=${PATH}:~/android-sdk/platform-tools:~/android-sdk/tools:~/apache-ant-1.9.2/bin
- export ANT_HOME=/Users/apple/apache-ant-1.9.2
- export ANDROID_SDK=/Users/apple/android-sdk
- export ANDROID_NDK=/Users/apple/android-ndk-r8d
- ti version 3.4.1
- ti sdk version 3.4.1
- alloy version 1.5.1
- ShareSdk version: 2.5.7 android

### 下载[ShareSDK](http://sharesdk.mob.com/Download)
选择的是自定义下载，不包括所有的平台。

### 仔细阅读[官网](http://wiki.mob.com/%E5%BF%AB%E9%80%9F%E9%9B%86%E6%88%90%E6%8C%87%E5%8D%97/)

### 根据[官网](http://wiki.mob.com/%E5%BF%AB%E9%80%9F%E9%9B%86%E6%88%90%E6%8C%87%E5%8D%97/)使用"QuickIntegrater.jar"

### 创建module
创建android目录，目录结构为
```
ShareSDKModuleDemo
  |--ShareSDKTestApp
  |--ios
  \--android
```
终端输入：
```
ti create --platforms=android --type=module --name=TestShareSDK --id=com.test.testsharesdk --url=www.test.testsharesdk --workspace-dir=android
```

### 打开Eclipse。export "Existing Projects into Workspace"把刚创建android项目导入进来。这步可以不做，完全可以不需要Eclipse，为了看的更清楚。

### 配置[build.properties](https://github.com/mdsb100/titanium-good-practices/blob/master/ShareSDKModuleDemo/android/TestShareSDK/android/build.properties)
```
titanium.platform=${env.HOME}/Library/Application Support/Titanium/mobilesdk/osx/3.5.0.GA/android
android.platform=${env.ANDROID_SDK}/platforms/android-10
google.apis=${env.ANDROID_SDK}/add-ons/addon-google_apis-google-10
android.ndk=${env.ANDROID_NDK}
```
windows如果不行就写绝对路径。titanium.platform根据你自己的平台写。

### 加入一行```<property environment="env"/>```到[build.xml](https://github.com/mdsb100/titanium-good-practices/blob/master/ShareSDKModuleDemo/android/TestShareSDK/android/build.xml)

### Terminal中输入```and dist```。看看是否build成功。这里要装ant。

### 修改[timodule.xml](https://github.com/mdsb100/titanium-good-practices/blob/master/ShareSDKModuleDemo/android/TestShareSDK/android/timodule.xml)。增加权限，参考[官网](http://wiki.mob.com/%E5%BF%AB%E9%80%9F%E9%9B%86%E6%88%90%E6%8C%87%E5%8D%97/)。如果您集成了微信或者易信，还需要添加下面两个Activity。
这里没用易信所以注释掉了。
注意！android:name=".wxapi.WXEntryActivity"。 必须是".wxapi.WXEntryActivity"。之后创建的目录结构也必须对应起来

### 复制ShareSDK jar包到lib目录下
![android_copy_jar](https://cloud.githubusercontent.com/assets/2350193/5615509/e3a7ec6c-9536-11e4-9acd-ea852c08e917.png)

### 添加ShareSDK jar包。把下载的ShareSDK添加到项目中，这个时候Eclipse就派上用场了。最后结果[.classpath](https://github.com/mdsb100/titanium-good-practices/blob/master/ShareSDKModuleDemo/android/TestShareSDK/android/.classpath)
![android_add_jar](https://cloud.githubusercontent.com/assets/2350193/5615582/6feebbf6-9537-11e4-8850-261305e5e3dd.png)

### 添加res到platform/android下
![android_copy_res](https://cloud.githubusercontent.com/assets/2350193/5617399/5d64dbde-9549-11e4-9bb8-6bc026204e62.png)

### 复制src
![android_copy_src](https://cloud.githubusercontent.com/assets/2350193/5615737/f6b2924c-9538-11e4-961c-53cb76dba1d3.png)

### 增加[ShareSDK.xml](https://github.com/mdsb100/titanium-good-practices/tree/master/ShareSDKModuleDemo/android/TestShareSDK/assets/ShareSDK.xml)和增加[shareicon.jpg](https://github.com/mdsb100/titanium-good-practices/tree/master/ShareSDKModuleDemo/android/TestShareSDK/assets/shareicon.jpg)到assets目录下
ShareSDK.xml是配置appkey的。
shareicon.jpg是分享的默认图片。

### 修改[TestShareSDKModule.java](https://github.com/mdsb100/titanium-good-practices/blob/master/ShareSDKModuleDemo/android/TestShareSDK/android/src/com/test/testsharesdk/TestShareSDKModule.java)
增加share这个方法。
因为有人问如何初始化SDK，所以在教程里特别着重指出这个方法
```
@Kroll.onAppCreate
public static void onAppCreate(TiApplication app)
{
  Log.d(LCAT, "inside onAppCreate");
  ShareSDK.initSDK(app);

  SHAREICON = getApplicationResource(SHAREICONNAME);
  SHAREICONPATH = copyFileIntoSDCard(app, SHAREICONNAME+".jpg");
  // put module init code that needs to run when the application is created
}
```

### 预备发布。在android文件夹下增加文件[build.sh](https://github.com/mdsb100/titanium-good-practices/blob/master/ShareSDKModuleDemo/android/TestShareSDK/android/build.sh)
目的是把module解压到ShareSDKTestApp下。

### 执行 `./build.sh`

### 修改ShareSDKTestApp文件夹下的[tiapp.xml](https://github.com/mdsb100/titanium-good-practices/blob/master/ShareSDKModuleDemo/ShareSDKTestApp/tiapp.xml)
增加```<module platform="android" version="1.0.0">com.baidao.testsharesdk</module>```，让app能使用android下的sharesdk module。
增加```<application android:debuggable="true" ></application>```以调试。

### 删除"ShareSDKTestApp/platform/android/res/drawable-xxxhdpi"
如果有这个文件夹就跑不起来。因为没有文件夹"android/TestShareSDK/android/platform/android/res/drawable-xxxhdpi"

### 跳转回主[README.md](https://github.com/mdsb100/titanium-good-practices/tree/master/ShareSDKModuleDemo)
