---
title: Intent详解
date: 2016-10-30 15:42:58
tags: Android基础
categories: Android基础
---

## Intent是什么

### 定义

- Intent被译作意图，其实还是很能传神的，Intent期望做到的，就是把实现者和调用者完全解耦，调用者专心将以意图描述清晰，发送出去，就可以梦想成真，达到目的

- Intent是一种运行时绑定（run-time binding）机制，它能在程序运行过程中连接两个不同的组件。通过Intent，你的程序可以向Android表达某种请求或者意愿，Android会根据意愿的内容选择适当的组件来完成请求。比如，有一个Activity希望打开网页浏览器查看某一网页的内容，那么这个Activity只需要发出WEB_SEARCH_ACTION给Android，Android就会根据Intent的请求内容，查询各组件注册时声明的IntentFilter，找到网页浏览器的Activity来浏览网页。这个解释好像理解起来就容易好多，我们通过intent传入某种意图，而android就会根据这种意图，自动寻找合适的activity来启动，如果有多个条件符合的activity，就以列表的方式让用户手动选择一个

### 显示Intent与隐式Intent

- __例一：__

```java
Intent intent = new Intent();  
intent.setClass(Context packageContext, OtherActivity.class);  
startActivity(intent);  
```

- __例二：__

```java
Intent intent = new Intent();  
intent.setAction(Intent.ACTION_NEW);  
startActivity(intent);  
```

- 从这两段代码中可以明显看出，同样是startActivity，但第一个明确指出是启动OtherActivity，而在第二个例子中，并没有明确指出要启动哪个activity！

这两种书写方式就分别是显示Intent（例一）、隐式Intent（例二）；

- 显式intent是指明确指出此intent是启动哪个activity.

- 隐式intent是指并没有指出要启动哪个activity,而要系统自动去判断并启动匹配的activity.

### 显式Intent小结

- 有两种方式来显示的指示要启动的Activity:

- __方式一：__（通过setClassName）

```java
Intent intent = new Intent();  
//表示希望启动com.example.test_permission包中的com.example.test_permission.MainActivity  
intent.setClassName("com.example.test_permission", "com.example.test_permission.MainActivity");  
startActivity(intent);  
```

- __方式二：__（通过SetClass）

```java
Intent intent = new Intent();  
intent.setClass(Context packageContext, OtherActivity.class);  
startActivity(intent);  
```


<!-- more -->

- 同样，setClass(Context packageContext, OtherActivity.class);是指启动packageContext包里的OtherActivity.class类；

## 针对隐式intent，Activity的匹配原则

- 上面我们讲了隐式intent是要靠系统自动去匹配并启动某个activity的。那系统是怎样匹配activity的呢，系统是怎样知道这个actitiy就是某个intent想要的呢。

- 某个activity能不能被某个intent激活，要看这个activity是不是符合这个intent的要求，而某个activity能被哪个intent激活是有定义的，定义就在AndroidManifest.xml

- 打开AndroidManifest.xml，找到任意一个activity，一般都能看到一段代码，举个例子，我随便复制一个，如下：

```xml
<activity  
    android:name=".MainActivity"  
    android:label="@string/app_name" >  
    <intent-filter>  
        <action android:name="android.intent.action.MAIN" />  
  
        <category android:name="android.intent.category.LAUNCHER" />  
    </intent-filter>  
</activity>  
```

- 看这里有一对标签<intent-filter>……</intent-filter>
在这个标签里定义的所有东东都是用来定义该activity可以被哪些intent激活的，如果匹配，就会被激活！！！！！

- 在<intent-filter>里有以下几个属性可以让intent来匹配：Action、Category、Data；下面逐一介绍：

### Action：该activity可以执行的动作

- 该标识用来说明这个activity可以执行哪些动作，所以当隐式intent传递过来action时，如果跟这里<intent-filter>所列出的任意一个匹配的话，就说明这个activity是可以完成这个intent的意图的，可以将它激活！！！！

- 常用的Action如下所示：

```java
ACTION_CALL activity 启动一个电话.  
ACTION_EDIT activity 显示用户编辑的数据.  
ACTION_MAIN activity 作为Task中第一个Activity启动  
ACTION_SYNC activity 同步手机与数据服务器上的数据.  
ACTION_BATTERY_LOW broadcast receiver 电池电量过低警告.  
ACTION_HEADSET_PLUG broadcast receiver 插拔耳机警告  
ACTION_SCREEN_ON broadcast receiver 屏幕变亮警告.  
ACTION_TIMEZONE_CHANGED broadcast receiver 改变时区警告.  
```

