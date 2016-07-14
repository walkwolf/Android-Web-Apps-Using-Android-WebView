# Android Web Apps 使用 Android WebView


Android Web App or Android Hybrid App?
WebView is Based on Chrome
WebView Needs Internet Permission
Insert a WebView Into a Layout
Accessing the WebView From Code
Loading a URL Into the WebView
Enabling JavaScript in the WebView
Calling From JavaScript To The Android Web App
Disabling the JavaScript Interface Object For Security
Calling From Android Web App to JavaScript
Calling JavaScript via WebView loadUrl()
Calling JavaScript via WebView evaluateJavascript()
Keeping Page Navigation Inside the WebView With a WebViewClient
Setting the WebViewClient on the WebView
Navigating WebView History With The Back Button
Intercepting WebView HTTP Requests
Loading Resources From App APK Assets
Caching Web Resources in The Android Device
Prefetching Web Resources
Prefetching Known Web Resources
Prefetching Unknown Web Resources
When to Start The Prefetching
Filtering HTML
Caching Values in HTML 5 Local Storage
Device Orientation Change Handling
Loading HTML Directly Into a WebView With loadData()
Loading HTML Into a WebView With a Base URL
Responsive Web Design
	
Jakob Jenkov
最后更新: 2014-12-19
    
An Android web app is an application that uses the Android WebView component to render part of the Android app's GUI. The WebView component is a full-fledged browser implemented as a View subclass so you can embed it inside your Android app's GUI anywhere you like. It is common to let the WebView take up most of the screen space, but you can also just let the browser take half the screen or whatever partition that suits your app.

安卓web app是利用安卓 WebView组件渲染来左外安卓app 用户图形界面（GUI）的一部分。WebView 组件是View的子类，同时也是成熟浏览器的实现，你可以在你的安卓app 用户图形界面（GUI）的任意位置中使用它。通常WebView都占有屏幕的大部分，当然你可以可以让它仅占用屏幕一半，甚至可以某个小块中使用它。

##完全 web app 还是混合 app（Hybrid App）?
Calling an Android app that uses a WebView internally for an Android Web App may sound a bit misleading, since the app is actually a mix of a native Android app and a web app. Parts of the app uses native Android components and parts of the app is rendered using web technologies (HTML, CSS, JavaScript, SVG) inside a WebView.
因为app确实混合了原生app和web app，就称一个安卓app用WebView展现app内容叫web app 听起来有些不好理解。其实在WebView 内部是用安卓组件和app的部分是使用web 技术(HTML, CSS, JavaScript, SVG)来展现的。

Another common term for an Android web app is Android Hybrid App. The term Android Hybrid app means that the app is a hybrid between a different app types. It is typically used about apps that are a hybrid between native apps and web apps. However, the term Android hybrid app by itself doesn't clearly convey what the app is a hybrid between. It could be a hybrid between a native app, a P2P app and a Client / Server app. You have to know that the term refers specifically to a hybrid between a native app and a web app. Therefore I will go with the term Android Web App because at least it says that the app uses web technologies.
web app的另外一个常见的术语混合app（Hybrid App）。意思是说app使用了几种app的展现方式。典型的是使用了原生app和web app。无论如何混合app术语中没有清晰的说明是怎么那些app混合的。可能混合内容：原生app,p2p app,客户端服务器app (Client / Server app)。你必须知道这个术语通常指原生app和web app混合。因此我会使用安卓web app，因为至少这说明这个app使用了web技术。

##WebView 是基于 Chrome的
From Android version 4.4 (Kitkat) the WebView component is based on the same code as Chrome for Android. This assures a more consistent rendering of your web app whether the user sees it inside your native Android web app (hybrid) or via their Android Chrome browser.
从安卓4.4（Kitkat）版本开始WebView组建就是基于与Chrome代码一样的内容提供给安卓使用。这确保了对于你的原生web app（混合模式）或者通过安卓Chrome浏览器看到的大多数效果是一致的。

