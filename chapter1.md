<!-- toc -->

# Android Web Apps 使用 Android WebView

```
作者：Jakob Jenkov
来源：http://tutorials.jenkov.com/android/android-web-apps-using-android-webview.html
最后更新: 2014-12-19
翻译：reaper
翻译日期：2016-07-15
```

安卓web app是利用安卓 WebView组件渲染来左外安卓app 用户图形界面（GUI）的一部分。WebView 组件是View的子类，同时也是成熟浏览器的实现，你可以在你的安卓app 用户图形界面（GUI）的任意位置中使用它。通常WebView都占有屏幕的大部分，当然你可以可以让它仅占用屏幕一半，甚至可以某个小块中使用它。

## 完全 web app 还是混合 app（Hybrid App）?

因为app确实混合了原生app和web app，就称一个安卓app用WebView展现app内容叫web app 听起来有些不好理解。其实在WebView 内部是用安卓组件和app的部分是使用web 技术(HTML, CSS, JavaScript, SVG)来展现的。

web app的另外一个常见的术语混合app（Hybrid App）。意思是说app使用了几种app的展现方式。典型的是使用了原生app和web app。无论如何混合app术语中没有清晰的说明是怎么那些app混合的。可能混合内容：原生app,p2p app,客户端服务器app (Client / Server app)。你必须知道这个术语通常指原生app和web app混合。因此我会使用安卓web app，因为至少这说明这个app使用了web技术。

## WebView 是基于 Chrome的

从安卓4.4（Kitkat）版本开始WebView组建就是基于与Chrome代码一样的内容提供给安卓使用。这确保了对于你的原生web app（混合模式）或者通过安卓Chrome浏览器看到的大多数效果是一致的。

在安卓4.4版本前，WebVeiw是依据安卓内部浏览器的，但是之后Chrome特带了老的安卓浏览器成为默认内建浏览器。

## WebView 需要网络权限（Internet Permission）

如果你的安卓web app需要通过网络加载页面，那么app就需要安卓网络权限（ Android internet permission）。如果没有权限，那么你的app压根就不能创建网络连接。当用户安装你的app时，系统会告诉她都需要什么权限，如果用户接受了这些权限，app才会安装。

你的app获取网络权限是通过在app manifest文件中增加internet permission 节点来实现的。下面是一个例子

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.jenkov.androidwebappexamples" >

    <uses-permission android:name="android.permission.INTERNET" />

    <application ...>
    </application>

</manifest>
```

通过XML 元素节点<uses-permission android:name="android.permission.INTERNET" /> （通过android:name attribute）指示app 需要网络权限。

## 在布局中插入WebView

为了使用安卓WebView组件，你必须在你app的某处的用户交互界面（GUI）中来插入它。常用做法是在你想要显示的地方的布局XML文件中插入WebView元素。下面是在布局文件中个插入WebView的例子

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    android:paddingBottom="@dimen/activity_vertical_margin"
    tools:context=".MainActivity">

    <WebView
        android:id="@+id/webview"
        android:layout_alignParentTop="true"
        android:layout_alignParentLeft="true"
        android:layout_width="match_parent"
        android:layout_height="match_parent"></WebView>

</RelativeLayout>    
```

这个例子在相对布局（RelativeLayout）中加入了WebView。在我的关于安卓布局的教程中有更多关于布局的详细解释。

## 从代码访问WebView

一旦你在布局中插入了WebView，你就可以在你的代码中访问。你可以访问WebView来实现任何想实现的东西。通常是在Activity中访问WebView的，例如：

```
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        WebView webView = (WebView)
            findViewById(R.id.webview);
    }

}
```

Activity叫MainActivity，布局文件是activity_main.xml。这个布局文件和前一节提供的样例有些类似。

注意上面代码中的```findViewById(R.id.webview)``` 方法。这个是调用布局文件中的WebView。

同样需要注意，在这里面已经移除了关于ActionBar的内容，如果你想使用，那么记得在Activity中加入的方法。

一旦你获取到了WebView 的引用，那么你就可以配置它了，比如告诉他通过HTTP 加载URLs或者其他事情。本教程的剩余部分会详细讲解你可以用WebView来做什么。

## 用WebView加载URL

一旦你获取到WebView的引用实例后你就可以让它去加载一个URL。URL上加载的资源（HTML，test,图片等等）就会在WebView中展现。下面是加载URL代码：

```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    WebView webView = (WebView) findViewById(R.id.webview);

    webView.loadUrl("http://tutorials.jenkov.com");
}
```

loadUrl() 方法负责加载URL。

## 在WebView中启用 JavaScript

默认WebView组件禁用JavaScript。要想让加载页面中的JavaScript执行，你必须获取WebView 的 WebSettings对象，并且调用setJavaScriptEnabled(true)方法。下面是个启用的例子：

