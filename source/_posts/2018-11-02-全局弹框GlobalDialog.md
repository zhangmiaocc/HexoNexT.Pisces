---
title: 全局弹框GlobalDialog
date: 2018-11-02 14:54:28
tags:
- blog
- markdown
- Android 
categories:
- Android 
---

全局弹框，比如异地登录提示。思路就是通过非 Activity 的 Context 来启动一个透明 activity， 然后使用这个 activity 来显示一个 dialog。

#### AndroidManifest.xml

```html
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.whohelp.globaldialogdemo">

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".GlobalDialogActivity"
            android:theme="@style/Transparent" />
    </application>

</manifest>
```

#### styles.xml

```html
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <style name="Transparent" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@android:color/transparent</item>
        <item name="android:windowIsTranslucent">true</item>
        <item name="android:windowAnimationStyle">@android:style/Animation</item>
        <item name="android:windowNoTitle">true</item>
    </style>

</resources>
```

#### MainActivity.java

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);


    }

    public void showDialog(View view) {
        GlobalDialogActivity.start(this.getApplicationContext());
    }
}
```
#### GlobalDialogActivity

```java
public class GlobalDialogActivity extends AppCompatActivity {

    public static void start(Context context) {
        Intent starter = new Intent(context, GlobalDialogActivity.class);
        //设置启动方式
        starter.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        context.startActivity(starter);
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_global_dialog);
        //显示dialog
        new AlertDialog.Builder(this)
                .setTitle("全局dialog")
                .setMessage("这是一个全局dialog")
                .setNegativeButton("取消", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        finish();
                    }
                })
                .setPositiveButton("确定", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        finish();
                    }
                })
                .setCancelable(false)
                .show();
    }
}
```

