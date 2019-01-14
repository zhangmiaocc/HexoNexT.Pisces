---
title: MediaMetadataRetriever获取网络/本地视频第一帧图片
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: f0cf7486
date: 2019-01-14 20:29:35
---

**获取视频第一帧图片，这种需求不多，项目中用到了这个功能踩了点坑，很骚。**

![](https://ws3.sinaimg.cn/large/006tNc79ly1fz6e0eyendj305c05cglq.jpg)

<!--more-->

### 获取网络视频

```java
MediaMetadataRetriever mmr = new MediaMetadataRetriever();
//后面这个是传请求Headers，如果有需要可以添加
mmr.setDataSource(url, new HashMap());
```

### 获取本地视频(`setDataSource`中不需要传第二个参数，直接传路径就好)

```java
MediaMetadataRetriever mmr = new MediaMetadataRetriever();
//后面这个是传请求Headers，如果有需要可以添加
mmr.setDataSource(path, new HashMap());
```

### 封装的代码如下：

```java
public class MediaUtils
{
    public static final int MEDIA_TYPE_IMAGE = 1;
    public static final int MEDIA_TYPE_VIDEO = 2;
    public static File file;

    /**
     * Create a file Uri for saving an image or video
     */
    public static Uri getOutputMediaFileUri(Context context, int type)
    {
        Uri uri = null;
        //适配Android N
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N)
        {
            return FileProvider.getUriForFile(context, context.getApplicationContext().getPackageName() + ".provider", getOutputMediaFile(type));
        } else
        {
            return Uri.fromFile(getOutputMediaFile(type));
        }
    }

    /**
     * Create a File for saving an image or video
     */
    public static File getOutputMediaFile(int type)
    {
        // To be safe, you should check that the SDCard is mounted
        // using Environment.getExternalStorageState() before doing this.
        File mediaStorageDir = new File(Environment.getExternalStoragePublicDirectory(
                Environment.DIRECTORY_PICTURES), "image");
        // This location works best if you want the created images to be shared
        // between applications and persist after your app has been uninstalled.
        // Create the storage directory if it does not exist
        if (!mediaStorageDir.exists())
        {
            if (!mediaStorageDir.mkdirs())
            {
                return null;
            }
        }
        // Create a media file name
        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
        File mediaFile;
        if (type == MEDIA_TYPE_IMAGE)
        {
            mediaFile = new File(mediaStorageDir.getPath() + File.separator +
                    "IMG_" + timeStamp + ".jpg");
        } else if (type == MEDIA_TYPE_VIDEO)
        {
            mediaFile = new File(mediaStorageDir.getPath() + File.separator +
                    "VID_" + timeStamp + ".mp4");
        } else
        {
            return null;
        }
        file = mediaFile;
        return mediaFile;
    }

    /**
     * 获取视频的第一帧图片
     */
    public static void getImageForVideo(String videoPath, OnLoadVideoImageListener listener)
    {
        LoadVideoImageTask task = new LoadVideoImageTask(listener);
        task.execute(videoPath);
    }

    public static class LoadVideoImageTask extends AsyncTask<String, Integer, File>
    {
        private OnLoadVideoImageListener listener;

        public LoadVideoImageTask(OnLoadVideoImageListener listener)
        {
            this.listener = listener;
        }

        @Override
        protected File doInBackground(String... params)
        {
            MediaMetadataRetriever mmr = new MediaMetadataRetriever();
            String path = params[0];
            if (path.startsWith("http"))
                //获取网络视频第一帧图片
                mmr.setDataSource(path, new HashMap());
            else
                //本地视频
                mmr.setDataSource(path);
            Bitmap bitmap = mmr.getFrameAtTime();
            //保存图片
            File f = getOutputMediaFile(MEDIA_TYPE_IMAGE);
            if (f.exists())
            {
                f.delete();
            }
            try
            {
                FileOutputStream out = new FileOutputStream(f);
                bitmap.compress(Bitmap.CompressFormat.JPEG, 90, out);
                out.flush();
                out.close();
            } catch (FileNotFoundException e)
            {
                e.printStackTrace();
            } catch (IOException e)
            {
                e.printStackTrace();
            }
            mmr.release();
            return f;
        }

        @Override
        protected void onPostExecute(File file)
        {
            super.onPostExecute(file);
            if (listener != null)
            {
                listener.onLoadImage(file);
            }
        }
    }

    public interface OnLoadVideoImageListener
    {
        void onLoadImage(File file);
    }
}
```

### 因为考虑到处理视频比较耗时，上面代码使用了AsycTask+Callback的方式来实现，先保存到本地后在加载本地的图片。