```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    WebView webView = (WebView) findViewById(R.id.webview);

    WebSettings webSettings = webView.getSettings();
    webSettings.setJavaScriptEnabled(true);

    webView.loadUrl("http://tutorials.jenkov.com");
}
}
```

## 从JavaScript调用Java代码

运行在Android WebView 中的JavaScript 是有可能去调用在你的app中的Java代码中的。要从JavaScript调用你的Java，你需要创建一个让运行在WebView中的JavaScript 可以看见的 JavaScript 接口对象

首先，让我们看看JavaScript接口对象是什么样子的，下面是个JavaScript接口类

```
public class AppJavaScriptProxy {

    private Activity activity = null;

    public AppJavaScriptProxy(Activity activity) {
        this.activity = activity;
    }

    @JavascriptInterface
    public void showMessage(String message) {

        Toast toast = Toast.makeText(this.activity.getApplicationContext(),
                message,
                Toast.LENGTH_SHORT);

        toast.show();
    }

}
```

就像你看到的一样，JavaScript接口类（我管这个叫代理（Proxy）替换接口）中的showMessage()方法弹出了一个传递进这个方法的message参数的提示。

要想让AppJavaScriptProxy在你的WebView中的JavaScript可以调用。你在 WebView 实例中必须调用addJavaScriptInterface()方法。下面是样例：

```
webView.addJavascriptInterface(new AppJavaScriptProxy(this), "androidAppProxy");
```

传递到addJavaScriptInterface()方法的第一个参数是JavaScript接口对象自己。第二个参数是绑定在JavaScript接口对象的全局JavaScript变量。下面是一个关于JavaScript访问到上面的JavaScript接口对象的样例：

```
if(typeof androidAppProxy !== "undefined"){
    androidAppProxy.showMessage("Message from JavaScript");
} else {
    alert("Running outside Android app");
}
```

注意JavaScript首先检查是否有androidAppProxy全局变量定义，如果定义了那么，JavaScript 可以在WebView中调用这个方法，进而调用你的Java代码。如果全局变量没定义，那么在web app中就不能运行，并使用另外一个机制来显示消息（javascript:alert()）。

### 因为安全原因禁用JavaScript 接口

当你向WebView实例中注册一个JavaScript接口对象时，JavaScript接口对象对加载进入WebView中的所有页面都是可用的。这意味着，如果用户引导至我们自己站点/我们内部app 之外的网站，这个页面也在同一个WebView战线，那么外部页也可以访问JavaScript接口对象。这是一个潜在的安全风险。

你可以检查WebView访问的URL来判断是否JavaScript接口对象可以被调用。无论如何，获取WebView的URL，你可以通过调用getUrl()方法。但是这个方法仅仅可以在安卓app 的UI线程中调用，并且JavaScript接口对象所在的线程调用方法并不在UI线程。因此，你必须像下面一样进行URL检查：

```
public class AppJavaScriptProxy {

    private Activity activity = null;
    private WebView  webView  = null;

    public AppJavaScriptProxy(Activity activity, WebView webview) {

        this.activity = activity;
        this.webView  = webview;
    }

    @JavascriptInterface
    public void showMessage(final String message) {

        final Activity theActivity = this.activity;
        final WebView theWebView = this.webView;

        this.activity.runOnUiThread(new Runnable() {

            @Override
            public void run() {
                if(!theWebView.getUrl().startsWith("http://tutorials.jenkov.com")){
                    return ;
                }

                Toast toast = Toast.makeText(
                        theActivity.getApplicationContext(),
                        message,
                        Toast.LENGTH_SHORT);

                toast.show();
            }
        });
    }
}
```

首先，注意AppJavaScriptProxy构造函数中有Activity和WebView实例。其次注意showMessage()方法现在调用了Activity的runOnUiThread()方法，传递了一个Runnable去运行。在Runnable内部我们可以安全的访问WebView。

在Runnable内部，我们首先检查了WebView加载的URL是否是自己的站点（在这里是 http://tutorials.jenkov.com )），如果不是showMessage()方法不做任何事情，立即返回。

## 从安卓Web App调用JavaScript

可以从你的安卓web app中调用WebView中的JavaScript函数。你有两中方式来做这个，下面是相关描述：

### 通过WebView 的loadUrl()方法调用JavaScript

在API 19之前（Android 4.4 - Kitkat）你可以使用WebView loadUrl()方法：

```
webView.loadUrl("javascript:theFunction('text')");
```

这和在WebView中的内部点击JavaScript链接是一个效果。这个调用不会再新加载的页面中执行，换句话说，仅对当前加载的页面生效。

这个方法的缺点是你不能获取任何调用函数的返回值。然而，你可以安排调用的JavaScript函数来回调到Java传递结果（怎么调用看上面一节）。

### 用 WebView evaluateJavascript()方法调用JavaScript