Before Android 4.4 the WebView was based on the internal Android browser, but Chrome is replacing the old Android browser as the default / built-in browser.
在安卓4.4版本前，WebVeiw是依据安卓内部浏览器的，但是之后Chrome特带了老的安卓浏览器成为默认内建浏览器。

##WebView 需要网络权限（Internet Permission）
If your Android web app needs to load web pages over the internet the app needs the Android internet permission. If your app does not have internet permission it cannot create any internet connections at all. When the user installs your app he / she is told what permissions the app needs. If the user accepts that, the app can be installed.
如果你的安卓web app需要通过网络加载页面，那么app就需要安卓网络权限（ Android internet permission）。如果没有权限，那么你的app压根就不能创建网络连接。当用户安装你的app时，系统会告诉她都需要什么权限，如果用户接受了这些权限，app才会安装。

Your app gets internet permission by adding the internet permission element to the app's manifest file. Her is an example of an Android manifest file with internet permission:
你的app获取网络权限是通过在app manifest文件中增加internet permission 节点来实现的。下面是一个例子

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.jenkov.androidwebappexamples" >

    <uses-permission android:name="android.permission.INTERNET" />

    <application ...>
    </application>

</manifest>
It is the XML element <uses-permission android:name="android.permission.INTERNET" /> (via the android:name attribute) which signals that the app needs internet permission.

通过XML 元素节点<uses-permission android:name="android.permission.INTERNET" /> （通过android:name attribute）指示app 需要网络权限。

##在布局中插入WebView 
In order to use the Android WebView component you must insert it into the GUI of your app somewhere. This is most often done by inserting a WebView element into the layout XML file for the layout you want the WebView to be displayed in. Here is an example layout file with a WebView embedded:
为了使用安卓WebView组件，你必须在你app的某处的用户交互界面（GUI）中来插入它。常用做法是在你想要显示的地方的布局XML文件中插入WebView元素。下面是在布局文件中个插入WebView的例子

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
This example creates a RelativeLayout with a WebView inside. Layouts are explained in more detail in my tutorial about Android layout.
这个例子在相对布局（RelativeLayout）中加入了WebView。在我的关于安卓布局的教程中有更多关于布局的详细解释。

## 从代码访问WebView
Once you have inserted a WebView into a layout somewhere, you can access it from your code. You need to access the WebView to make it do anything interesting. You typically access a WebView from inside an Activity. Here is an example Activity subclass which accesses a WebView embedded in its layout XML file:
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

The Activity subclass is called MainActivity and the activity layout file is called activity_main.xml. This layout file looks like the example layout file shown in the previous section about inserting a WebView into a layout.

Activity叫MainActivity，布局文件是activity_main.xml。这个布局文件和前一节提供的样例有些类似。

Notice the method call findViewById(R.id.webview) in the code above. It is this method call which locates the WebView in the layout file.
注意上面代码中的```findViewById(R.id.webview)``` 方法。这个是调用布局文件中的WebView。

Notice also that the methods that normally configure the Android ActionBar have been left out of this example. If you want your app to have an ActionBar, remember to add those methods to your Activity subclass.
同样需要注意，在这里面已经移除了关于ActionBar的内容，如果你想使用，那么记得在Activity中加入的方法。

Once you have obtained a reference to the WebView you can configure it and instruct it to load URLs via HTTP and many other interesting things. The rest of this tutorial will get into deeper details about what you can do with a WebView.
一旦你获取到了WebView 的引用，那么你就可以配置它了，比如告诉他通过HTTP 加载URLs或者其他事情。本教程的剩余部分会详细讲解你可以用WebView来做什么。

## 用WebView加载URL
Once you have a reference to the WebView instance you can instruct it to load a URL. The resource (HTML, test, an image etc.) loaded from the URL will be displayed inside the WebView. Here is an example of how to load a URL inside a WebView:
一旦你获取到WebView的引用实例后你就可以让它去加载一个URL。URL上加载的资源（HTML，test,图片等等）就会在WebView中展现。下面是加载URL代码：

```
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    WebView webView = (WebView) findViewById(R.id.webview);

    webView.loadUrl("http://tutorials.jenkov.com");
}
```

