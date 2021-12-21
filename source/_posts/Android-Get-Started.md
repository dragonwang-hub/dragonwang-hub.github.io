---
title: Android Get Started
date: 2021-07-30 16:09:59
author: Dragon
toc: true
mathjax: false
categories: Tech Improvement
tags:
    - Android
    - Java
    - Rxjava
    - Layout/ViewModel/LiveData
---

# Android 101 从入门之放弃路线

近来有机会初步学习Android开发的基本要点，虽项目尚不需使用，但正所谓闲来无事，几斤几两的肚中墨水还需增加。
脑子总复现武当开派宗师张三丰对其徒孙所说的那几句话。
> 无忌，你学会了吗？
> 师公，我又忘光了。

因此，本文注重在于整理学习Android过程的基础知识以便日后需要进而勾取当下回忆而不至于又忘光了。
从Android 布局 -> 多线程 -> 数据存储 -> 网络 -> 测试 路线徐徐展开。

当然
工欲善其事，必先利其器。

- [Android Studio](https://developer.android.com/studio)
- [Android Debug Bridge](https://developer.android.com/studio/command-line/adb)
- [AVD manager](https://developer.android.com/studio/run/managing-avds)

若需查看Android-HelloWorld Demo 代码，请 Star [Demo](https://github.com/dragonwang-hub/android-helloworld)地址。

## Android 布局
在学习Android中，还没有接触类似于Web开发的前后端分离开发形式。因此，仍需自己Code 相关前端页面，若有前端微微基础，倒也不算困难。

### 常用四种布局

- LinearLayout [线性布局](https://www.runoob.com/w3cnote/android-tutorial-linearlayout.html)
- RelativeLayout 相对布局： 相对布局 (RelativeLayout) 以 父容器 或者 兄弟组件 参考+margin +padding 来设置组件的显示位置
- FrameLayout  帧布局：屏幕上开辟出一块空白的区域,当往里面添加控件的时候,会默认把他们放到这块区域的左上角。
- ConstraintLayout [约束布局](https://developer.android.com/training/constraint-layout)：所有的视图均根据同级视图与父布局之间的关系进行布局

[文章] [常用UI布局和控件](https://blog.csdn.net/qq_40587575/article/details/107484260)
[文章] [ScrollView](https://guides.codepath.com/android/Working-with-the-ScrollView)
[文章] [启动另一个 Activity](https://developer.android.com/training/basics/firstapp/starting-activity)
[文章] [How to start new Activity on click button in Android?](https://www.tutorialspoint.com/how-to-start-new-activity-on-click-button-in-android)
[文章] [Hide ActionBar on certain activities](https://stackoverflow.com/questions/19545370/android-how-to-hide-actionbar-on-certain-activities)
[文章] [No statusbar](https://developer.android.com/training/system-ui/status)
[文章] [How to create a rounded corners Button in Android](https://android--code.blogspot.com/2015/01/android-rounded-corners-button.html)
[文章] [Edittext change border color with shape.xml](https://stackoverflow.com/questions/27709238/edittext-change-border-color-with-shape-xml/27709426)
[文章] [Android Background Drawable Not Working in Button Since Android Studio 4.1](https://stackoverflow.com/questions/64722733/android-background-drawable-not-working-in-button-since-android-studio-4-1)

### Acitvity 和 Fragment

#### Acitvity

Activity 是Android[四大组件](https://juejin.cn/post/6844903977100771335)之一
Activity 中所有操作都与用户密切相关，是一个负责与**用户交互**的组件。

**每个Activity 都需要在AndroidManifest.xml声明。**

两种[启动模式](https://blog.csdn.net/Blue3Red1/article/details/108903751)：

- 显式启动：直接指定要跳转的Activity类名，不用过滤，效率高，适用于同一个应用中的不同Activity跳转。
- 隐式启动：需要过滤，相对耗时，但可以找到所有之匹配的应用。适用于不同应用之间的Activity跳转。

[文章] [启动一个Activity](https://developer.android.com/guide/components/activities/activity-lifecycle?hl=zh-cn#tba)
[文章] [Activity生命周期](https://developer.android.com/guide/components/activities/activity-lifecycle?hl=zh-cn)
[文章] [Activity状态](https://developer.android.com/guide/components/activities/state-changes?hl=zh-cn)
[文章] [Intent和Intent Filter](https://developer.android.com/guide/components/intents-filters?hl=zh-cn#top_of_page)

#### Fragment
[Fragment](https://guides.codepath.com/android/creating-and-using-fragments#embedding-a-fragment-in-an-activity)必须依附于Activity或者另一个Fragment。
Fragment 允许您将界面划分为离散的区块，从而将模块化和可重用性引入 Activity 的界面。Activity 是围绕应用的界面放置全局元素（如抽屉式导航栏）的理想位置。相反，Fragment 更适合定义和管理单个屏幕或部分屏幕的界面。

1. 更像一个轻量级的Activity。更具模块化。

一般用于一个页面无法显示全部内容或在不同尺寸屏幕用不同布局显示相同内容

- 加入backStack，被切换时会处于STOP状态，不会Destory，并且点击返回键还可以回弹出原来Fragment。
- onCreateView()内增加一些方法调用。而不是onCreate()

2. FragmentManager 与 Fragment 的关系

- 负责决定fragment应处于的状态，并进行状态更改。
- 负责attach或detach一个fragment到其宿主。

当Fragment被添加至FragmentManager，会调用onAttach方法
当Fragment被移除至FragmentManager，会调用onDetach方法
onAttach()/onDetach is always called before any Lifecycle state changes.

**生命状态不可能比宿主更高。启动晚于父，销毁早于父**

### RecyclerView
[RecyclerView](https://guides.codepath.com/android/using-the-recyclerview) 会回收这些单个的元素。当列表项滚动出屏幕时，RecyclerView 不会销毁其视图。相反，RecyclerView 会对屏幕上滚动的新列表项重用该视图。这种重用可以显著提高性能，改善应用响应能力并降低功耗。

#### 三种布局
1. LinearLayoutManager 将各个项排列在一维列表中。
2. GridLayoutManager 将所有项排列在二维网格中
3. StaggeredGridLayoutManager 同一行或同一列中的列表项可能会错落不齐。


#### Code Step
1. 构造Activity 存放一个recycler view控件。（包括class,xml）
2. 声明initRecyclerView(), 实现通过id获取recycler view控件，并对控件设置setAdapter()和setLayoutManager()
3. 根据第二步，需要自己实现一个Adapter。  
    3.1. 包括继承RecyclerView.ViewHolder实现一个ViewHolder。ViewHold存放每一个元素的表现形式，比如这个元素有一个textview和一个button,就可以存放在viewholder。  
    3.2. implement Adapter的三个方法。
    - onCreateViewHolder中需要使用一个item的xml布局文件，内部存放的是一个item的元素。此处注意item内容的高度和宽度，不要是一屏。
    - onBindViewHolder是用来设置每个viewholder中的数据，根据position确定。
    - getItemCount 返回当前元素列表的长度
    - 若有必要，还需在adapter类内实现一个setData的方法，用来初始化数据。
4. 返回之Activity class, 在initRecyclerView内构造自定义的adapter，并赋予数据。


## Android 多线程
对于Android而言，页面就属于主线程，但在应用后台，可能需要其他操作，例如读写文件或者下载文件数据等等，若这些操作在主线程，将会阻塞页面展示效果造成程序不响应等问题，所以在Android中多线程是必须的。

[理解Android多线程](https://blog.csdn.net/u011240877/article/details/53142177)
[多线程性能](https://blog.csdn.net/u011240877/article/details/53142177)
[AsyncTask示例](https://www.cnblogs.com/BeyondAnyTime/articles/2989239.html) Android 11已弃用
[HandlerThread](https://blog.csdn.net/weixin_41101173/article/details/79687313 ; https://www.cnblogs.com/demodashi/p/8481574.html)

虽然Android SDK 提供了一些方法用于实现UI线程与后台工作线程的分离，但还有一种更简单的链式调用Library [RxJava](https://gank.io/post/560e15be2dca930e00da1083)（**五星推荐**）也可以更好实现多线程操作。

[文章] [RxJava 关于 Disposable](https://www.jianshu.com/p/2a882604bbe8)
[文章] [RxJava 文档](http://reactivex.io/documentation/operators.html)



## 数据存储

本地数据存储的几种方式

- SP： [SharePerference](https://developer.android.com/training/data-storage/shared-preferences) 保存的相对较小键值对集合，每个 `SharedPreferences` 文件均由框架进行管理，可以是私有文件，也可以是共享文件。
- assert 目录：[Android的一种特殊目录](https://www.jianshu.com/p/5974fcf88170)，用于放置APP所需的固定文件，且该文件被打包到APK中时，不会被编码到二进制文件。
- raw 资源：Android一种资源文件，在res目录下。可通过API进行调用，每个raw下的文件都有id。`R.raw.id`[获取当前指定文件](https://www.programcreek.com/java-api-examples/?class=android.content.res.Resources&method=openRawResource)。

**Room 数据库**

Android 使用 Sqlite 数据库，[Room](https://developer.android.com/training/data-storage/room) 是对 Sqlite 的封装。以便在充分利用 SQLite 的强大功能的同时，能够流畅地访问数据库。

目的：最常见的用例是缓存相关数据。这样，当设备无法访问网络时，用户仍可在离线状态下浏览相应内容。设备重新连接到网络后，用户发起的所有内容更改都会同步到服务器。

难点：涉及存取数据的操作，需要定义Dao / Entity / Repository，还需考虑Entity 外键映射关系等等。[推荐阅读](https://juejin.cn/post/6844903903020974093)

配置 Room + RxJava 时注意根据 RxJava 的版本配置 
```groovy
androidx.room:room-rxjava3:$room_version 
或者 
androidx.room:room-rxjava2:$room_version
```

[文章] [使用 Room 将数据保存到本地数据库](https://developer.android.com/training/data-storage/room)
[文章] [Room + RxJava](https://medium.com/androiddevelopers/room-rxjava-acb0cd4f3757)
[文章] [Room 依赖配置](https://developer.android.com/jetpack/androidx/releases/room)



## 网络

在应用中，权限设置我们经常能看到，实际上，安卓分为[基本权限和危险权限](https://developer.android.com/guide/topics/permissions/overview)。而网络属于基本权限中一种，但若需要此权限，仍需要在Manifest.xml[声明应用权限](https://developer.android.com/training/permissions/declaring?hl=zh-cn)。
Android中可以使用两种框架来获取网络数据，这里主要使用[Okhttp Library](https://square.github.io/okhttp/)

[文章] [Using RxJava and Okhttp](https://stackoverflow.com/questions/32687921/using-rxjava-and-okhttp)

实际上，在本次101学习过程中，没有过多接触[Android架构](https://developer.android.com/jetpack/guide)相关的知识，不像三层架构或者DDD架构模式有所了解，所以Class文件职责划分并不清晰。



#### ViewModel & LiveData

如果系统销毁或重新创建界面控制器，则存储在其中的任何瞬态界面相关数据都会丢失。
ViewModel 类旨在以注重生命周期的方式存储和管理界面相关的数据。ViewModel 类让数据可在发生屏幕旋转等配置更改后继续留存。
LiveData 是一种可观察的数据存储器类。与常规的可观察类不同，LiveData 具有生命周期感知能力，意指它遵循其他应用组件（如 Activity、Fragment 或 Service）的生命周期。这种感知能力可确保 LiveData 仅更新处于活跃生命周期状态的应用组件观察者。

[文章] [LiveData 概览](https://developer.android.com/topic/libraries/architecture/livedata)
[文章] [ViewModel 概览](https://developer.android.com/topic/libraries/architecture/viewmodel)

使用这两个Class可以使数据及时更新和保存，更利于页面数据的实时展示。



##  Test

测试的目的，并不用多说。

Android 测试分为单元测试和UI页面测试。

[文章] [Android测试](https://developer.android.com/studio/test)
[文章] [构建本地单元测试](https://developer.android.com/training/testing/unit-testing/local-unit-tests)
[文章] [测试单个应用界面](https://developer.android.com/training/testing/ui-testing/espresso-testing)



## 扩展阅读

[文章] [SDK Version](https://blog.csdn.net/qq_23062979/article/details/81294550)
[文章] [Anroid Gradle Build](https://www.jianshu.com/p/c11862136abf)
[文章] [Keystore](https://developer.android.com/training/articles/keystore)
[文章] [gson](https://github.com/google/gson/blob/master/UserGuide.md)
[文章] [图片加载库 Glide](https://github.com/bumptech/glide)
[文章] [连接到网络](https://developer.android.com/training/basics/network-ops/connecting)
[文章] [Android基础之自定义Application](https://www.jianshu.com/p/98324e5d67ae)
[文章] [Toast](https://developer.android.com/guide/topics/ui/notifiers/toasts)



### Q & A

在个人学习过程，也遇到不少难于理解的问题，现将其整理如下：

1. Login页面要考虑各种高度的手机适配问题，有的手机高度不够，可能下面的按钮就看不到了，所以建议在 ConstraintLayout 外部套上 androidx.core.widget.NestedScrollView
2. 如果有用到图片资源，需要创建各分辨率的 drawable 目录，要将图片资源放入 drawable 中而不是 mipmap 中.mipmap放启动图标比较合适。
3. 如果不配置 keystore， Android studio 会自动配置一个 debug keystore给你的apk，这个debug keystore只能用一年，一年后这个apk就不能再被安装了。
4. 设置button透明
```
android:background="@android:color/transparent"
```
5. 4.1 以上隐藏actionbar和statusbar
```
View decorView = getWindow().getDecorView();
// Hide the status bar.
int uiOptions = View.SYSTEM_UI_FLAG_FULLSCREEN;
decorView.setSystemUiVisibility(uiOptions);
// Remember that you shrould never show the action bar if the
// status bar is hidden, so hide that too if necessary.
ActionBar actionBar = getActionBar();
if (actionBar!=null)actionBar.hide();
```
若发现代码中无法获取actionbar，可在manifest文件配置
```
android:theme="@style/Theme.AppCompat.Light.NoActionBar"
```
可应用在全局或指定某个activity内。
若是继承AppCompatActivity，则只需加入一条语句：
```
getSupportActionBar().hide();
```
6. FrameLayout布局的特点是：所有放在布局里的视图组件，都按照层次堆叠在屏幕的左上角，后面的视图组件覆盖前面的。 当然，组件本身是可以控制自己的内部布局的。 一种常见的场景是可以在FrameLayout上放多个图片视图，来实现动画。

7. Android 项目 数据文件存放位置在 asset，与java/res同层级
https://blog.csdn.net/ww897532167/article/details/70875334

8. Textview设置多行
- Android:singleLine=”true”，单行：
- android:lines=”2”
android:ellipsize=”end”

这样就可以让TextView最多显示2行，如果要显示指定的其它行数，只要修改上面的android:lines属性即可，设置了显示两行，如果内容用一行就可以全部显示了，这时候TextView会占用两行的高度空间。

- 
android:maxLines=”2”
android:ellipsize=”end”
显示两行，高度自适应

9. 自定义实现recyclerview的headerview和footerview
https://www.jianshu.com/p/e9dfb7167f87
https://www.journaldev.com/12372/android-recyclerview-example
10. 判断当前recyclerview的item是最后一个position
https://blog.csdn.net/salute_li/article/details/52118504
11. collection Type 示例 （序列化与反序列化）
Type listUserType = new TypeToken<List<User>>() { }.getType();
12. Activity 和 Context 的区别与联系：
https://blog.csdn.net/weixin_41101173/article/details/79882846
13. new ViewModelProvider(this)中this报错
    https://blog.csdn.net/zsq8187/article/details/109087798
14. ViewModel & AndroidViewModel 适用
    如果 ViewModel 需要 Application 上下文（例如，为了查找系统服务），它可以扩展 AndroidViewModel 类并设置用于接收 Application 的构造函数，因为 Application 类会扩展 Context。
15. Test Error 
    Error: "Method ... not mocked"
    如果您运行测试，从Android SDK调用API，你不会使用mock，可能会收到一个错误，说这种方法没有被模拟。 这是因为用于运行单元测试的android.jar文件不包含任何当前代码（这些API仅由设备上的Android系统映像提供）。

相反，所有方法默认都会抛出异常。 这是为了确保你的单元测试你的代码，而不是依赖于Android平台的任何特定的行为（你没有明确地mock，如Mockito）。

如果抛出的异常说你的测试有问题，可以更改行为，以便通过在项目的顶级build.gradle文件中添加以下配置来返回null或零：

```
android {
  ...
  testOptions {
    unitTests.returnDefaultValues = true
  }
}
```

注意：将returnDefaultValues属性设置为true应该小心。 null / zero返回值可以在测试中引入回归，这些回调很难调试，并且可能允许失败的测试通过。只能用它作为最后的手段。



**路漫漫其修远兮，吾将上下而求索。**
