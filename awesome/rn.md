RN 行为指导
===

- 处理键盘模式（保证 windowSoftInputMode 为 adjestResize）

- RN 版本 0.48

- 路由使用 React Navigation

- 库存在的组件用库的，不用原生（Text、TextInput、Dialog、待补充）

- 特殊情况，比如在 Touchable 下必须是原生组件，此时用 原生组件，且补 S.text 。的 <Text style={[S.text]} />

- 图片使用 [IFont](http://iconfont.cn/manage/index?spm=a313x.7781069.1998910419.10&manage_type=myprojects&projectId=87794&keyword=)

- 在首页应该响应按两次返回退出应用

- 在 dev menu里面 Dev Setting 有关闭Dev Mode选项和JS Minify选项


RN 升级 48 一些指南

- android/app/src/main/java/com/reactnativegm/MainApplication.java 里 修改 `protected boolean getUseDeveloperSupport() {` 为 `public boolean getUseDeveloperSupport() {`

#### IOS打包FYI
- IOS打包App现在要development和distribution的Provisioning Profiles都有才可以code signing.
-  升级了xcode9之后 虽然会报一堆issues,但是 竟然可以正常Archive

***

#### RN布局调试 
 可以开启显示布局边界，方便调试

![](http://image.document.guanmai.cn/19-1-24/3179470.jpg)

# RN总结

## Table of Contents

- [RN-坑](#rn-坑)
  - [android](#android)
  - [ios](#ios)
- [RN-操作](#rn-操作)
  - [RN调试](#rn调试)
  - [集成微信支付](#集成微信支付)
  - [ios设置UserAgent](#ios设置useragent)
  - [蒲公英](#蒲公英)
- [打包](#打包)
  - [android](#android-1)
  - [ios](#ios-1)

## RN-坑

- ❗️ **build报错`":CFBundleIdentifier", Does Not Exist`**

    一般出现在0.45以上的版本,react-native在下载和编译第三方库的时候出错，有可能是下载包的时候断开。

    寻找第三方平台(http://pan.baidu.com/s/1kVDUAZ9)下载下列文件 移动到`~/.rncache`目录下

    ```sh
    fetch_and_unpack glog-0.3.4.tar.gz https://github.com/google/glog/archive/v0.3.4.tar.gz "CC='$SCRIPTDIR'/ios-cc.sh CXX='$SCRIPTDIR'/ios-cc.sh ./configure --host arm-apple-darwin"
    fetch_and_unpack double-conversion-1.1.5.tar.gz https://github.com/google/double-conversion/archive/v1.1.5.tar.gz
    fetch_and_unpack boost_1_63_0.tar.gz https://github.com/react-native-community/boost-for-react-native/releases/download/v1.63.0-0/boost_1_63_0.tar.gz
    fetch_and_unpack folly-2016.09.26.00.tar.gz https://github.com/facebook/folly/archive/v2016.09.26.00.tar.gz
    ```

### android

- ⌨️ **键盘处理:解决键盘弹起时遮挡内容的问题**
  - 在`./android/src/main/AndroidManifest.xml`里添加/修改`android:windowSoftInputMode`为`adjustResize`:
      ```xml
      android:windowSoftInputMode="stateHidden|adjustResize"
      ```
      可以代替使用js的办法去解决遮罩问题. android会根据软键盘弹出的高度来调整activity的高度

      > “adjustResize”: 始终调整 Activity 主窗口的尺寸来为屏幕上的软键盘腾出空间。
      > “adjustPan”: 不调整 Activity 主窗口的尺寸来为软键盘腾出空间， 而是自动平移窗口的内容，使当前焦点永远不被键盘遮盖，让用户始终都能看到其输入的内容。 这通常不如尺寸调正可取，因为用户可能需要关闭软键盘以到达被遮盖的窗口部分或与这些部分进行交互。

  - 使用`KeyboardAvoidingView`组件
  - 通过`Keyboard`监听Keyboard的高度变化,改变`View`的高度

### ios

- **❗️ ios报错 cmd+r不能刷新**
  - 按 cmd + shift + k， 等同于点击 Hardware > Keyboard > Connect Hardware Keyboard。
  - 关闭simulator, 重新run project

## RN-操作

### RN调试

- 在 dev menu里面 Dev Setting 有**关闭Dev Mode**选项和**JS Minify**选项
- android中开启**显示布局边界**，方便调试

### 集成微信支付

**支付流程流程:**
**App用户点击支付 => 支付相关信息给后台 => 后台根据信息和商户号向微信发起支付请求 => 微信返回支付信息给后台 => 后台返回支付信息给App => App通过SDK调起微信 => 根据后台返回的信息拉起支付 => 支付成功,跳转App**

使用 `react-native-wechat`第三方库

- [微信开放平台](https://open.weixin.qq.com/)上注册应用并获得`AppID`;
- 登录[商户平台](https://pay.weixin.qq.com/index.php/core/home/login?return_url=%2F)进行验证,开户,获取`商户号`,`商户号`是给后台用的
- 根据[文档](https://github.com/yorkie/react-native-wechat/blob/master/docs/build-setup-android.md)进行配置;
- 配置的时候注意在包名相应目录下新建一个wxapi目录，并在该wxapi目录下新增一个`WXEntryActivity.java`和`WXPayEntryActivity.java`,里面配置文件名对应的类.
- 需要在全局的入口处初始化WeChat模块
  ```js
  import * as WeChat from 'react-native-wechat';
  WeChat.registerApp('appid');
  ```
- JavaScript中的调用参考文档[demo](https://github.com/yorkie/react-native-wechat#api-documentation)

### ios设置UserAgent

  在APPDelegate.m中添加:

  ```Objective-C
    NSString *deviceType = [UIDevice currentDevice].model;
    UIWebView *webView = [[UIWebView alloc] initWithFrame:CGRectZero];
    NSString *oldAgent = [webView stringByEvaluatingJavaScriptFromString:@"navigator.userAgent"];
    NSString *newAgent = [oldAgent stringByAppendingString:@" GMRN - iOS - "];
    newAgent = [newAgent stringByAppendingString:deviceType];
    NSDictionary *dictionnary = [[NSDictionary alloc] initWithObjectsAndKeys:newAgent, @"UserAgent", nil];
    [[NSUserDefaults standardUserDefaults] registerDefaults:dictionnary];
  ```

- userAgent 为默认 UserAgent。
- newUserAgent 拼接了`GMRN - iOS -` ，标识当前是在rn的iOS环境中

### 蒲公英

- **集成蒲公英sdk**:

    [文档](https://www.pgyer.com/doc/view/sdk_android_guide)

    **新版的蒲公英已经支持配置是否强制升级**:
    ```java
    import com.pgyersdk.update.PgyUpdateManager;
    PgyUpdateManager.setIsForced(true); //设置是否强制更新。true为强制更新；false为不强制更新（默认值）。
    PgyUpdateManager.register(this);
    ```

    **旧版的蒲公英设置强制升级**:
    在安卓的MainActivity.java中:
    ```java
    PgyUpdateManager.register(MainActivity.this, "com.guanmai.gmrnpa.fileprovider",  new UpdateManagerListener() {
      @Override
      public void onUpdateAvailable(final String result) {
        // 将新版本信息封装到AppBean中
        final AppBean appBean = getAppBeanFromString(result);
        new AlertDialog.Builder(MainActivity.this)
          .setTitle("更新")
          .setMessage("")
          .setCancelable(false)
          .setNegativeButton(
            "确定",
            new DialogInterface.OnClickListener() {
              @Override
              public void onClick(DialogInterface dialog,int which) {
                  startDownloadTask(
                    MainActivity.this,
                    appBean.getDownloadURL());
              }
              }
          ).show();
          }
          @Override
          public void onNoUpdateAvailable() {
            
          }
      }
    );
    ```

- **蒲公英升级策略**
  - 两个概念`App 本身的版本号`、`蒲公英的自增 Build 版本号`:

    - `App 本身的版本号`: 

      安卓下有 VersionName 和 VersionCode ,`VersionName`是我们通常说的版本号, 是应用向用户宣传时候用到的标识，例如：`1.1`、`8.2.1`等;`VersionCode`是编译版本号,编译一次会增加一次这个版本号,(iOS来说是字符串类型)Android是一个整数类型.

    - `蒲公英的自增 Build 版本号`:

      蒲公英给每个应用的不同版本，设置了一个递增的 Build 版本号（仅用于在蒲公英上显示，不修改应用文件本身）。在默认情况下，会直接显示这个递增的版本号，不显示应用本身设置的 Build 版本号。若想设置为显示自身的版本号也可以在`应用设置`里更改.

  - 蒲公英更新策略:蒲公英 SDK 的更新，是根据`App 的版本号`和`蒲公英自增 Build 版本号`来判定更新的。这两个版本号中，任何一个检查到线上版本大于本地版本，都会提示更新。
    具体步骤:
      1. 先比较`App 的版本号`,如果小于服务器上上的,就更新;
      2. 第二步比较蒲公英的自增版本号,若小于服务器上的版本,就更新.

    在这两步中，任何一步如果判断出有新版本，都会提示更新。否则不会提示。

## 打包

### android

- 我们所有的安卓应用使用同一个签名文件,签名需要找个安全的地方存一份备份
- 把`*.keystore`文件放到工程中的`android/app`文件夹下。
- `android/app/build.gradle`中配置
- 打包命令:`cd android && ./gradlew assembleRelease`

### ios

- 打开Xcode, `General`>`Identity` 里面有几个字段 分别表示:
  - `Display Name`:应用显示的名称
  - `Bundle Identifier`:应用的bundleID, 对应android中的applicationId
  - `Version`:展示的版本号,对应android的`versionName`
  - `Build`:应用构建的Build版本号,上传打包应用至应用商店时,build版本号要比现有的版本号大,才能覆盖

- iOS 获取签名
  - [登录Apple开发者平台](http://developer.apple.com/account)
  - 签名过程[参考](https://www.jianshu.com/p/a19d2d0747ee)
  - 签名 https://www.jianshu.com/p/71acb31eadd2

- ios上架流程:
  - duplicate target获得一个副本
  - 修改target name
  - 修改 target的相应bundle ID和DisplayName
  - 选择对应证书的account做signing
  - 在ImageSet里修改AppIcon
  - 在edit scheme里修改新副本的名称
  - device 选为 Generic iOS Device
  - 在Product里选择Archive 等待一段时间 获得*.ipa文件
  - 根据弹窗的提示可选择上传到appstore.可以在`window`=>`organizer`重新打开弹窗
  - 需要[登录Apple开发者平台](http://developer.apple.com/account),在iTunes connect中新建相对应的App信息,才能上传成功

- icon
 - https://help.apple.com/itunes-connect/developer/#/devd274dd925
 - https://icon.wuruihong.com/