#### __两条原则：__

- 一条<intent-filter>元素至少应该包含一个<action>，否则任何Intent请求都不能和该<intent-filter>匹配。

- 如果Intent请求的Action和<intent-filter>中个任意一条<action>匹配，那么该Intent就可以激活该activity(前提是除了action的其它项也要通过)。

#### __两条注意：__

如果Intent请求或<intent-filter>中没有说明具体的Action类型，那么会出现下面两种情况

- 如果<intent-filter>中没有包含任何Action类型，那么无论什么Intent请求都无法和这条<intent-filter>匹配
 
- 反之，如果Intent请求中没有设定Action类型，那么只要<intent-filter>中包含有Action类型，这个Intent请求就将顺利地通过<intent-filter>的行为测试

### Category：于指定当前动作（Action）被执行的环境

- 即这个activity在哪个环境中才能被激活。不属于这个环境的，不能被激活

- 常用的Category属性如下所示：

```java
CATEGORY_DEFAULT：Android系统中默认的执行方式，按照普通Activity的执行方式执行。表示所有intent都可以激活它　  
  
CATEGORY_HOME：设置该组件为Home Activity。  
  
CATEGORY_PREFERENCE：设置该组件为Preference。　  
  
CATEGORY_LAUNCHER：设置该组件为在当前应用程序启动器中优先级最高的Activity，通常为入口ACTION_MAIN配合使用。　  
  
CATEGORY_BROWSABLE：设置该组件可以使用浏览器启动。表示该activity只能用来浏览网页。　  
  
CATEGORY_GADGET：设置该组件可以内嵌到另外的Activity中。  
```

#### __注意：__

- 如果该activity想要通过隐式intent方式激活，那么不能没有任何category设置，至少包含一个android.intent.category.DEFAULT


### Data  执行时要操作的数据

在目标<data/>标签中包含了以下几种子元素，他们定义了url的匹配规则：

- android:scheme 匹配url中的前缀，除了“http”、“https”、“tel”...之外，我们可以定义自己的前缀

- android:host 匹配url中的主机名部分，如“google.com”，如果定义为“*”则表示任意主机名

- android:port 匹配url中的端口

- android:path 匹配url中的路径

在XML中声明可以操作的data域应该是这样的：

```xml
<activity android:name=".TargetActivity">  
<intent-filter>  
    <action android:name="com.scott.intent.action.TARGET"/>  
    <category android:name="android.intent.category.DEFAULT"/>  
    <data android:scheme="scott" android:host="com.scott.intent.data" android:port="7788" android:path="/target"/>  
</intent-filter>  
</activity>  
```

#### __注意：__

- 这个标识比较特殊，它定义了执行此Activity时所需要的数据，也就是说，这些数据是必须的！！！！！所有如果其它条件都足以激活该Activity，但intent却没有传进来指定类型的Data时，就不能激活该activity！！！！

## Intent的七大属性

- Intent七大属性是指Intent的ComponentName、Action、Category、Data、Type、Extra以及Flag，七个属性，总体上可以分为3类：

- 第一类：启动，有ComponentName（显式）,Action（隐式），Category（隐式）。

- 第二类：传值，有Data（隐式），Type（隐式），Extra（隐式、显式）。

- 第三类：启动模式，有Flag。

- 下面我们逐一来说

### ComponentName

- Component本身有组件的意思，我们通过设置Component可以启动其他的Activity或者其他应用中的Activity，来看一个简单的实例：

- 启动同一个App中另外一个Activity：

```java
intent = new Intent();  
            intent.setComponent(new ComponentName(this, SecondActivity.class));  
            startActivity(intent); 
```

- 这中启动方式等同于以下两种启动方式：

```java
intent = new Intent(this,SecondActivity.class);  
            startActivity(intent); 
```

```java
intent = new Intent();  
            intent.setClass(this, SecondActivity.class);  
            startActivity(intent);  
```