第二个可选的方案仅对于 Android API level 19 (包含，Android Kitkat) 以上可用，安卓的WebView类才会包含evaluateJavascript()方法。这个方法可以运行JavaScript就像在页面中运行一样。下面是个例子：

```
webView.evaluateJavascript("fromAndroid()", new ValueCallback<String>() {
    @Override
    public void onReceiveValue(String value) {
        //store / process result received from executing Javascript.
    }
});
```

evaluateJavascript()第一个参数传递了JavaScript字符来评估（执行）。第二个参数是一个回调对象，包含了一个叫onReceiveValue的方法。当JavaScript被执行并且获取到结果，the onReceiveValue() 方法就会被调用。安卓web app可以处理JavaScript的返回值啦。

## 在WebView中使用WebViewClient保持导航

用户点击加载到WebView中页面的链接后，默认的行为是在浏览器中加载点击的链接。这意味着系统中的默认安卓浏览器会打开链接，并不是在你的WebView中。这样对于你的用户体验就非常不好了。
To keep page navigation within the WebView and hence within your app, you need to create a subclass of WebViewClient, and override its shouldOverrideUrlLoading(WebView webView, String url) method. Here is how such a WebViewClient subclass could look:

> 为了在你app内部的WebView中保存页面导航，你不要创建WebViewClient的子类，并重载（override）shouldOverrideUrlLoading(WebView webView, String url)方法。下面是样例：

```
private class MyWebViewClient extends WebViewClient {
    @Override
    public boolean shouldOverrideUrlLoading(WebView webView, String url) {
        return false;
    }
}
```

当 shouldOverrideUrlLoading() 方法返回false时，传递至方法的URL会在WebView中加载。在上面的样例中所有的URLs都会在WebView中加载。

如果你想区分哪些URLs应该WebView加载，哪些应该安卓浏览器加载，你应该实现shouldOverrideUrlLoading()方法，检查传递的URL。下面例子是jenkov.com 地址在WebView中加载，其余的用浏览器加载。

```
public class WebViewClientImpl extends WebViewClient {

    @Override
    public boolean shouldOverrideUrlLoading(WebView webView, String url) {
        if(url.indexOf("jenkov.com") > -1 ) return false;
        return true;
    }

}
```
太古怪了，返回真不会导致外部浏览器加载。不如说，这会导致URL压根就不会被加载。要打开其他的URLs在外部安卓浏览器，你还需要调用Intent。下面是加了Intent的WebViewClient

```
 public class WebViewClientImpl extends WebViewClient {

    private Activity activity = null;

    public WebViewClientImpl(Activity activity) {
        this.activity = activity;
    }

    @Override
    public boolean shouldOverrideUrlLoading(WebView webView, String url) {
        if(url.indexOf("jenkov.com") > -1 ) return false;

        Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
        activity.startActivity(intent);
        return true;
    }

}

```

注意WebViewClientImpl类现在在构造函数中接收了一个Activity参数。这个activity用来触发安卓浏览器打开URL那个Intent。

### 在WebView中设置WebViewClient

在你WebViewClient子类有任何变化前，你必须在WebView中设置（译者注：不大好理解），下面是样例：

```
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        WebView webView = (WebView) findViewById(R.id.webview);

        WebSettings webSettings = webView.getSettings();
        webSettings.setJavaScriptEnabled(true);

        WebViewClientImpl webViewClient = new WebViewClientImpl(this);
        webView.setWebViewClient(webViewClient);


        webView.loadUrl("http://tutorials.jenkov.com");
    }

}
```

## 使用后退按钮导航WebView浏览历史

如果你点击你安卓设备的后退按钮，默认的响应是app退回到安卓操作系统/主界面（或者其他你在做之前开始了的web应用程序）。甚至当你在WebView中导航到站点新的页面，后退按钮也会引导用户退出app。

直接取代现存的app，你应该让用户向在浏览器中后退按钮一样，点击后退引导前一个页面。仅仅在用户在加载的第一个页面再次点击后退的时候才会让用户退出app。

要达到这个后退按钮的功能，前面说的MainActivity文件应该做些小的修改，需要重载 onKeyDown()方法，下面是例子：

```
public class MainActivity extends Activity {

    private WebView webView = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        this.webView = (WebView) findViewById(R.id.webview);

        WebSettings webSettings = webView.getSettings();
        webSettings.setJavaScriptEnabled(true);

        WebViewClientImpl webViewClient = new WebViewClientImpl(this);
        webView.setWebViewClient(webViewClient);

        webView.loadUrl("http://tutorials.jenkov.com");
    }


    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if ((keyCode == KeyEvent.KEYCODE_BACK) && this.webView.canGoBack()) {
            this.webView.goBack();
            return true;
        }

        return super.onKeyDown(keyCode, event);
    }

}
```

第一步，WebView实例分配给到一个成员变量，因此onKeyDown() 方法可以访问它。