It is the call to the WebView's loadUrl() method which loads the URL into the WebView.
loadUrl() 方法负责加载URL。

## 在WebView中启用 JavaScript
By default the Android WebView component has JavaScript disabled. To enable execution JavaScript inside the pages loaded, you must obtain the WebView's WebSettings object and call setJavaScriptEnabled(true) on it. Here is an example of how to enable JavaScript in Android's WebView:
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
```

## 从JavaScript调用Java代码
It is possible for JavaScript running inside an Android WebView to call Java code inside your Android web app. To call from JavaScript to Java you need to create a JavaScript Interface object which is made available to the JavaScript running in the WebView.

运行在Android WebView 中的JavaScript 是有可能去调用在你的app中的Java代码中的。要从JavaScript调用你的Java，你需要创建一个让运行在WebView中的JavaScript 可以看见的 JavaScript 接口对象

First, lets see what such a JavaScript interface object looks like. Here is an example JavaScript interface class:
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
As you can see, the showMessage() of this JavaScript interface class (which I call a Proxy instead of Interface) shows a Toast containing the message passed to the method in its message parameter.
就像你看到的一样，JavaScript接口类（我管这个叫代理（Proxy）替换接口）中的showMessage()方法弹出了一个传递进这个方法的message参数的提示。

To make an object of the AppJavaScriptProxy class available to the JavaScript running inside a WebView, you must call the addJavaScriptInterface() method on the WebView instance. Here is a WebView addJavaScriptInterface() example:
要想让AppJavaScriptProxy在你的WebView中的JavaScript可以调用。你在 WebView 实例中必须调用addJavaScriptInterface()方法。下面是样例：
```
webView.addJavascriptInterface(new AppJavaScriptProxy(this), "androidAppProxy"); 
```

The first parameter passed to addJavaScriptInterface() is the JavaScript interface object itself. The second parameter is the name of the global JavaScript variable which the JavaScript interface object is bound to. Here is an example of how JavaScript would access the above JavaScript interface object:

传递到addJavaScriptInterface()方法的第一个参数是JavaScript接口对象自己。第二个参数是绑定在JavaScript接口对象的全局JavaScript变量。下面是一个关于JavaScript访问到上面的JavaScript接口对象的样例：

```
if(typeof androidAppProxy !== "undefined"){
    androidAppProxy.showMessage("Message from JavaScript");
} else {
    alert("Running outside Android app");
}
```

Notice how the JavaScript first checks if the androidAppProxy global variable is defined. If it is, the JavaScript is running inside your Android web app. If the global variable is not defined, the JavaScript is not running inside your Android web app, and it will have to use another mechanism for showing its message.

注意JavaScript首先检查是否有androidAppProxy全局变量定义，如果定义了那么，JavaScript 可以在WebView中调用这个方法，进而调用你的Java代码。如果全局变量没定义，那么在web app中就不能运行，并使用另外一个机制来显示消息（javascript:alert()）。

## 因为安全原因禁用JavaScript 接口

When you register a JavaScript interface object on a WebView instance, the JavaScript interface object is available to all pages loaded into the WebView. That means, that if the user navigates to a page outside your own website / web app, and this page is also displayed inside the same WebView, then that foreign page also has access to the JavaScript interface object. This is a potential security risk.

当你向WebView实例中注册一个JavaScript接口对象时，JavaScript接口对象对加载进入WebView中的所有页面都是可用的。这意味着，如果用户引导至我们自己站点/我们内部app 之外的网站，这个页面也在同一个WebView战线，那么外部页也可以访问JavaScript接口对象。这是一个潜在的安全风险。

You can check the URL of the WebView to see if the given JavaScript interface object method should be callable or not. However, to obtain the URL of the WebView you have to call its getUrl() method. But this method can only be called by the UI thread of the Android app, and the thread calling the methods in the JavaScript interface object is not the UI thread. So, you will have to implement the URL check liks this:

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

First, notice that the AppJavaScriptProxy constructor now takes both an Activity and a WebView instance. Second, notice how showMessage() now calls the Activity method runOnUiThread(), passing along a Runnable to execute. Inside this Runnable we can access the WebView safely.

Inside the Runnable we first check if the URL loaded in the WebView is within our own website (in this case http://tutorials.jenkov.com) and if not, the showMessage() method returns immediately without doing anything.

Calling From Android Web App to JavaScript
It is also possible to call JavaScript functions inside the WebView from your Android web app. You have two possibilities to do so. Both will be covered below.

Calling JavaScript via WebView loadUrl()

Before API level 19 (before Android 4.4 - Kitkat) you can use the WebView loadUrl() method like this:

webView.loadUrl("javascript:theFunction('text')");
This has the same effect as clicking on a JavaScript link inside the page currently loaded in the WebView. It does not result in a new page being loaded. Rather it results in the JavaScript being executed within the currently loaded page.

The disadvantage of this method is that you cannot get any return values from the called function. However, you can arrange for the called JavaScript function to call back into Java with the result (how to call Java from JavaScript is explained earlier in this tutorial).

Calling JavaScript via WebView evaluateJavascript()

The second option is only available from Android API level 19 (Android Kitkat) and forward, Android's WebView class contains a method called evaluateJavascript(). This method can execute JavaScript as if it was executed inside the page currently loaded into the WebView . Here is an example of executing JavaScript via WebView evaluateJavascript() :
```
webView.evaluateJavascript("fromAndroid()", new ValueCallback<String>() {
    @Override
    public void onReceiveValue(String value) {
        //store / process result received from executing Javascript.
    }
});
```

The first parameter passed to evaluateJavascript() is the JavaScript string to evaluate (execute). The second parameter is a callback object which contains a single method named onReceiveValue. When the JavaScript has been evaluated and a result obtained from it, the onReceiveValue() method of this callback object is called. The Android web app can then process the value returned from exeuting the JavaScript.

Keeping Page Navigation Inside the WebView With a WebViewClient
The the users clicks a link in the web page loaded into the WebView, the default behaviour is to load that URL of the link in the system Android browser. That means that the Android browser app is opened and the page for the link is shown in the Android browser, and not inside the WebView in your app. This breaks the user experience of your app's users.

To keep page navigation within the WebView and hence within your app, you need to create a subclass of WebViewClient, and override its shouldOverrideUrlLoading(WebView webView, String url) method. Here is how such a WebViewClient subclass could look:

```
private class MyWebViewClient extends WebViewClient {
    @Override
    public boolean shouldOverrideUrlLoading(WebView webView, String url) {
        return false;
    }
}
```
When the shouldOverrideUrlLoading() method returns false, the URLs passed as parameter to the method is loaded inside the WebView instead of the Android standard browser. In the above example all URls will be loaded inside the WebView.

If you want to distinguish between that URLs are loaded inside the WebView and which are loaded in the Android browser, your implementation of shouldOverrideUrlLoading() can examine the URL passed to it as parameter. Here is an example that only loads URLs that contains jenkov.com inside the WebView and all other URLs in the Android browser:
```
public class WebViewClientImpl extends WebViewClient {

