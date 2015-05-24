---
layout: post
title: "How to Use Android Toggle Button"
date: 2014-11-08 14:57:11 +0800
comments: true
categories: Android
keywords: android, toggle button
description: 
---
I am taking an online android course on Coursera. The first assignment is to build a app which could properly present text and image.

Apart from what I learned from the first three lectures, I also tried to add something interesting. I wanted to show two photos in the same place and allowed users to switching between them. So I chosed Toggle Button. 

However, I was not quite followed the instructions on Android Developer Online [Doc][1]. Besides, some problems like 'variables are not resolved' occurred. 

After searching for the solution on [Stackoverflow][3] and finding [example][2] apps, I finally managed to achieve what I want.

<!-- more -->

First a toggle button in layout.

```java
<ToggleButton
  android:id="@+id/toggleButton1"
  ...
  android:textOn=""
  android:textOff="" />
```

Add ToggleButton function in MainActivity.java.

```java
import android.widget.CompoundButton;  
import android.widget.CompoundButton.OnCheckedChangeListener;  
import android.widget.LinearLayout;
import android.widget.ImageView;
import android.widget.ToggleButton; 

public class MainActivity extends ActionBarActivity {
	private ImageView imageView;     
  private ToggleButton toggleButton; 
	
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		imageView = (ImageView) findViewById(R.id.imageView1); 
		toggleButton = (ToggleButton) findViewById(R.id.toggleButton1);   
        toggleButton.setOnCheckedChangeListener(new OnCheckedChangeListener() { 
        	public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
        		toggleButton.setChecked(isChecked);
                imageView.setImageResource(isChecked?R.drawable.hznight:R.drawable.hzday);
        	}
        });
	}
```
Generate a new file called 'ic_toggle' in drawable folder and customize toggle button in layout.

```xml
<?xml version="1.0" encoding="utf-8"?> 
<selector xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/night" android:state_checked="true"/>
  <item android:drawable="@drawable/day" android:state_checked="false"/>
</selector>
```

```
<ToggleButton
  ...
  android:background="@drawable/ic_toggle" 
  android:textOn=""
  android:textOff="" />
```

[1]: http://developer.android.com/guide/topics/ui/controls/togglebutton.html) 
[2]: http://liangruijun.blog.51cto.com/3061169/655014
[3]: http://stackoverflow.com/questions/18335239/android-toggle-button-custom-look