第二步，重载onKeyDown()方法检查是否WebView可以后退。如果用户导航不是第一个加载的页面，那么WebView可以后退。WebView包含了浏览历史和正常的浏览器一样。如果WebView可以后退（有浏览历史）那么WebView就后退。否则在超类的onKeyDown() 会被调用，导致默认的app中的后退按钮。

注意onKeyDown()方法检查什么键会被按。仅仅“后退”按钮按下的时候才会操作浏览器历史，另外按钮会交给 onKeyDown()方法的超类处理。

## 中断 WebView HTTP 请求

当WebView加载页面的时候，是可以终止HTTP请求的，包括页面里面的资源（images, JavaScript files, CSS files etc.）。当你中断HTTP请求时候，你可以决定是否WebView应该加载正常资源，或者你想要返回另外一个同样资源的版本（就是本地资源呗，比如本来请求网络图片，改变成请求本地图片）。

要中断HTTP请求，你需要在WebViewClient实现类中重载 shouldInterceptRequest()方法，下面是样例：

```
public class WebViewClientImpl extends WebViewClient {

    private Activity activity = null;

    public WebViewClientImpl(Activity activity) {
        this.activity = activity;
    }

    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        if(url.indexOf("jenkov.com") > -1 ) return false;

        Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
        activity.startActivity(intent);
        return true;
    }

    @Override
    public WebResourceResponse shouldInterceptRequest(WebView view, String url) {
        if(url.startsWith("http://tutorials.jenkov.com/images/logo.png")){
            String mimeType = "image/png";
            String encoding = "";
            InputStream input = ...;


            WebResourceResponse response =
                    new WebResourceResponse(mimeType, encoding, input);

            return response;
        }

        return null;
    }
}

```

注意在样例代码下放的shouldInterceptRequest()方法的实现。shouldInterceptRequest() 实现看起来是确认是否URL指向为log png图片。如果是，那么创建一个WebResourceResponse实例，并返回。

WebResourceResponse构造函数需要一个可以加载匹配URL的InputStream。在上面样例中InputStream扁郎没有初始化。这个样例就是看看怎么构造请求的，而不是专注于如何初始化InputStream（输入流）。后面你会看到一点代码如何从APK文件中嵌入的assets目录中加载资源。

如果shouldInterceptRequest()方法返回NULL，那么WebView会通过网络正常加载资源。

正常加载资源在前面的文中已经提到了，因此也包含了 shoulldOverrideUrlLoading()方法，尽管这个方法不是中断WebView HTTP资源必须的。

### 从 App APK Assets中加载资源

如果你想要中断HTTP请求，并且加载web app assets目录中的资源，你可以这样么。加载资源从资源目录要比从网络加载更有的原因如下：

It is faster to load files from the assets directory than over the network.
When you load less data over the wireless internet (WIFI / Mobile data) the Android device uses less battery power.
When you load less data over the internet, your app uses less of the user's internet bandwidth quota.
Of course this makes sense with data that is pretty much static, like logo image files, JavaScript files, CSS files and the like, which does not change so often.

* 加载资源目录文件会比网络更快
* 通过网络加载（WIFI /手机信号）少量数据会比加载本地文件更费电
* 通过网络加载会占用用户网络流量。

当然这是有前提的，就是你加载的是静态文件，例如图标文件、脚本文件、CSS文件等等不经常变化的文件。

在安卓app中的资源目录位于你 Android Studio 项目的src/main/assets。如果你的项目中不包含这个目录，你需要手动创建一个，assets 目录仅仅是一个规范，没什么特别。

所有在assets目录的文件都会打包嵌入在APK文件中。因此当你的web app安装在用户安卓设备后，所有在资源目录的静态资源也同样安装在手机里面。

要访问资源目录里面的文件，你需要获取AssetManager实例。通过调用 getAssets()方法就可以了。下面是一个WebViewClient子类的实现（基于更早的实现）显示了如何中断HTTP请求并通过AssetManager 读取本地资源：

```
public class WebViewClientImpl extends WebViewClient {

    private Activity activity = null;

    public WebViewClientImpl(Activity activity) {
        this.activity = activity;
    }

    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        if(url.indexOf("jenkov.com") > -1 ) return false;

        Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
        activity.startActivity(intent);
        return true;
    }


    @Override
    public WebResourceResponse shouldInterceptRequest(WebView view, String url) {

        if(url.startsWith("http://tutorials.jenkov.com/images/logo.png")){
            return loadFromAssets(url, "images/logo.png", "image/png", "");
        }

        return null;
    }

    private WebResourceResponse loadFromAssets( String url,
        String assetPath, String mimeType, String encoding){

        AssetManager assetManager = this.activity.getAssets();
        InputStream input = null;
        try {
            Log.d(Constants.LOG_TAG, "Loading from assets: " + assetPath);


            input = assetManager.open("/images/logo.png");
            WebResourceResponse response =
                    new WebResourceResponse(mimeType, encoding, input);

            return response;
        } catch (IOException e) {
            Log.e("WEB-APP", "Error loading " + assetPath + " from assets: " +
                e.getMessage(), e);
        }
        return null;
    }
}
```

