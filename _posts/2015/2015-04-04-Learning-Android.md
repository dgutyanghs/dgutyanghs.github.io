---
title: 安卓编程--视图
layout: default
tags: [读书,编程]
---

# 1.视图的构建 #
安卓的界面是Java生态系统中的新的一套UI框架。但是和AWT，Swing一样，也是单进程的，基于事件的UI交互机制。安卓的视图框架，基于CVM（View-Module-Controller）模型开发。
View就是用户看到的界面，Modle就是在界面上要展示的程序背后的数据，Controller是控制数据如何在View上展示，当数据有更的时候View如何放映，当view中用户有动作的时候，数据如何更新到Modle中去。


## 1. 安卓UI框架的工作机制 ##
   安卓的UI框架通过树的方式来绘制界面，每一个要绘制的主件都是使用先根次序来绘制，也就是说，每一个组件首先绘制它自己，然后要求它的每一个子主件作相同的事情。当这个数被渲染完成之后，也就是说最小的，嵌套最深的主件，也就是这个视图树的也只节点，也就是相对来说最后被绘制的节点，出现在那些距离根近的，最先被渲染的主件的上面.
## 2. 安卓的UI构件（widget）##
  在安卓的UI框架中，所有的类都是android.view.View的子类。在渲染树中位于叶子节点，或者接近叶子节点主件，在应用程序的UI上下文中，完成了大部分的实际的界面绘制的工作，这些类通常叫做构件。
## 3. 容器视图 ##
  可以包含其他主件的主件是一种特殊的主件，叫做容器主件，他们通常是android.view.ViewGroup的子类，而ViewGroup也是View类的子类。容器主件通常很少做渲染工作。相反，他们一般用来安排他们的孩子如何在屏幕上显示，已经当视图的形状、方向等等，发生变化的时候如该在屏幕上显示它的孩子。

一个Activity可以在创建的时候绘制它的视图，也就是在`onCreate(Bundle)`方法中构造出这个Activity需要的视图树，最后让这个根设置为这个Activity的上下文视图即可（使用`setContentView(View view)`。例如：

```java
package com.oreilly.android.intro;
import android.app.Activity;
import android.graphics.Color;
import android.os.Bundle;
import android.view.Gravity;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.EditText;
import android.widget.LinearLayout;

public class AndroidDemo extends Activity {
    private LinearLayout root;
    @Override
    public void onCreate(Bundle state) {
        super.onCreate(state);
        //开始构件界面解构
        LinearLayout.LayoutParams containerParams  = new LinearLayout.LayoutParams(
                               ViewGroup.LayoutParams.FILL_PARENT,
                               ViewGroup.LayoutParams.WRAP_CONTENT,
                               0.0F);
        LinearLayout.LayoutParams widgetParams  = new LinearLayout.LayoutParams(
                               ViewGroup.LayoutParams.FILL_PARENT,
                               ViewGroup.LayoutParams.FILL_PARENT,
                               1.0F);
        root = new LinearLayout(this);
        root.setOrientation(LinearLayout.VERTICAL);
        root.setBackgroundColor(Color.LTGRAY);
        root.setLayoutParams(containerParams);
        LinearLayout ll = new LinearLayout(this);
        ll.setOrientation(LinearLayout.HORIZONTAL);
        ll.setBackgroundColor(Color.GRAY);
        ll.setLayoutParams(containerParams);
        root.addView(ll);
        EditText tb = new EditText(this);
        tb.setText(R.string.defaultLeftText);
        tb.setFocusable(false);
        tb.setLayoutParams(widgetParams);
        ll.addView(tb);
        tb = new EditText(this);
        tb.setText(R.string.defaultRightText);
        tb.setFocusable(false);
        tb.setLayoutParams(widgetParams);
        ll.addView(tb);
        ll = new LinearLayout(this);
        ll.setOrientation(LinearLayout.HORIZONTAL);
        ll.setBackgroundColor(Color.DKGRAY);
        ll.setLayoutParams(containerParams);
        root.addView(ll);
        Button b = new Button(this);
        b.setText(R.string.labelRed);
        b.setTextColor(Color.RED);
        b.setLayoutParams(widgetParams);
        ll.addView(b);
        b = new Button(this);
        b.setText(R.string.labelGreen);
        b.setTextColor(Color.GREEN);
        b.setLayoutParams(widgetParams);
        ll.addView(b);
        //界面构建结束
        setContentView(root);
    }
}
```

从上面的代码中可以看到大部分的代码都是在如何构建视图上，而视图有时最容易变动的部分，所以这种实现界面的方法虽然可行，但是却很难应付软件需求变动带来的问题。视图构建的代码，其中主要的工作有两种：1）设置一个主件自己的属性，2）为一个容器主件添加其他主件。标记语言中，一个节点可以包含其他的节点，也有自己的属性，所以，使用标记语言可以很好的表示视图树的解构。安卓的UI框架，提供了这样的解决方案。例如上面的代码表示的视图，可以使用下面的XML来表示：

```xml
<LinearLayout
      xmlns:android="http://schemas.android.com/apk/res/android"
      android:id="@+id/root"
      android:orientation="vertical"
      android:background="@drawable/lt_gray"
      android:layout_width="fill_parent"
      android:layout_height="wrap_content">
    <LinearLayout
        android:orientation="horizontal"
        android:background="@drawable/gray"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content">
        <EditText
            android:id="@+id/text1"
            android:text="@string/defaultLeftText"
            android:focusable="false"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:layout_weight="1"/>
        <EditText
            android:id="@+id/text2"
            android:text="@string/defaultRightText"
            android:focusable="false"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:layout_weight="1"/>
     </ LinearLayout>
    <LinearLayout
        android:orientation="horizontal"
        android:background="@drawable/dk_gray"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content">
        <Button
            android:id="@+id/button1"
            android:text="@string/labelRed"
            android:textColor="@drawable/red"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:layout_weight="1"/>
        <Button
            android:id="@+id/button2"
            android:text="@string/labelGreen"
            android:textColor="@drawable/green"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:layout_weight="1"/>
    </LinearLayout>
</LinearLayout>
```

这个XML文件作为资源文件，当一个Active创建的时候，通过R类来获取这个资源文件。就可以构建这个XML对应的这个View了。例如：

```java
package com.oreilly.android.intro;
import android.app.Activity;
import android.os.Bundle;
/**
* Android UI demo program
*/
public class AndroidDemo extends Activity {
    private LinearLayout root;
    @Override public void onCreate(Bundle state) {
       super.onCreate(state);
       setContentView(R.layout.main);//把整个的XML对应的View构建好之后设置为这个Activity的上下文视图。
       root = (LinearLayout) findViewById(R.id.root);//这个句子展示了如何中获取一个有XML勾结出来的View中节点
    }
}
```