- 当然，通过设置ComponentName属性我们也可以启动其他App中的Activity，关于这一块的内容大家可以参考 [关于ComponentName的使用](http://blog.csdn.net/u012702547/article/details/49557905)。下面我们看看隐式启动

### Action和Category

- 因为在实际开发中，Action大多时候都是和Category一起使用的，所以这里我们将这两个放在一起来讲解。Intent中的Action我们在使用广播的时候用的比较多，在Activity中，我们可以通过设置Action来隐式的启动一个Activity，比如我们有一个ThirdActivity，我们在清单文件中做如下配置：

```xml
<activity  
    android:name=".ThirdActivity"  
    android:label="@string/title_activity_third" >  
    <intent-filter>  
        <category android:name="android.intent.category.DEFAULT" />  
  
  
        <action android:name="com.qf.ThirdActivity" />  
    </intent-filter>  
</activity>  
```

- 当我们在清单文件中做了这样的配置之后，我们的ThirdActivity会就会响应这个动作，怎么那么怎么响应呢？看下面：

```java
intent = new Intent();  
intent.setAction("com.qf.ThirdActivity");  
startActivity(intent); 
```

- 当然，我们也可以写的更简单一些，如下：

```java
intent = new Intent("com.qf.ThirdActivity");  
            startActivity(intent);  
```

- 通过这中方式我们也可以启动一个Activity，那么大家可能也注意到了，我们的清单文件中有一个category的节点，那么没有这个节点可以吗？不可以！！当我们使用这种隐式启动的方式来启动一个Activity的时候，必须要action和category都匹配上了，该Activity才会成功启动。如果我们没有定义category，那么可以暂时先使用系统默认的category，总之，category不能没有。这个时候我们可能会有疑问了，如果我有多个Activity都配置了相同的action，那么会启动哪个？看看下面这个熟悉的图片：

<center>![](http://o99dg8ap9.bkt.clouddn.com/Intent%E8%AF%A6%E8%A7%A3.png)</center>

- 当我们有多个Activity配置了相同的action的时候，那么系统会弹出来一个选择框，让我们自己选择要启动那个Activity。
action我们只能添加一个，但是category却可以添加多个（至少有一个，没有就要设置为DEFAULT），如下：

```xml
<activity  
    android:name=".ThirdActivity"  
    android:label="@string/title_activity_third" >  
    <intent-filter>  
        <category android:name="android.intent.category.DEFAULT" />  
        <category android:name="mycategory" />  
  
        <action android:name="com.qf.ThirdActivity" />  
    </intent-filter>  
</activity>  
```

- 相应的我们的启动方式也可以修改，如下：

```java
intent = new Intent("com.qf.ThirdActivity");  
            intent.addCategory("mycategory");  
            startActivity(intent); 
```



### Data

- 通过设置data，我们可以执行打电话，发短信，开发网页等等操作。究竟做哪种操作，要看我们的数据格式：

```java
// 打开网页  
intent = new Intent(Intent.ACTION_VIEW);  
intent.setData(Uri.parse("http://www.baidu.com"));  
startActivity(intent);  
// 打电话  
intent = new Intent(Intent.ACTION_VIEW);  
intent.setData(Uri.parse("tel:18565554482"));  
startActivity(intent);  
```


- 当我们的data是一个http协议的时候，系统会自动去查找可以打开http协议的Activity，这个时候如果手机安装了多个浏览器，那么系统会弹出多个浏览器供我们选择。这是我们通过设置Data来启动一个Activity，同时，我们也可以通过设置一个Data属性来将我们的Activity发布出去供别人调用，怎么发布呢？

```xml
<activity  
    android:name=".HttpActivity"  
    android:label="@string/title_activity_http" >  
    <intent-filter>  
        <action android:name="android.intent.action.VIEW" />  
  
        <category android:name="android.intent.category.DEFAULT" />  
  
        <data  
            android:scheme="http" />  
    </intent-filter>  
</activity>  
```

- 在data节点中我们设置我们这个Activity可以打开的协议，我们这里设置为http协议，那么以后要打开一个http请求的时候，系统都会让我们选择是否用这个Activity打开。当然，我们也可以自己定义一个协议（自己定义的协议，由于别人不知道，所以只能由我们自己的程序打开）。比如下面这样：

```xml
<activity  
    android:name=".HttpActivity"  
    android:label="@string/title_activity_http" >  
    <intent-filter>  
        <action android:name="android.intent.action.VIEW" />  
  
        <category android:name="android.intent.category.DEFAULT" />  
  
        <data  
            android:scheme="myhttp" />  
    </intent-filter>  
</activity>  
```

- 那么我们怎么打开自己的Activity呢？

```java
intent = new Intent();  
            intent.setData(Uri.parse("myhttp://www.baidu.com"));  
            startActivity(intent);  
```

- 这个例子没有什么实际意义，我只是举一个自定义协议的栗子

- 其实，说到这里，大家应该明白了为什么我们说data是隐式传值，比如我们打开一个网页，http协议后面跟的就是网页地址，我们不用再单独指定要打开哪个网页。


### Type

- type的存在，主要是为了对data的类型做进一步的说明，但是一般情况下，只有data属性为null的时候，type属性才有效，如果data属性不为null，系统会自动根据data中的协议来分析data的数据类型，而不会去管type，我们先来看看下面一段源码：

```java
/** 
 * Set the data this intent is operating on.  This method automatically 
 * clears any type that was previously set by {@link #setType} or 
 * {@link #setTypeAndNormalize}. 
 * 
 * <p><em>Note: scheme matching in the Android framework is 
 * case-sensitive, unlike the formal RFC. As a result, 
 * you should always write your Uri with a lower case scheme, 
 * or use {@link Uri#normalizeScheme} or 
 * {@link #setDataAndNormalize} 
 * to ensure that the scheme is converted to lower case.</em> 
 * 
 * @param data The Uri of the data this intent is now targeting. 
 * 
 * @return Returns the same Intent object, for chaining multiple calls 
 * into a single statement. 
 * 
 * @see #getData 
 * @see #setDataAndNormalize 
 * @see android.net.Uri#normalizeScheme() 
 */  
public Intent setData(Uri data) {  
    mData = data;  
    mType = null;  
    return this;  
}  
  
/** 
 * Set an explicit MIME data type. 
 * 
 * <p>This is used to create intents that only specify a type and not data, 
 * for example to indicate the type of data to return. 
 * 
 * <p>This method automatically clears any data that was 
 * previously set (for example by {@link #setData}). 
 * 
 * <p><em>Note: MIME type matching in the Android framework is 
 * case-sensitive, unlike formal RFC MIME types.  As a result, 
 * you should always write your MIME types with lower case letters, 
 * or use {@link #normalizeMimeType} or {@link #setTypeAndNormalize} 
 * to ensure that it is converted to lower case.</em> 
 * 
 * @param type The MIME type of the data being handled by this intent. 
 * 
 * @return Returns the same Intent object, for chaining multiple calls 
 * into a single statement. 
 * 
 * @see #getType 
 * @see #setTypeAndNormalize 
 * @see #setDataAndType 
 * @see #normalizeMimeType 
 */  
public Intent setType(String type) {  
    mData = null;  
    mType = type;  
    return this;  
}  
```

- 当我们设置data的时候，系统会默认将type设置为null，当我们设置type的时候，系统会默认将data设置为null.也就是说，一般情况下，data和type我们只需要设置一个就行了，如果我们既想要设置data又想要设置type，那么可以使用

```java
setDataAndType(Uri data, String type)  
```

- 这个方法来完成。下面我们来看看通过给Intent设置type来打开一个音乐播放器。代码如下：

```java
intent = new Intent();  
            intent.setAction(Intent.ACTION_VIEW);  
            Uri data = Uri.parse("file:///storage/emulated/0/xiami/audios/被动.mp3");  
            intent.setDataAndType(data, "audio/mp3");  
            startActivity(intent);  
```

- 如果我们要打开的是视频文件，那么type就要设置为"video/*"，其中*表示支持所有的视频文件

### Extras

- 这个参数不参与匹配activity，而仅作为额外数据传送到另一个activity中，接收的activity可以将其取出来。这些信息并不是激活这个activity所必须的。也就是说激活某个activity与否只上action、data、catagory有关，与extras无关。而extras用来传递附加信息，诸如用户名，用户密码什么的

- 可通过putXX()和getXX()方法存取信息；也可以通过创建Bundle对象，再通过putExtras()和getExtras()方法来存取

__通过bundle对象传递__


- __发送方__

```java
Intent intent = new Intent("com.scott.intent.action.TARGET");    
Bundle bundle = new Bundle();    
bundle.putInt("id", 0);    
bundle.putString("name", "scott");    
intent.putExtras(bundle);    
startActivity(intent);  
```

- __接收方：__

```java
Bundle bundle = intent.getExtras();  
int id = bundle.getInt("id");  
String name = bundle.getString("name");  
```

### Flag

- 通过设置Flag，我们可以设定一个Activity的启动模式，这个和launchMode基本上是一样的，所以我也不再细说，关于launchMode的使用参见 [launchMode使用详解](http://blog.csdn.net/u012702547/article/details/49529825)


## 附《Intent调用常见系统组件方法》

```java
// 调用浏览器  
Uri webViewUri = Uri.parse("http://blog.csdn.net/zuolongsnail");  
Intent intent = new Intent(Intent.ACTION_VIEW, webViewUri);  
  
// 调用地图  
Uri mapUri = Uri.parse("geo:100,100");  
Intent intent = new Intent(Intent.ACTION_VIEW, mapUri);  
  
// 播放mp3  
Uri playUri = Uri.parse("file:///sdcard/test.mp3");  
Intent intent = new Intent(Intent.ACTION_VIEW, playUri);  
intent.setDataAndType(playUri, "audio/mp3");  
  
// 调用拨打电话  
Uri dialUri = Uri.parse("tel:10086");  
Intent intent = new Intent(Intent.ACTION_DIAL, dialUri);  
// 直接拨打电话，需要加上权限<uses-permission id="android.permission.CALL_PHONE" />  
Uri callUri = Uri.parse("tel:10086");  
Intent intent = new Intent(Intent.ACTION_CALL, callUri);  
  
// 调用发邮件（这里要事先配置好的系统Email，否则是调不出发邮件界面的）  
Uri emailUri = Uri.parse("mailto:zuolongsnail@163.com");  
Intent intent = new Intent(Intent.ACTION_SENDTO, emailUri);  
// 直接发邮件  
Intent intent = new Intent(Intent.ACTION_SEND);  
String[] tos = { "zuolongsnail@gmail.com" };  
String[] ccs = { "zuolongsnail@163.com" };  
intent.putExtra(Intent.EXTRA_EMAIL, tos);  
intent.putExtra(Intent.EXTRA_CC, ccs);  
intent.putExtra(Intent.EXTRA_TEXT, "the email text");  
intent.putExtra(Intent.EXTRA_SUBJECT, "subject");  
intent.setType("text/plain");  
Intent.createChooser(intent, "Choose Email Client");  
  
// 发短信  
Intent intent = new Intent(Intent.ACTION_VIEW);  
intent.putExtra("sms_body", "the sms text");  
intent.setType("vnd.android-dir/mms-sms");  
// 直接发短信  
Uri smsToUri = Uri.parse("smsto:10086");  
Intent intent = new Intent(Intent.ACTION_SENDTO, smsToUri);  
intent.putExtra("sms_body", "the sms text");  
// 发彩信  
Uri mmsUri = Uri.parse("content://media/external/images/media/23");  
Intent intent = new Intent(Intent.ACTION_SEND);  
intent.putExtra("sms_body", "the sms text");  
intent.putExtra(Intent.EXTRA_STREAM, mmsUri);  
intent.setType("image/png");  
  
// 卸载应用  
Uri uninstallUri = Uri.fromParts("package", "com.app.test", null);  
Intent intent = new Intent(Intent.ACTION_DELETE, uninstallUri);  
// 安装应用  
Intent intent = new Intent(Intent.ACTION_VIEW);  
intent.setDataAndType(Uri.fromFile(new File("/sdcard/test.apk"), "application/vnd.android.package-archive");  
  
// 在Android Market中查找应用  
Uri uri = Uri.parse("market://search?q=愤怒的小鸟");           
Intent intent = new Intent(Intent.ACTION_VIEW, uri); 
```

## 参考博客

- [intent详解（一）](http://blog.csdn.net/harvic880925/article/details/38399723)

- [关于Intent的七大属性](http://blog.csdn.net/u012702547/article/details/50178429)

- [关于ComponentName的使用](http://blog.csdn.net/u012702547/article/details/49557905)

- [launchMode使用详解](http://blog.csdn.net/u012702547/article/details/49529825)

- [intent详解（二）](http://blog.csdn.net/harvic880925/article/details/38406421)