注意shouldInterceptRequest()方法检查URL是否是logo URL，如果是就从本地加载。The loadFromAssets()中通过activity 的 getAssets()方法获取一个AssetManager实例。一旦AssetManager获取到，那么目标资源的输入流也可以获取到，包含在返回的WebResourceResponse对象中。

样例的结果是logo.png文件从资源目录里面加载，而不是网络加载。这让logo更快的载入，app让人用的更舒服了（好舒服）。

##在安卓设备中缓存资源

就像之前你看到的一样，WebView是可以中断HTTP其你去的，它也能让你自己从网络上加载资源。因此是有可能在安卓设备中使用WebView来缓存资源数据的，从缓存中加载资源要比网络加载快多了。除了你可以替换新版本的缓存文件外，效果近似于加载APK内部资源。

你可以用标准的Java URL类通过HTTP下载资源，样例代码稍后可以看到。

你可以在安卓设备的内部存储或者外部存储卡上存储数据。无论是哪一种都可以用标准Java File类进行访问。稍后可以看到使用内部app 存储缓存文件的代码。

我实现了一个简单的UrlCache类来演示怎么下载和缓存文件。你可以用这个类作为你URL缓存类的基础。UrlCache在WebViewClientImpl（WebViewClient 的子类）类中使用。下面就是WebViewClientImpl怎么样查找本地缓存资源的样例（不是上一章说的APK 中的assets）：

```
public class WebViewClientImpl extends WebViewClient {

    private Activity activity = null;
    private UrlCache urlCache = null;

    public WebViewClientImpl(Activity activity) {
        this.activity = activity;
        this.urlCache = new UrlCache(activity);

        this.urlCache.register("http://tutorials.jenkov.com/", "tutorials-jenkov-com.html",
                "text/html", "UTF-8", 5 * UrlCache.ONE_MINUTE);
        
    }

    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        if(url.indexOf("jenkov.com") > -1 ) return false;

        Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
        activity.startActivity(intent);
        return true;
    }

    @Override
    public WebResourceResponse shouldInterceptRequest(WebView view, String url) {

        return this.urlCache.load(url);
    }
}
```

上面的示例代码在WebViewClientImpl构造函数中创建了一个新的UrlCache实例。由于UrlCache需要通过通过Activity来访问内部存储，所以在UrlCache构造函数中传递入了一个Activity对象

创建好UrlCache实例后，构造函数注册了一个本地缓存的资源对象。资源通过URL、缓存文件名、mime type、编码和缓存保存时间注册。当调用UrlCache's load()方法时，资源会在 UrlCache中注册、下载、缓存。注册资源并不会调用下载，仅 load()后开始 。

WebVieClientImpl 的shouldInterceptRequest() 非常简单，通过UrlCache.load()返回结果。The load() 方法返回一个通过下载和缓存的生成的WebResourceResponse对象，如果UrlCache中没注册这个缓存信息，那么就返回null对象。因此，如果UrlCache没有返回对象，那么WebView就会自己执行下载（因为UrlCache.load()返回null，shouldInterceptRequest()也会返回null）。

下面是UrlCache的代码（没包含导入部分）：


