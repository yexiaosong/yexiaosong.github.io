---
title: React-Native 真的是移动开发的未来吗?
date: 2018-06-21 00:34:28
tags: FE
---

公司本年度有App任务，陆陆续续用RN开发了两个应用。一款是涉及仪器控制的平板项目，另一款是客户端的App。下文谈谈使用RN开发的部分认知(其实只是随便扯一扯，没有什么深度)

## 我们适合使用RN吗？
其实能问出这个问题的我猜只有小公司了(比如我们)，因为大公司早已入坑，甚至已有公司出坑了(Airbnb)。
### RN的优势
+ 满足需求变更的快速发版。原生代码审核时间长，Hybird运行速度又不够快。
+ 很多小公司想不招移动开发而开发App，节约成本。
+ 跨平台，一份代码，稍微改动部分就可以运行在两个平台上。
+ 业务类代码开发很快。

### RN的弊端
+ 坑多，这个坑在方方面面。RN在0.5x版本上，安卓系统上的border渲染就有锯齿，后来我们不分border效果是使用图片实现的，汗啊~

+ 第三方组件不全。RN作为一个有影响力开源项目，有很多第三方组件，但是可以毫不客气的说，都不完美~ 无论是相机调用、音频播放还是消息处理，甚至路由组件。很多组件需要修改源码。因为这个组件依赖于RN0.4x，而另一个依赖于RN0.5x，这就特别尴尬。所以还是自己原生开发工程师提供接口，通过JS bridge调用比较好。

+ 对前端来说，开发完整的App还是需要懂原生开发知识，对一般程序员来说Android开发用的Java还好，但是ios的开发语言OC简直是魔咒。当然我们可以面向百度和Google编程，不过这会浪费大量的时间且做出来并不完美。所以，公司想做App的话还是至少需要一个懂Android开发和懂IOS开发的工程师。

+ 屏幕适配，虽然RN的尺寸是相对尺寸，且提供了获取屏幕密度等方法，但是具体到设备上，还是各有不同。不过相对轻松的是，我开发的平板项目只有一个尺寸，所以甚至可以使用定位来处理布局。

## 开发中的经验
### 配置完RN环境，以安卓为例
在使用react-native run-android之后，本质是将Java等初始化代码打成了一个包，后续开发和动态更新都是通过读取在通过命令启动的8081端口服务下的js bundle实现的，所以初次出现如下提示
```bash
Unable to load script from assets 'index.android.bundle' ...
```
只需要摇一摇真机，在出现的dialog中设置Dev setting中的host、port再reload后就正常了

### 因Android版本问题导致的无法安装
我是首次是通过Android 6.0开发，后来打算使用Android 5.1进行测试。错误信息如下
```bash
com.android.builder.testing.api.DeviceException: com.android.ddmlib.InstallException: Failed to install all
```
出现这种情况，网上很多说是通过修改gradle来进行修改，但是通常开发android程序时，需要测试不同的设备。可以直接使用adb命令进行安装
```bash
adb install android/app/build/outputs/apk/app-debug.apk
```

### 缓存问题
特别是在使用了babel-resolver之后---，错误信息如下
```bash
unable to resolve module ***
```
解决方式：
react-native start --reset-cache

### JSX注释语法
在使用`command + /`添加注释后，经常出现错误：
```bash
Cannot add a child node that doesn't have a YogaNode to a parent without measure function
```
解决方式当然是检查自己的JSX代码是否注释写的不对。

### 摇一摇问题的解决(Android)
在我们使用了`Mobx Redux`等状态管理时，热更新不会更新这些代码，而频繁摇一摇实在是太累了。此时可以使用
```bash
adb shell input keyevent 82
```
此时相当于虚拟了一个摇一摇事件。

### 远程调试(root设备)
当我们的开发环境可能有多种设备且不方便插USB得话，可以进行Adb远程调试。方法如下：
```bash
1. 安卓端：首先安装安卓终端模拟器
su
setprop service.adb.tcp.port 5555
stop adbd
start adbd
2. 开发端：
adb connect ANDROID_HOST
adb install ***.apk
需要唤出设置页面的话，用上面的摇一摇问题的解决方案
```

### JS调用Android代码
以通过js获取原生Android序列号为例，此处代码会比官方文档全。其他的可以参考[官方文档](http://facebook.github.io/react-native/docs/native-modules-android)
+ 在与android的MainActivity同级目录下新建一个SerialNumberModule.java文件，内容如下：
```java
public class SerialNumberModule extends ReactContextBaseJavaModule{

    @Override
    public String getName() {
        return "SerialNumber";
    }

    public SerialNumberModule(ReactApplicationContext reactContext) {
        super(reactContext);
    }

    @ReactMethod
    public void getSerialNumber (Callback successCallback, Callback errorCallback) {
        String SerialNumber = android.os.Build.SERIAL;
        try {
            successCallback.invoke(SerialNumber);
        } catch (IllegalViewOperationException e) {
            errorCallback.invoke(e.getMessage());
        }
    }
}
```
+ 在新建一个SerialNumberPackage.java文件，用来添加模块，代码如下：
```java
public class SerialNumberPackage implements ReactPackage {
    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }

    @Override
    public List<NativeModule> createNativeModules(
            ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();

        modules.add(new SerialNumberModule(reactContext));

        return modules;
    }
}

```
+ 在本目录的MainApplication.java中重写的getPackages中new此模块，代码：
```java
@Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage(),
                new SerialNumberPackage()
      );
    }
```
+ js端调用，以rn的入口App.js为例
```javascript
import { NativeModules } from 'react-native'
*******
在组件内：
componentWillMount () {
    NativeModules.SerialNumber.getSerialNumber(success => {
      console.log('success', success)
    }, err => {
      console.log('err', err)
    })
}
  
```

### 发行IOS包
可以在项目根目录下执行
```bash
1. react-native bundle --entry-file index.js --bundle-output ./ios/bundle/index.ios.jsbundle --platform ios --assets-dest ./ios/bundle --dev false
2. 修改AppDelegate.m文件，将
jsCodeLocation = [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index.ios" fallbackResource:nil];
注释掉，并添加
jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"index.ios" withExtension:@"jsbundle"];
3. 将打好的文件在ios目录下的bundle目录里的两个文件和一个文件夹拖拽到Xcode的项目相应名字的目录下
4. 插上真机，在Xcode的目标上选择真机，点击Xcode标题栏的product --> archive即可
```

> 先上结论，个人认为这种开发模式迟早会结束，第三方强行兼容设备总是不行的，个人还是信WEB, 信W3C。踩坑还在进行，上述只是一点开发经验。大神轻喷，同行人欢迎一起讨论。