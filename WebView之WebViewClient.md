# WebView之WebViewClient

---
Android 混合应用（hybride）开发中会用到WebView。辅助WebView有两个大类，WebViewClient和WebChromeClient。

### WebViewClient常用API
onPageStarted(WebView view, String url, Bitmap favicon)
在WebView开始加载网页之前调用。

public void onPageFinished(WebView view, String url)
加载结束的时候调用。

boolean shouldOverrideUrlLoading(WebView view, String url)对网页中超链接按钮的响应。当按下某个超链接时WebViewClient会调用这个方法，并传递参数。

onReceivedError(WebView view, int errorCode,String description, String failingUrl) 页面加载错误时调用该方法。

onReceivedSslError(WebView view, SslErrorHandler handler,SslError error)使用HTTPS通信的网址页面加载错误调用该方法。SslErrorHandler.proceed()表示忽略错误继续加载，SslErrorHandler.cancel()表示取消加载。在onReceivedSslError的默认实现中是使用的SslErrorHandler.cancel()来取消加载。

WebResourceResponse shouldInterceptRequest(WebView view,  
        String url)每一次请求资源时，都会通过这个函数来回调，比如超链接、JS文件、CSS文件、图片等，也就是说浏览器中每一次请求资源时，都会回调回来，无论任何资源！但是必须注意的是shouldInterceptRequest函数是在非UI线程中执行的，在其中不能直接做UI操作，如果需要做UI操作，则需要利用Handler来实现。
``` java
/** 
 * 在加载页面资源时会调用，每一个资源（比如图片）的加载都会调用一次 
 */  
public void onLoadResource(WebView view, String url)   
/** 
 *  (WebView发生改变时调用)  
 *  可以参考http://www.it1352.com/191180.html的用法 
 */  
 public void onScaleChanged(WebView view, float oldScale, float newScale)  
/** 
 * 重写此方法才能够处理在浏览器中的按键事件。 
 * 是否让主程序同步处理Key Event事件，如过滤菜单快捷键的Key Event事件。 
 * 如果返回true，WebView不会处理Key Event， 
 * 如果返回false，Key Event总是由WebView处理。默认：false 
 */  
public boolean shouldOverrideKeyEvent(WebView view, KeyEvent event)  
/** 
 * 是否重发POST请求数据，默认不重发。 
 */  
onFormResubmission(WebView view, Message dontResend, Message resend)  
/** 
 * 更新访问历史 
 */  
doUpdateVisitedHistory(WebView view, String url, boolean isReload)  
 /** 
 * 通知主程序输入事件不是由WebView调用。是否让主程序处理WebView未处理的Input Event。 
 * 除了系统按键，WebView总是消耗掉输入事件或shouldOverrideKeyEvent返回true。 
 * 该方法由event 分发异步调用。注意：如果事件为MotionEvent，则事件的生命周期只存在方法调用过程中， 
 * 如果WebViewClient想要使用这个Event，则需要复制Event对象。 
 */  
onUnhandledInputEvent(WebView view, InputEvent event)  
 /** 
 * 通知主程序执行了自动登录请求。 
 */  
onReceivedLoginRequest(WebView view, String realm, String account, String args)  
/** 
 * 通知主程序：WebView接收HTTP认证请求，主程序可以使用HttpAuthHandler为请求设置WebView响应。默认取消请求。 
 */  
onReceivedHttpAuthRequest(WebView view, HttpAuthHandler handler, String host, String realm)  
/** 
 * 通知主程序处理SSL客户端认证请求。如果需要提供密钥，主程序负责显示UI界面。 
 * 有三个响应方法：proceed(), cancel() 和 ignore()。 
 * 如果调用proceed()和cancel()，webview将会记住response， 
 * 对相同的host和port地址不再调用onReceivedClientCertRequest方法。 
 * 如果调用ignore()方法，webview则不会记住response。该方法在UI线程中执行， 
 * 在回调期间，连接被挂起。默认cancel()，即无客户端认证 
 */  
onReceivedClientCertRequest(WebView view, ClientCertRequest request)
``` 
### Back事件处理
如果用webview点链接看了很多页以后，如果不做任何处理，点击系统“Back”键，整个浏览器会调用finish()而结束自身，如果希望浏览的网页回退而不是退出浏览器，需要在当前Activity中处理并消费掉该Back事件。 覆盖Activity类的onKeyDown(int keyCoder,KeyEvent event)方法。
```
public class MyActivity extends Activity {  
    private WebView mWebView;  
    private ProgressDialog mProgressDialog;  
    @Override  
    public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.main);  
        mWebView = (WebView)findViewById(R.id.webview);  
        mProgressDialog = new ProgressDialog(this);  
        mWebView.getSettings().setJavaScriptEnabled(true);  
        mWebView.setWebViewClient(new WebViewClient());  
        mWebView.loadUrl("http://baidu.com");  
    }  
  
    @Override  
    public boolean onKeyDown(int keyCode, KeyEvent event) {  
        //改写物理返回键的逻辑  
        if(keyCode==KeyEvent.KEYCODE_BACK) {  
            if(mWebView.canGoBack()) {  
                mWebView.goBack();//返回上一页面   
                return true;  
            } else {  
                System.exit(0);//退出程序   
            }  
        }  
        return super.onKeyDown(keyCode, event);  
    }  
}  
```
在未重写onKeyDown前的效果图：点击回退按钮，整个Activity就销毁了


---