    @Override
    public boolean shouldOverrideUrlLoading(WebView webView, String url) {
        if(url.indexOf("jenkov.com") > -1 ) return false;
        return true;
    }

}
```
Weirdly enough, returning true from shouldOverrideUrlLoading() does not cause the URL to be loaded in the external Android browser. Rather, it causes the URL not to be loaded at all. To open all other URLs in the external Android browser you will have to fire an Intent. Here is how the WebViewClient subclass looks with that added:
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
Notice how the WebViewClientImpl class now takes an Activity in its constructor. This activity is used to fire the Intent which opens the URL in the Android browser.

Setting the WebViewClient on the WebView

Before your WebViewClient subclass has any effect you must set an instance of it on the WebView. Here is how that looks:
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
Navigating WebView History With The Back Button
If you click the "back" button of your Android device while running the app we have developed so far, the default reaction is that the app goes "back" to the Android operating system / home screen (or whatever else you were doing before you started the web app). Even if you have navigated a few pages into the website or web app loaded inside the WebView, the "back" button takes the user out of the app.

Instead of exiting the app directly, we would like the app to go back through the browsing history of the webview when the "back" button is clicked. Thus, the "back" button will function just like the "back" button in a browser. Only if the WebView is back to the first page loaded and the user clicks the "back" button again, do we want to exit the app.

To achieve this effect of the "back" button the MainActivity class shown earlier must be modified a bit. We have to override the onKeyDown() method in the Activity class. Here is how the modified MainActivity class looks with the modification:
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
First of all, the WebView instance is now being assigned to a member variable, so the onKeyDown() method can access it.

Second, the onKeyDown() method has been overridden with an implementation that first checks if the WebView has can go back. If the user has navigated away from the first page loaded inside the WebView, then the WebView can go back. The WebView contains a browsing history just like a normal browser. If the WebView can go back (has a browsing history) then the WebView is instructed to go back. Else, the onKeyDown() implementation in the superclass is called, which will result in default behaviour of the "back" button, which is exiting the app.

Note that the onKeyDown() method checks what key was pressed. Only if the "back" button is pressed will it attempt to manipulate the WebView's browsing history. All other button presses are handled by the superclass onKeyDown() implementation.

Intercepting WebView HTTP Requests
It is possible to intercept HTTP requests made by an Android WebView when loading a page, or resources used inside a page (images, JavaScript files, CSS files etc.). When you intercept an HTTP request you can decide whether the WebView should load the resource normally, or whether you want to return another version of the same resource which is then used inside the WebView.

To intercept an HTTP request made by a WebView you need to override the shouldInterceptRequest() method in your WebViewClient subclass. Here is a shouldInterceptRequest() example implementation:
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
Notice the implementation of the shouldInterceptRequest() method at the bottom of this code example. This shouldInterceptRequest() implementation looks at the URL to determine if the URL points to the logo PNG image. If it does, it creates a WebResourceResponse instance and returns it.

The WebResourceResponse constructor needs an InputStream from which it can load the resource matching the URL. In the example above the InputStream variable is not initialized. The example just shows ... instead of showing how to initialize an InputStream. You will see a bit later how to load resources from the assets directory embedded in your Android web app's APK file.

If the shouldInterceptRequest() method returns null, then the WebView will load the resource normally (over the internet).

This example builds on the WebViewClient subclass shown earlier in this tutorial. Therefore it also contains the shoulldOverrideUrlLoading() method, although this method is not necessary to intercept WebView HTTP requests.

Loading Resources From App APK Assets

In case you want to intercept an HTTP request and load the given resource from the web app assets directory instead, you can do so. Loading resources from the assets directory is better than loading resources over the network for the following reasons:

It is faster to load files from the assets directory than over the network.
When you load less data over the wireless internet (WIFI / Mobile data) the Android device uses less battery power.
When you load less data over the internet, your app uses less of the user's internet bandwidth quota.
Of course this makes sense with data that is pretty much static, like logo image files, JavaScript files, CSS files and the like, which does not change so often.

The assets directory of an Android app is located at src/main/assets inside your Android Studio project. If your project does not contain an assets directory you must create one yourself. It's just a regular directory named assets. No magic.

All files and folders inside this assets directory will be packaged and embedded inside the app's APK file. Thus, when your web app is installed on the user's Android device, all the static assets inside the assets directory are too.

To access files inside the assets directory you must obtain an instance of the AssetManager. You do so by calling the Activity getAssets() method. Here is a WebViewClient subclass implementation (based on the earlier implementation) which shows you how to intercept HTTP requests and read the resources via the AssetManager:
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
Notice how the shouldInterceptRequest() checks if the URL is the logo URL, and if it is, loads the logo from the static assets instead of over the network. The loadFromAssets() method obtains an AssetManager instance by calling the getAssets() method of the Activity instance passed to the WebViewClientIpml class in its constructor. Once a AssetManager has been obtained, an InputStream to the desired resource can be obtained and included in the returned WebResourceResponse.

The result of this example is that the logo.png file is loaded from the assets directory instead of over the network. This makes the logo faster to load, and the app more pleasant to use.

Caching Web Resources in The Android Device
As you have seen, it is possible to intercept HTTP requests made by the WebView. It is also possible to load the resources over the network yourself. Thus it is possible to cache resources used by the WebView locally in the Android device, and load them from the cache rather than over the network. The effect is similar to embedding resources inside the APK file, except you can replace the cached files with newer versions from time to time.

You can download resources over HTTP using the standard Java URL class. This class works just fine in Android. You will see an example later.

You can store the resources in either the internal storage or external storage on the Android device. Both internal and external storage can be accessed like a file system via the standard Java File class. You will also see an example of that later. The example later uses the internal app storage to store cached files.

To show you how to download and cache files used by a WebView I have implemented a simple UrlCache class. You can use that class as a base for your own URL cache class. The UrlCache class is used inside the WebViewClientImpl (the WebViewClient subclass). Here is first how the WebViewClientImpl looks with the local resource caching built in (and without the assets loading from previous section):
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
This example creates a new UrlCache instance in the constructor of WebViewClientImpl. The Activity is passed to the UrlCache constructor because UrlCache needs the Activity to access the internal storage.

After creating the UrlCache instance, the constructor registers one resource which should be cached locally. The resource is registered with its URL, cache file name, mime type, encoding and maximum age. When a resource is registered in the UrlCache it will be downloaded and cached when you call the UrlCache's load() method. Registering the resource does not download it. Only load() does.

The shouldInterceptRequest() method of the WebVieClientImpl is now very simple. All it does is to return the value returned by UrlCache.load(). The load() method returns a WebResourceResponse object it the resource was downloaded and cached, and null if the resource is unknown to the UrlCache (if the URL has not been registered for caching via register()). Thus, if the resource is not returned by the UrlCache the WebView will just download it itself (because UrlCache.load() returns null and hence shouldInterceptRequest() returns null).

Here is the code for the UrlCache class (without import statements) :
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
This UrlCache class does not check the APK file's assets directory for embedded resources before checking the internal storage. I will leave it as an exercise to you to add that in case you need that. It should not be too hard to add, given that this tutorial contains code examples for both options separately. You just need to merge the code of one into the other.

Prefetching Web Resources
Sometimes you may want to prefetch web resources that you know the user may be loading in a near future. For instance, imagine if the user starts your Android web app and the first page shown in the app contains links to other pages. To make the pages linked to from the first page load faster, you may want to prefetch these pages in the background.

There are several ways to implement web resource prefetching. Which method to use depends on whether you know ahead of time which web resources to prefetch.

Prefetching Known Web Resources

If you already know what web resources to prefetch, your app can do so when it starts up. If you are using a cache like the one I described earlier in this tutorial, simply register the URLs of the resources to prefetch, and then call the UrlCache load() method. See the section later on "when to start prefetching" for more detail about exactly when and where in your app to start the prefetching.

Prefetching Unknown Web Resources

If you don't know ahead of time what web resources to prefetch you cannot hardcode into the Android web app which resources to prefetch. For instance, imagine the first web page loaded shows a list of links to articles, and the URLs of the articles changes regularly like on the frontpage of a news website. There is no way your Android app can know at the time it is developed what articles to prefetch.

In this situation the actual web page has to control the prefetching of the web resources. You can do so in two ways. The first way is to let the web page call from JavaScript into the Android web app, as explained earlier. The web page will pass a list of resources to prefetch.

The second way is that the web page actually loads these resources in the background somehow (e.g. inside a hidden div element). The Android web app intercepts the loading of these resources and stores them in the cache. Once the user navigates to these resources they will be loaded directly from the cache and thus load fast. For this to work the Android web app needs to be able to see from the URL of a web resource if it is to be cached or not. For instance, all URLs which contains /article/ or which ends in .html and is inside the same domain as the first page (e.g. inside tutorials.jenkov.com).

When to Start The Prefetching

Prefetching of pages should not start until the first page is fully loaded. Otherwise the prefetch traffic may slow down the loading of resources needed for the first page.

If you are prefetching known pages, you can do so by overriding the onPageFinished() method of your WebViewClient subclass. Here is a very simple example of how overriding onPageFinished() could look:
```
@Override
public void onPageFinished(WebView view, String url) {
    super.onPageFinished(view, url);

    if("http://tutorials.jenkov.com/".equals(url)){
        this.urlCache.load("http://tutorials.jenkov.com/java/index.html");
    }
}
```
This example uses the UrlCache shown earlier in this text. Notice how the onPageFinished() method loads another page if the page that just finished is the front page of my tutorial website. Of course, the URL http://tutorials.jenkov.com/java/index.html would have to be registered for caching for this to have any caching effect (my UrlCache class requires that resources that are to be cached are registered first).

If you are prefetching unknown pages then you can do a prefetching similar to the above, but inside the shouldInterceptRequest() method. Here is an example of how that could look:
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
The above prefetching mechanism is only working if you can see on the URL itself whether a given resource should be prefetched or not. In the above example all URLs loaded by the web page (e.g. inside a hidden div) where the URL starts with http://mydomain.com/article/ will be cached so that future requests to these URLs are read directly from the cache.

If you cannot see from the URL if a resource should be cached or not, you will have make the web page tell the Android app, either by having the web page call into Android, or by having the Android app execute a JavaScript function which returns a list of resources to cache. I don't have working code at this point in time showing you how to do this, but I will update this tutorial when I do.

Filtering HTML
Sometimes you may want to filter the HTML loaded from a web server before displaying it in the Android WebView. You might want to reuse an existing HTML page but remove e.g. logo images or text which takes up too much space in your Android web app, and which may be duplicate if you already show e.g. a logo in the app's action bar.

To filter HTML loaded from a web server you must intercept the HTTP request made by the WebView. I have described how to do that earlier in this tutorial. When you intercept the HTTP request you want filter, you can download the HTML file yourself, modify it, and wrap it in an InputStream implementation which you can return inside a WebResourceResponse object. You could use a ByteArrayInputStream since it is easy to transform either a String or a local file into a byte array.

If the filtered resource does not change often, you can cache the filtered version locally to speed up future requests to that resource. You can build a filtering function into the UrlCache shown earlier. When you register a URL for caching, you could add the possibility to add a WebResourceFilter too (or whatever else you call your filter interface).

Caching Values in HTML 5 Local Storage
HTML 5 local storage makes it possible for web applications to store values locally in the browser. The values can be stored in either the sessionStorage or localStorage global JavaScript object. You can read the specifics of how HTML 5 local storage works in my HTML 5 Local Storage Tutorial.

The values stored in sessionStorage are only kept for as long as the browser window (the WebView) is open. When the browser window is closed (when the app destroys the WebView or the user shuts down the Android app), all sessionStorage values are deleted.

The values stored in localStorage are kept across app restarts. If you plan to store values across app restarts I suggest you use localStorage. Keep in mind that the Android OS may delete you localStorage variables if it needs space.

To enable HTML 5 local storage you must call setDomStorageEnabled(true); on the WebSettings object of the WebView. Here is a WebSettings.setDomStorageEnabled() example:
```
WebSettings webSettings = webView.getSettings();

