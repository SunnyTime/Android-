# WebView之WebChromeClient

标签（空格分隔）： WebView

-------
WebView的开发过程中你需要使用WebChromeClient来辅助WebView处理 JavaScript 的对话框、网站图标、网站title、加载进度等。

### WebChromeClient常用的API

 1. onProgressChanged(WebView view, int newProgress)
    通知应用网页加载进度。
 2. onReceivedTitle(WebView view, String title)
    得到网页标题。
 3. onShowCustomView(View view, CustomViewCallback callback)
    网页中有H5播放flash video的时候按下全屏按钮将会调用到这个方法，一般用作设置网页播放全屏操作。
 4. onHideCustomView()、
    取消全屏方法
 5. mWebView.setDownloadListener(new DownloadListener() {
    @Override
    public void onDownloadStart(String url, String userAgent, String contentDisposition, String mimetype, long contentLength) {
    }
});
WebView下载监听。

### WebChromeClient高级API
 1. webview支持File Input 标签
Android 5.0 API 21后借助新的onShowFileChooser()方法,您现在不但可以在WebView中使用输入表单字段,而且可以启动文件选择器从 Android 设备中选择图片和文件。
WebView上传本地文件、视频播放全屏可以查看[GoogleChrome高级使用实例][1]
在5.0之前的版本上次文件方法有所不同：
```
// For Android 3.0
public void openFileChooser(ValueCallback<Uri> uploadMsg) {
    
}
// For Android 3.0+
public void openFileChooser(ValueCallback uploadMsg, String acceptType) {
    
}

// For Android 4.1
public void openFileChooser(ValueCallback<Uri> uploadMsg, String acceptType, String capture) {

}
```
但是在4.4.2上（亲测）是不可以使用的因为在android 4.4.0 4.4.1 4.4.2版本中，取消了openFileChooser这个方法。
解决方法：
1.） android提供方法给js调用选择图片上传。
2.）用com.tencent.smtt.sdk.WebView代替原生的WebView。
对于com.tencent.smtt.sdk.WebView也会有很多问题 http://x5.tencent.com/doc?id=1003

http://www.jianshu.com/p/e75fa22c7f06?utm_campaign=haruki&utm_content=note&utm_medium=reader_share&utm_source=qq

http://bbs.mb.qq.com/forum-110-4.html
2. onJsPrompt
在Android API level 16以及之前的版本存在远程代码执行安全漏洞，16以上的版本通过添加@JavascriptInterface注解解决漏洞，16一下的版本却没有解决，如果app的要兼容16一下的可采用可以利用prompt方法传参以完成java与js的交互。对应java中的onJsPrompt方法的声明如下：
```
public boolean onJsPrompt( WebView view, String url, String message, String defaultValue, JsPromptResult result )
    {
        if( message.equals("login")){
        //去登录  
        }
        //返回结果
        return true;
    }
```
有关解决旧版本WebView漏洞的方法可参考[JS与WebView交互存在的一些问题][2]


  [1]: https://github.com/GoogleChrome/chromium-webview-samples
  [2]: http://www.jianshu.com/p/93cea79a2443