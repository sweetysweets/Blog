---
title: 记一次坑爹的安卓调试经历>.<
date: 2019-01-02 17:04:40
tags:
- android
---

# 弹出键盘的时候Toolbar被拉伸或者移出屏幕的问题

今天遇到了一个问题，如下图所示：

<!--more-->

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/android-bug/test.gif)

### 问题原因

#### fitsSystemWindow属性

根据官方对fitsSystemWindows属性[（链接）](https://link.jianshu.com?t=https://developer.android.com/reference/android/view/View.html#fitSystemWindows(android.graphics.Rect))的描述，当View的fitsSystemWindows设置为true的时候，系统会自动为该View设置相应的padding以适应键盘、状态栏、导航栏等系统窗口，这就可以解释为什么给Toolbar设置fitsSystemWindows之后Toolbar会自动加上paddingTop以适应状态栏，如果没有加上fitsSystemWindows=true，Toolbar则会有部分被状态栏覆盖。

#### EditText问题分析

在其他页面上没有遇到这个问题，是因为这些页面没有需要自适应键盘窗口的问题，但这个页面由于edittext的存在，键盘会弹出，此时系统会为topbar设置相应的padding以适应键盘，导致这样的问题。

### 问题解决

#### 第一次尝试

一开始的想法是删除fitsSystemWindow属性，手动设置topbar的高度，尝试无果，卒233

原因： 我的topbar使用的是QMUI组件，无法支持自定义的高度设置，如果设置的高度不合适，会导致title和返回键的垂直位置非常奇怪=。=

只能想其他的办法。。

#### 第二次尝试

想了一下，如果我不能先删除fitsSystemWindow属性，那么是不是可以在点击edittext的时候再设置fitsSystemWindow为false，这样就不会导致状态栏高度变化，也不会出现padding变高的情况。emmm，尝试了之后发现第一次点击还是会变高，但之后的点击是可以的！

#### 第三次尝试

后面又想，既然这样可以实现，我应该可以在activity加载完成后调用设置fitsSystemWindow为false的方法，应该就可以实现我的要求，后面查了一下activity的生命周期，看到onStart是在onCreate之后执行，所以尝试覆写了onStart方法，试图解决=。 =

```
@Override
    public void onStart() {
        super.onStart();
        mTopBar.setFitsSystemWindows(false);
    }
```

emmmm，onStart虽然在activity加载完成后执行，但还是在显示之前执行的，所以并没有卵用。

#### 最终

后来尝试了onResume。。也不行。。然后意外的找到了这个方法

```
@Override
public void onWindowFocusChanged(boolean hasFocus) {
    // TODO Auto-generated method stub
    super.onWindowFocusChanged(hasFocus);
}
```

Called when the current Window of the activity gains or loses focus.

这个方法在一个Activity完全加载完毕后，会执行（也就是它获得焦点），感觉有戏，试了一下，果然可以！折腾我一下午终于搞定了>.<

#### 结果展示

添加代码：

```
 @Override
    public void onWindowFocusChanged(boolean hasFocus) {
        // TODO Auto-generated method stub
        super.onWindowFocusChanged(hasFocus);
        Log.e(TAG,String.valueOf(hasFocus));
        if(hasFocus){
            mTopBar.setFitsSystemWindows(false);
        }else{
            mTopBar.setFitsSystemWindows(true);
        }
    }
```

结果截图：

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/android-bug/%E4%BF%AE%E6%94%B9%E5%90%8E.gif)

### 总结经验

用开源控件要谨慎啊。。想要啥效果都不好改，限制太多，还不如自己定制。。虽然麻烦点，但是总归是可控的2333

再也不看见好看的开源控件就恨不得全拿过用了=。 =

### 参考

 https://www.jianshu.com/p/1b22a1d2a7b8

https://blog.csdn.net/wangjia55/article/details/23268341