webSettings.setJavaScriptEnabled(true);
webSettings.setDomStorageEnabled(true);
```

This code is typically located inside the onCreate() method of the Activity subclass hosting the WebView.

Device Orientation Change Handling
When the user changes the orientation of his Android device from portrait to landscape and vice versa, the default behaviour is that Android destroys the visible activity, and recreates it in the new device orientation. Unfortunately this means that all View instances inside the destroyed activity are destroyed too, including the WebView. When the WebView is destroyed, so is its internal browsing history. Thus, when you change device orientation the browsing history is destroyed.

Additionally, if the WebView is recreated you would have to keep track of what page was being displayed in the WebView that was destroyed, so you can load that page into the WebView instead of loading the first page of the website / web app again.

Instead of destroying and recreating the WebView on device orientation change, you can reuse the same WebView instance across the recreated activity instances. I will explain how to do that in the following sections.

First we create two layout files instead of one for the Android web app main activity. The first layout file contains a RelativeLayout with a WebView inside, and the second layout file contains only a RelativeLayout element with no children (no WebView).

Here is how the first layout file (activity_main.xml) looks:
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
Here is how the second layout file (activity_main_no_webview) looks:

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

Next, we change the MainActivity a bit. The reference to the WebView is made static so it will be independent of created and destroyed instances of MainActivity. Second, we add a static reference to the ViewGroup containing the WebView. That way we can remove the WebView from the previous ViewGroup and add the WebView to the new ViewGroup whenever device orientation changes. Here is how the MainActivity class looks with device orientation change handling implemented:
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
The device orientation change handling all happens inside the onCreate() method. If the static webView variable is null, then the first layout file with the WebView inside is used. The WebView is extracted from the inflated layout, and the WebView is configured. I have left out the WebView configuration code in this example to keep the example shorter.

If the static webView variable is not null, then a WebView instance already exists, and your Android web app has to reuse it. It will first remove the WebView instance from the parent ViewGroup of the newly destroyed activity's layout, and add the WebView to the newly created activity's root ViewGroup. The newly created activity uses the second layout file to inflate its layout. The second layout has no WebView in the layout file. Just a root ViewGroup element into which we can insert the existing WebView.

Loading HTML Directly Into a WebView With loadData()
It is possible to load HTML directly into the WebView without loading it from a URL. You do so using the WebView's loadData() method. Here is a WebView loadData() example:
```
String data = "<html><body><h1>HTML Loaded Directly</h1></body></html>";
```
webView.loadData(data, "text/html", "UTF-16");
The loadData() method can also be used to load other types of data than HTML, like text files, JavaScript etc. but HTML files a very common use case.

Loading HTML Into a WebView With a Base URL

If the HTML you load directly into the WebView in your Android web app contains links with relative URLs, then these links may not work correctly. When you load HTML directly into the WebView the HTML has no base URL from which to interpret the relative URLs. The Android WebView component has a solution for that.

You can load HTML directly into the WebView with a base URL. The base URL is then used to resolve all relative URLs in the HTML. To load HTML with a base URL you have to use the loadDataWithBaseURL() method. Here is a WebView loadDataWithBaseURL() example:
```
String baseUrl    = "http://tutorials.jenkov.com";
String data       = "Relative Link";
String mimeType   = "text/html";
String encoding   = "UTF-8";
String historyUrl = "http://tutorials.jenkov.com/jquery/index.html";
```

webView.loadDataWithBaseURL(baseUrl, data, mimeType, encoding, historyUrl);
The loadDataWithBaseURL() method takes 5 parameters. The data parameter is the HTML to load into the WebView. The mimeType is the mime type of the data loaded into the WebView (in this example text/html). The encoding is the binary encoding of the data (in this example UTF-8). Note: I tried using UTF-16 as encoding but the content displayed in the WebView looked pretty strange (like Asian characters).

The baseUrl parameter is the base URL from which all relative URLs in the loaded HTML is interpreted.

The historyUrl parameter is the URL to write into the WebView's internal navigation history for the HTML loaded into the WebView. If the user navigates from the loaded HTML to another page, and then clicks the "back" button, then it is this URL the WebView will navigate back to. You may have to intercept the loading of this URL, since navigating back the WebView's history will not take you to the loaded HTML, but to the URL specified in the historyUrl parameter (or about:blank if historyUrl is set to null).

Responsive Web Design
Responsive web design means web design that can adapt itself (respond) to the device that displays the HTML page. If you are trying to wrap a website in an Android web app, if that website is also displayed on other devices (desktop browsers, tablets, perhaps even TVs) it makes sense to make the web design responsive.

Making responsive web design is a whole topic in itself. I have explained the basics in my tutorial about Responsive Web Design.

One of the core technologies to make responsive web design is CSS Media Queries. CSS media queries enable you to apply different CSS styles depending on the screen width, device orientation, pixel density and other screen specific properties of the device displaying the website.