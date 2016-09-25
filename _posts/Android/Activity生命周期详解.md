---
title: Activity生命周期详解
date: 2016-09-10 20:17:13
tags: Android
categories: Android
---

## 生命周期

- 官方图解


<center>![](http://o99dg8ap9.bkt.clouddn.com/activity_lifecycle.png)</center>

<!--more-->

### 主要步骤

这张图列出了Activity生命周期最主要的一些方法，启动后依次执行：

onCreate –> onStart –> onResume –> onPause –> onStop –> onDestroy

信很多人也都已经知道以上方法与执行顺序，但是Activity还有其他方法，如onContentChanged， onPostCreate， onPostResume， onConfigurationChanged， onSaveInstanceState， onRestoreInstanceState


### 代码测试

```java
public class MainActivity extends Activity {
    private String TAG="MainActivity";
    /**
     * 首次创建Activity的时调用
     * @param savedInstanceState the saved instance state
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Log.d("TAG","onCreate");
    }
    /**
     * 当Activity的布局改动时，即setContentView()或者addContentView()
     * 方法执行完毕时就会调用该方法
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    public void onContentChanged() {
        super.onContentChanged();
        Log.d(TAG, "onContentChanged: ");
    }
    /**
     * 在Activity即将对用户可见时调用
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    protected void onStart() {
        super.onStart();
        Log.d("TAG","onStart");
    }
    /**
     * 在 Activity 已停止并即将再次启动前调用
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    protected void onRestart(){
        super.onRestart();
        Log.d(TAG, "onRestart: ");
    }

    /**
     * 指onCreate方法彻底执行完毕的回调
     * @param savedInstanceState the saved instance state
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    protected void onPostCreate(Bundle savedInstanceState) {
        super.onPostCreate(savedInstanceState);
        Log.d(TAG, "onPostCreate: ");
    }
    /**
     * 在Activity即将开始与用户交互时调用，此时，Activity 处于
     * Activity 堆栈的顶层，并具有用户输入焦点
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume");
    }

    @Override
    protected void onPostResume() {
        super.onPostResume();
        Log.d(TAG, "onPostResume: ");
    }
    /**
     * 当系统即将开始继续另一个 Activity 时调用
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "onPause");
    }

    /**
     * Activity 对用户不再可见时调用
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    protected void onStop() {
        super.onStop();
        Log.d(TAG, "onStop: ");
    }

    /**
     * 在 Activity 被销毁前调用
     * @author qiu  博客：www.qiuchengjia.cn 时间：2016-09-10
     */
    @Override
    protected void onDestroy(){
        super.onDestroy();
        Log.d(TAG, "onDestroy: ");
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);
        Log.d(TAG, "onConfigurationChanged: ");
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        Log.d(TAG, "onSaveInstanceState: ");
    }

    @Override
    protected void onRestoreInstanceState
    (Bundle savedInstanceState){
        super.onRestoreInstanceState(savedInstanceState);
        Log.d(TAG, "onRestoreInstanceState: ");
    }
}
```

- 程序启动运行并结束上述生命周期的方法执行顺序是这样的：

    onCreate –> onContentChanged –> onStart –> onPostCreate –> onResume –> onPostResume –> onPause –> onStop –> onDestroy

- 具体参考 [Activity](https://developer.android.com/guide/components/activities.html?hl=zh-cn#Creating) 和 [ACTIVITY生命周期详解一](http://stormzhang.com/android/2014/09/14/activity-lifecycle1/)

## 生命周期具体场景

### 首次启动

- onCreate –> onStart –> onResume

### 按下返回按键

- onPause –> onStop –> onDestroy

### 按Home键

- onPause –> onSaveInstanceState –> onStop

### 再次打开

- onRestart –> onStart –> onResume

### 屏幕旋转

- 如果你不做任何配置

    启动Activity会执行如下方法：

    onCreate –> onStart –> onResume

    之后旋转屏幕，则Activity会被销毁并重新创建，之后便会执行如下方法：

    onPause –> onSaveInstanceState –> onStop –> onDestroy –> onCreate –> onStart –> onRestoreInstanceState –> onResume

- 在AndroidManifest配置文件里声明android:configChanges属性
默认屏幕旋转会重新创建，当然可以通过在配置文件里加上如下代码：

```html
android:configChanges="keyboardHidden|orientation|screenSize"
（sdk>13时需加上screenSize）
```

这个时候再旋转屏幕便不会销毁Activity，这时候再旋转屏幕可以看到只会执行onConfigurationChanged方法，有什么在屏幕旋转的逻辑可以重写这个方法：

```java
public void onConfigurationChanged(Configuration newConfig) {
    if (newConfig.orientation == ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE) {
        // TODO:
    }
    super.onConfigurationChanged(newConfig);
}
```

### FirstActivity打开SecondActivity

- FirstActivity打开SecondActivity，这时候FirstActivity生命周期的方法是这样的： onPause –> onSaveInstanceState –> onStop, 这个时候在SecondActivity按返回键，FirstActivity会有以下几种情况：

- 正常情况下会执行： onRestart -> onStart -> onResume

- 当系统由于要回收内存而把 activity 销毁时

    Activity在onPause或者onStop状态下都有可能遇到由于突发事件系统需要回收内存，之后的onDestroy方法便不会再执行，这时候会执行： onCreate –> onStart –> onRestoreInstanceState –> onResume
    

## 保存 Activity 状态

- 当系统为了恢复内存而销毁某项 Activity 时，Activity 对象也会被销毁，因此系统在继续 Activity 时根本无法让其状态保持完好，而是必须在用户返回Activity时重建 Activity 对象。但用户并不知道系统销毁 Activity 后又对其进行了重建，因此他们很可能认为 Activity 状态毫无变化。 在这种情况下，您可以实现另一个回调方法对有关 Activity 状态的信息进行保存，以确保有关 Activity 状态的重要信息得到保留：onSaveInstanceState()

- 系统会先调用 onSaveInstanceState()，然后再使 Activity 变得易于销毁。系统会向该方法传递一个 Bundle，您可以在其中使用 putString() 和 putInt() 等方法以名称-值对形式保存有关 Activity 状态的信息。然后，如果系统终止您的应用进程，并且用户返回您的 Activity，则系统会重建该 Activity，并将 Bundle 同时传递给 onCreate() 和 onRestoreInstanceState()。您可以使用上述任一方法从 Bundle 提取您保存的状态并恢复该 Activity 状态。如果没有状态信息需要恢复，则传递给您的 Bundle 是空值（如果是首次创建该 Activity，就会出现这种情况）

<center>![](http://o99dg8ap9.bkt.clouddn.com/Activity%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E8%AF%A6%E8%A7%A3.png)</center>

- 您只需旋转设备，让屏幕方向发生变化，就能有效地测试您的应用的状态恢复能力。 当屏幕方向变化时，系统会销毁并重建 Activity，以便应用可供新屏幕配置使用的备用资源。 单凭这一理由，您的 Activity 在重建时能否完全恢复其状态就显得非常重要，因为用户在使用应用时经常需要旋转屏幕

## 参考资料

- [ACTIVITY生命周期详解一](http://stormzhang.com/android/2014/09/14/activity-lifecycle1/)

- [ACTIVITY生命周期详解二](http://stormzhang.com/android/2014/09/17/android-lifecycle2/)

- [Android官方文档之Activity](https://developer.android.com/guide/components/activities.html?hl=zh-cn#Creating)