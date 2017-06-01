# 热修复---AndFix

标签（空格分隔）： 热修复

---

#### AndFix介绍
>AndFix是一个Android App的在线热补丁框架。使用此框架，我们能够在不重复发版的情况下，在线修改App中的Bug。AndFix就是 “Android Hot-Fix”的缩写。 
就目前来说，AndFix支持Android 2.3到6.0版本，并且支持arm 与 X86系统架构的设备。完美支持Dalvik与ART的Runtime。 
AndFix 的补丁文件是以 .apatch 结尾的文件。 
AndFix是阿里巴巴开源项目。

#### AndFix使用
1. 在gradle中配置andfix。
```
dependencies {
    compile 'com.alipay.euler:andfix:0.3.1@aar'
}
```
2. 自定义Applaction。
```
public class BaseApplication extends Application {
  private PatchManager patchManager;
  private String appversion;

  /**
   * apatch文件
   */
  private static final String APATCH_PATH = "/fix.apatch";

  @Override
  public void onCreate() {
    super.onCreate();
    try {
      appversion = getPackageManager().getPackageInfo(getPackageName(), 0).versionName;
    } catch (PackageManager.NameNotFoundException e) {
      e.printStackTrace();
    }
    patchManager = new PatchManager(this);
    patchManager.init(appversion);//current version
    //加载已经添加到PatchManager中的patch
    patchManager.loadPatch();

    //apatch文件的目录
    String patchFileString = Environment.getExternalStorageDirectory().getAbsolutePath() + APATCH_PATH;
    File apatchPath = new File(patchFileString);

    if (apatchPath.exists()) {
      Toast.makeText(this, "补丁文件存在", Toast.LENGTH_SHORT).show();
      try {
        //添加apatch文件
        patchManager.addPatch(patchFileString);
      } catch (IOException e) {
        Toast.makeText(this, "打补丁出错了", Toast.LENGTH_SHORT).show();
        e.printStackTrace();
      }
    } else {
      Toast.makeText(this, "补丁不文件存在", Toast.LENGTH_SHORT).show();
    }
  }
}
```
3. 如果有新的补丁需要修复，下载完成后，进行以下操作
```
//添加patch，只需指定patch的路径即可，补丁会立即生效
mPatchManager.addPatch(path);
```
4. 当apk版本升级，需要把之前patch文件的删除，需要以下操作
```
//删除所有已加载的patch文件
mPatchManager.removeAllPatch();
```
#### patch文件的生成
1. 工具：[apkpatch-1.0.3][1]
下载完成后解压。有Bug的APK和修复后的APK拷贝到解压目录下，将签名所用的.jks文件也拷贝到该目录下。如下图：    
![解压后的文件夹][2]
2. 使用工具：apkpatch-1.0.3
命令 : 
apkpatch.bat -f new.apk -t old.apk -o output1 -k ***.jks -p * -a * -e *
```
-f <new.apk> ：新版本
-t <old.apk> : 旧版本
-o <output> ： 输出目录
-k <keystore>： 打包所用的keystore
-p <password>： keystore的密码
-a <alias>： keystore 用户别名
-e <alias password>： keystore 用户别名密码
```
如果文件生成正确会出现如下信息：    
![patch文件生成成功][3]

#### 加载patch文件
测试时可以将生成的patch文件拷贝到sd卡上，如果是线上包可以使用推送将patch下载到sd卡上。、

#### 遇到的问题
1. 最开始学习时常常出错的是生成patch文件的命令，对照命令参数解释确定命令正确。
2. 当patch正确的下载到指定的目录并且根据log也发现加载了patch文件，却发现还是没有修复后的结果可以检查一下是否有SD卡读写权限。
```
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"></uses-permission>
```

----------

[AndFix源码地址][4]
有关AndFix的源码分析可参考：[Alibaba-AndFix Bug热修复框架原理及源码解析][5]


  [1]: https://github.com/alibaba/AndFix/raw/master/tools/apkpatch-1.0.3.zip
  [2]: https://github.com/SunnyTime/ProgrammerData/blob/master/hotfix1.png
  [3]: https://github.com/SunnyTime/ProgrammerData/blob/master/patchsuccess.png
  [4]: https://github.com/alibaba/AndFix
  [5]: http://blog.csdn.net/qxs965266509/article/details/49816007.com/SunnyTime/ProgrammerData/blob/master/patchsuccess.png