```
public class UrlCache {

  public static final long ONE_SECOND = 1000L;
  public static final long ONE_MINUTE = 60L * ONE_SECOND;
  public static final long ONE_HOUR   = 60L * ONE_MINUTE;
  public static final long ONE_DAY    = 24 * ONE_HOUR;

  private static class CacheEntry {
    public String url;
    public String fileName;
    public String mimeType;
    public String encoding;
    public long   maxAgeMillis;

    private CacheEntry(String url, String fileName,
        String mimeType, String encoding, long maxAgeMillis) {

        this.url = url;
        this.fileName = fileName;
        this.mimeType = mimeType;
        this.encoding = encoding;
        this.maxAgeMillis = maxAgeMillis;
    }
  }


  protected Map<String, CacheEntry> cacheEntries = new HashMap<String, CacheEntry>();
  protected Activity activity = null;
  protected File rootDir = null;


  public UrlCache(Activity activity) {
    this.activity = activity;
    this.rootDir  = this.activity.getFilesDir();
  }

  public UrlCache(Activity activity, File rootDir) {
    this.activity = activity;
    this.rootDir  = rootDir;
  }



  public void register(String url, String cacheFileName,
                       String mimeType, String encoding,
                       long maxAgeMillis) {

    CacheEntry entry = new CacheEntry(url, cacheFileName, mimeType, encoding, maxAgeMillis);

    this.cacheEntries.put(url, entry);
  }



  public WebResourceResponse load(String url){
    CacheEntry cacheEntry = this.cacheEntries.get(url);

    if(cacheEntry == null) return null;

    File cachedFile = new File(this.rootDir.getPath() + File.separator + cacheEntry.fileName);

    if(cachedFile.exists()){
      long cacheEntryAge = System.currentTimeMillis() - cachedFile.lastModified();
      if(cacheEntryAge > cacheEntry.maxAgeMillis){
        cachedFile.delete();

        //cached file deleted, call load() again.
        Log.d(Constants.LOG_TAG, "Deleting from cache: " + url);
        return load(url);
      }

      //cached file exists and is not too old. Return file.
      Log.d(Constants.LOG_TAG, "Loading from cache: " + url);
      try {
        return new WebResourceResponse(
                cacheEntry.mimeType, cacheEntry.encoding, new FileInputStream(cachedFile));
      } catch (FileNotFoundException e) {
        Log.d(Constants.LOG_TAG, "Error loading cached file: " + cachedFile.getPath() + " : "
                + e.getMessage(), e);
      }

    } else {
      try{
        downloadAndStore(url, cacheEntry, cachedFile);

        //now the file exists in the cache, so we can just call this method again to read it.
        return load(url);
      } catch(Exception e){
        Log.d(Constants.LOG_TAG, "Error reading file over network: " + cachedFile.getPath(), e);
      }
    }

    return null;
  }



  private void downloadAndStore(String url, CacheEntry cacheEntry, File cachedFile)
    throws IOException {

    URL urlObj = new URL(url);
    URLConnection urlConnection = urlObj.openConnection();
    InputStream urlInput = urlConnection.getInputStream();

    FileOutputStream fileOutputStream =
            this.activity.openFileOutput(cacheEntry.fileName, Context.MODE_PRIVATE);

    int data = urlInput.read();
    while( data != -1 ){
      fileOutputStream.write(data);

      data = urlInput.read();
    }

    urlInput.close();
    fileOutputStream.close();
    Log.d(Constants.LOG_TAG, "Cache file: " + cacheEntry.fileName + " stored. ");
  }
}
```

UrlCache类在检查内部存储前不会检查APK assets目录。如果你需要这个功能，恰好留给你做个练习。这应该不是很难，在教程中已经包含所有单独需要实现功能的代码了。你只需要选择合并就可以了。

## 预读Web资源

有时你可能想要预存取用户将要使用的资源。例如，假想用户打开你web app的第一个页面，里面包含了很多其他页面的链接。要想点击首页链接执行的更快的话，你可能会想要在后台执行那些链接目标页的预存取。有几种方式显示web资源的预读，方法的选择取决于你知道哪种资源预读。

### 预读已知的Web资源

如果你已经知道要预读什么web资源。你的app可以在开始的时候做一些工作。如果你使用在之前我教程中提到的缓存，那么简单的注册这些资源的URLs，并调用UrlCache load()方法就行了。至于什么时候开始预读参看 “什么时候开始预读”章节。

### 预读未知的Web资源

如果你不知道你要缓存什么资源，那么你就不能再你的应用中硬编码进行缓存。比如说首页是加载的文章列表，而文章列表对于网站运营来说是经常变化的，那就没办法告诉app到底怎么缓存了。

在这种情况下web页面控制web资源的预读。可以通过两种方式做这个：第一种方式像之前所说让web 页通过JavaScript传递文章列表到原生app里面进行缓存。

第二种方法是web页以某种方式（隐藏div）后台加载这些资源。web app 拦截这些资源请求，并形成缓存。一旦用户点击链接，那么就从缓存中加载。对于这种方法，原生app就需要知道哪些资源需要缓存了，例如URL中包含 /article/，或者.html结尾的，或者在某个域名下的(例如 inside tutorials.jenkov.com)资源。

### 什么时候开始预读

直到第一个页面完全加载之前预读不会开始。否则预读就会和当前加载页产生网络竞争。如果你预读未知页面，你可以通过WebViewClient子类 的 onPageFinished()方法，下面列子是关于onPageFinished()的简单实现：


```
@Override
public void onPageFinished(WebView view, String url) {
    super.onPageFinished(view, url);

    if("http://tutorials.jenkov.com/".equals(url)){
        this.urlCache.load("http://tutorials.jenkov.com/java/index.html");
    }
}
```


这个例子使用UrlCache，注意onPageFinished()方法是怎样在完成当前页面加载后加载其他页面的。当然代码里面的http://tutorials.jenkov.com/java/index.html 也需要注册到缓存中（我的UrlCache类需要缓存资源先进行注册）

如果你预读未知页面，那么你可以向上面所述一样进行预读，而是在shouldInterceptRequest()方法里面操作。下面是代码

