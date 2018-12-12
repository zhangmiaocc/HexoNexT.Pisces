---
title: Android解决qq分享后返回程序出现的Bug
date: 2018-07-22 23:09:42
tags:
- Android 
- 代码片段
categories:
- Android 
- 代码片段
---

#### 问题：
当我们使用qq分享时，分享成功后选择留在qq,这个时候按home键，回到手机主界面，在点击回到我的app,这个时候会出现界面显示出来了，但是任何事件都不响应，即按钮没反应。

#### 分析：
这个时候回到我们的app时，会发现activity的生命周期只走了 onRestart()---onStart(),走到这里就结束了，onResume()并没有执行，所以界面不响应

这个时候我们又会发现qq分享用到的的一个AssistActivity 它的生命周期：.: --onActivityResult()---onStart()---onResume()

<!--more-->

#### 结论：

至此，我们发现了原因，是这个AssistActivity的问题。

#### 解决：
我们可以在我们程序的onStart()方法判断一下，如果这个AssistActivity处在栈顶就把它清除掉。

```java
@Override
protected void onStart() {
    super.onStart();
    Log.i("---share----", "-----start");
    if(isNeedRestart()){
        Intent intent = new Intent(context, this.getClass());
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP); //清除栈顶的activity
        intent.addFlags(Intent.FLAG_ACTIVITY_NO_ANIMATION);//不显示多余的动画，假装没有重新启动 //记得带需要的参数
        startActivity(intent);
    }

}
private boolean isNeedRestart(){

    ActivityManager am = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
    List<ActivityManager.RunningTaskInfo> tasks = am.getRunningTasks(1);
    if (!tasks.isEmpty()) {
        ComponentName topActivity = tasks.get(0).topActivity;
        ActivityManager.RunningTaskInfo taskInfo = tasks.get(0);
        if (topActivity.getPackageName().equals(context.getPackageName())) {
        // 若当前栈顶界面是AssistActivity，则需要手动关闭
            if (topActivity.getClassName().equals("com.tencent.connect.common.AssistActivity"))
            return true;
        }
    }
    return false;
}
```

 