```
@Override
public WebResourceResponse shouldInterceptRequest(WebView view, String url) {

    if(url.startsWith("http://mydomain.com/article/") {
        String cacheFileName = url.substring(url.lastIndexOf("/"), url.length());
        this.urlCache.register(url, cacheFileName,
                "text/html", "UTF-8", 60 * UrlCache.ONE_MINUTE);

    }

    return this.urlCache.load(url);
}
```

上面的预读机制仅在你知道什么URL要去缓存的时候。上面样例中所有的URLs（例如内部hidden div）都会通过web页进行加载，http://mydomain.com/article/开头的访问则是通过缓存加载。

如果你不知道什么样的URL要去缓存，你得让你的web页来告诉原生app要加载页面了，是否需要缓存。或者通过JavaScript函数传入一组资源列表进行缓存。暂时我先不写这个样例代码，后继我会更新本教程。

## 过滤HTML

有时候，你可能想要在HTML显示在Android WebView之前过滤掉某些在HTML中的元素。你也许会重用本地HTML，但仅仅移除Logo图片或者占用较多页面空间的文本，亦或是想复用某些内容，例如action bar中的logo。

要过滤HTML从服务器端加载内容，你需要在WebView中执行拦截。在之前的教程中我描述了怎样拦截。当你拦截到你想要过滤的请求时，你可以自己下载HTML文件，修改文件，并用输入流（InputStream）实现来封装，最后返回一个WebResourceResponse对象。你应该使用ByteArrayInputStream，因为这个类用于转换字符串（String）和本地文件成字节数组（byte array）非常方便。

如果过滤器资源不经常改变，你可以在本地缓存过滤器版本用来加速资源请求。你可以在之前的UrlCache类中创建filtering 函数。当你注册到缓存中一个URL时候，你可以同时增加一个可能的WebResourceFilter（或者其他你想调用的过滤器接口）

## 用HTML 5本地缓存缓存值

HTML 5本地缓存使在web应用中客户端浏览器存储数据成为可能。数据可以存储在会话存储（sessionStorage）或者本地存储全局JavaScript对象。你可以读取[HTML本地存储相关教程](http://tutorials.jenkov.com/html5/local-storage.html)。

会话存储（sessionStorage）仅仅在浏览器窗口（WebView）打开的时候保存数据。当浏览器窗口关闭（app销毁WebView，用户关闭app），所有的存储数据就被删除了。

本地存储（localStorage）就算app重启也会保存。我建议使用本地存储，本地存储具体保存时间取决度安卓系统什么时候满了，需要空间的时候可能会删除这部分数据。

要启动HTML 5本地存储，你需要在WebView 的WebSettings对象上调用setDomStorageEnabled(true)，样例如下：

```
WebSettings webSettings = webView.getSettings();

webSettings.setJavaScriptEnabled(true);
webSettings.setDomStorageEnabled(true);
```

This code is typically located inside the onCreate() method of the Activity subclass hosting the WebView.

这段代码通常放在持有WebView对象的Activity子类的onCreate() 方法中。


## 设备方向变动处理

当用户持有的安卓设备由肖像转风景（就是竖屏转横屏），反之亦然，默认的行为是安卓销毁可见的visible，并重建一个新设备方向的activity。不幸的是，这样就意味着所有的在activity中的View实例也被销毁了，包含WebView。当WebView销毁后，内部浏览器也销毁了。因此，当你改变设备方向的时候浏览器历史也销毁了。

此外，如果浏览器重建，你可能想要保持销毁之前加载的页面地址，而不是站点的首页。

为了避免在设备方向变化时销毁重建WebView，你可能复用一些在activity实例外的WebView的实例，我会在这章节中讲述如何实现。

首先我们创建两个布局文件，第一个布局文件是RelativeLayout布局，包含WebView。第二个布局文件仅仅包含RelativeLayout元素，但没有具体内容（没有WebView）。

第一个布局文件(activity_main.xml) 

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:tools="http://schemas.android.com/tools"
                android:id="@+id/firstViewGroup"
                android:layout_width="match_parent"
                android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
                android:paddingRight="@dimen/activity_horizontal_margin"
                android:paddingTop="@dimen/activity_vertical_margin"
                android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">

    <WebView
            android:id="@+id/webview"
            android:layout_alignParentTop="true"
            android:layout_alignParentLeft="true"
            android:layout_width="match_parent"
            android:layout_height="match_parent"></WebView>

</RelativeLayout>
```

第二个布局文件 (activity_main_no_webview) 

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:tools="http://schemas.android.com/tools"
                android:id="@+id/secondViewGroup"
                android:layout_width="match_parent"
                android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
                android:paddingRight="@dimen/activity_horizontal_margin"
                android:paddingTop="@dimen/activity_vertical_margin"
                android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">


</RelativeLayout>
```

接下来我们对MainActivity稍微变更。WebView引用修改成静态的以便于独立于MainActivity。接下来我们增加一个静态引用到包含WebView的ViewGroup中。这样我们就可以从前一个ViewGroup中移除WebView并加入到新的 ViewGroup 中了。改变后 MainActivity 如下：

```
public class MainActivity extends Activity {

    private static ViewGroup webViewParentViewGroup = null;
    private static WebView   webView                = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        if(webView != null){
            webViewParentViewGroup.removeView(webView);

            setContentView(R.layout.activity_main_no_webview);

            webViewParentViewGroup = (ViewGroup) findViewById(R.id.secondViewGroup);
            webViewParentViewGroup.addView(this.webView);
        } else {
            setContentView(R.layout.activity_main);

            webViewParentViewGroup = (ViewGroup) findViewById(R.id.firstViewGroup);
            webView                = (WebView) findViewById(R.id.webview);

            //configure WebView - left out here for brevity

            webView.loadUrl("http://tutorials.jenkov.com");
        }
    }


    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if ((keyCode == KeyEvent.KEYCODE_BACK) && this.webView.canGoBack()) {
            this.webView.goBack();
            return true;
        }

        return super.onKeyDown(keyCode, event);
    }


}
```

设备方向改变会引起onCreate() 方法重新执行。如果你的静态webView变量是Null，那么第一个带有webView的布局文件就会被使用。WebView 会从上层布局中抽取出来并使用。为了保证代码简洁，我去掉了WebView的配置代码。

如果静态 webView 变量不为空，那么 webView 实例已经存在，app 可以服用啦。首先会从最新销毁的 activity 布局的父 ViewGroup 中移除 webView ，并加入到最新创建的activity 根 ViewGroup 中。activity 使用第二个布局文件来填充。第二个文件没有 WebView ，但有个让我们可以插入WebView 的 ViewGroup 。

## 使用loadData直接加载HTML数据到WebView

不通过URL加载 HTML 文件是可能的。你可以用 WebView 的 loadData() 方法：

```
String data = "<html><body><h1>HTML Loaded Directly</h1></body></html>";

webView.loadData(data, "text/html", "UTF-16");
```

loadData() 方法也可以加载其他类型的数据，文本文件、 JS脚本 。但 HTML 文件是最经常使用的。

### 在WebView中用base URL加载HTML

If the HTML you load directly into the WebView in your Android web app contains links with relative URLs, then these links may not work correctly. When you load HTML directly into the WebView the HTML has no base URL from which to interpret the relative URLs. The Android WebView component has a solution for that.

You can load HTML directly into the WebView with a base URL. The base URL is then used to resolve all relative URLs in the HTML. To load HTML with a base URL you have to use the loadDataWithBaseURL() method. Here is a WebView loadDataWithBaseURL() example:

如果你在你的web 页面中使用相对路径，那么很可能会出现问题。当你的WebView加载相对路径的时候，需要一个根路径（base URL）。要在WebView中实现这个，你需要调用loadDataWithBaseURL()方法加载 base URL ，然后base URL 会解析在里面的 relative URL，样例如下：



```
String baseUrl    = "http://tutorials.jenkov.com";
String data       = "Relative Link";
String mimeType   = "text/html";
String encoding   = "UTF-8";
String historyUrl = "http://tutorials.jenkov.com/jquery/index.html";

webView.loadDataWithBaseURL(baseUrl, data, mimeType, encoding, historyUrl);

```

The loadDataWithBaseURL()方法有5个参数
* data是载入WebView的HTML。
* mimeType 是载入WebView的数据的mimeType（上面例子是text/html）。
* encoding 是数据二进制编码（样例是UTF-8）。注意：我尝试过使用UTF-16 作为编码，但某些时候亚洲文字就会显示的特别奇怪。
* baseUrl 参数是所有相对URL路径的基础路径
* historyUrl 参数是写入WebView内部历史导航器的地址。如果用户load多个页面，那么点击“后退”按钮的时候。WebView会使用这个URL进行后退。你可能要拦截加载的URL，由于导航后退WebView历史记录不会引起加载HTML页面，而是到URL指定的历史记录参数（或者空白页面about:blank 如果historyUrl设置为空的话）。



## 响应式Web设计

响应式Web设计意味着在设备中的 web设计可以自己改变自己，或者说响应自己来显示HTML页面。如果你尝试在Android web app中包裹一个web站点，如果web站点也需要在其他设备上显示(桌面浏览器，平板电脑，还可能电视机)，那么就有必要做个响应式web设计了。

制作响应式设计又是一个单独的内容了，我在我的教程中简单介绍了一下相关的基础[Responsive Web Design](http://tutorials.jenkov.com/responsive-mobile-friendly-web-design/index.html).

响应式web设计的一个核心是CSS媒体查询（[CSS Media Queries](http://tutorials.jenkov.com/css/media-queries.html)）。CSS媒体查询可以让你根据屏幕宽度、设备的方向、像素密度或者其他的设备屏幕显示属性接受不同的CSS样式。
