---
title: 录音SoundRecording
date: 2018-08-09 18:11:15
tags:
- blog
- markdown
- Android
- 录音 
categories:
- Android
- 代码片段 
---

#### 效果图

| 首页 | 录音 | 播放 |
| ---- | ---- | ---- |
|  ![](https://ws2.sinaimg.cn/large/0069RVTdly1fu3m7su3oij30u01hcaai.jpg)    |  ![](https://ws3.sinaimg.cn/large/0069RVTdly1fu3m7ym5bnj30u01hcq3v.jpg)    |    ![](https://ws1.sinaimg.cn/large/0069RVTdly1fu3m82k571j30u01hc0td.jpg)  |


#### 实现录音的 Service

这个类可以说是这个包的核心了，如果理解了这个 `Service`，录音这一块基本就没什么问题了。

录音主要是利用 `MediaRecoder` 这个类，进行声音的记录，接下来我们一起来看看具体的实现。

<!--more-->

```java
public class RecordingService extends Service {

    private static final String LOG_TAG = "RecordingService";

    private String mFileName = null;
    private String mFilePath = null;

    private MediaRecorder mRecorder = null;

    private long mStartingTimeMillis = 0;
    private long mElapsedMillis = 0;
    private TimerTask mIncrementTimerTask = null;

    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }

    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        startRecording();
        return START_STICKY;
    }

    @Override
    public void onDestroy() {
        if (mRecorder != null) {
            stopRecording();
        }
        super.onDestroy();
    }

    public void startRecording() {
        setFileNameAndPath();

        mRecorder = new MediaRecorder();
        mRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
        mRecorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);
        mRecorder.setOutputFile(mFilePath);
        mRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.AAC);
        mRecorder.setAudioChannels(1);
        mRecorder.setAudioSamplingRate(44100);
        mRecorder.setAudioEncodingBitRate(192000);

        try {
            mRecorder.prepare();
            mRecorder.start();
            mStartingTimeMillis = System.currentTimeMillis();
        } catch (IOException e) {
            Log.e(LOG_TAG, "prepare() failed");
        }
    }

    public void setFileNameAndPath() {
        int count = 0;
        File f;

        do {
            count++;
            mFileName = getString(R.string.default_file_name)
                    + "_" + (System.currentTimeMillis()) + ".mp4";
            mFilePath = Environment.getExternalStorageDirectory().getAbsolutePath();
            mFilePath += "/SoundRecorder/" + mFileName;
            f = new File(mFilePath);
        } while (f.exists() && !f.isDirectory());
    }

    public void stopRecording() {
        mRecorder.stop();
        mElapsedMillis = (System.currentTimeMillis() - mStartingTimeMillis);
        mRecorder.release();

        getSharedPreferences("sp_name_audio", MODE_PRIVATE)
                .edit()
                .putString("audio_path", mFilePath)
                .putLong("elpased", mElapsedMillis)
                .apply();
        if (mIncrementTimerTask != null) {
            mIncrementTimerTask.cancel();
            mIncrementTimerTask = null;
        }

        mRecorder = null;
    }

}
```

可以看到在 `onStartCommand()` 里面有一个 `startRecording()` 方法，在外部启动这个 `RecordingService` 的时候，便会调用这个 `startRecording()` 方法开始录音。

在 `startRecording()` 方法中先调用了 `setFileNameAndPath` 方法，初始化了录音文件的名字和保存的路径，为了让每个录音文件都有唯一的名字，我调用 `System.currentMillis()` 拼接到录音文件的名字里面。

```java
public void setFileNameAndPath() {
    int count = 0;
    File f;

    do {
        count++;
        mFileName = getString(R.string.default_file_name)
                + "_" + (System.currentTimeMillis()) + ".mp4";
        mFilePath = Environment.getExternalStorageDirectory().getAbsolutePath();
        mFilePath += "/SoundRecorder/" + mFileName;
        f = new File(mFilePath);
    } while (f.exists() && !f.isDirectory());
}
```

设置好了文件的名字和保存路径之后，对 `mRecorder` 进行一系列参数的设置，这个`mRecorder` 是 `MediaRecorder` 的一个实例，专门用于录音的存储。

```java
public void startRecording() {
    setFileNameAndPath();

    mRecorder = new MediaRecorder();
    mRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
    mRecorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);
    mRecorder.setOutputFile(mFilePath);
    mRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.AAC);
    mRecorder.setAudioChannels(1);
    mRecorder.setAudioSamplingRate(44100);
    mRecorder.setAudioEncodingBitRate(192000);

    try {
        mRecorder.prepare();
        mRecorder.start();
        mStartingTimeMillis = System.currentTimeMillis();
    } catch (IOException e) {
        Log.e(LOG_TAG, "prepare() failed");
    }
}
```

设置好参数之后，启动 `mRecorder` 开始录音，可以看到启动 `mRecorder` 开始录音后，我还将当前的时间赋值给 `mStartingTimeMills`，这里主要是为了记录录音的时长，等到录音结束后再获取一次当前的时间，然后将两个时间进行相减，就能得到录音的具体时长了。

等到录音结束，停止服务后，便会回调 `RecordingService` 的 `onDestroy()` 方法，这时候便会调用 `stopRecording()` 方法，关闭 `mRecorder`，并用 `SharedPreferences` 保存录音文件的信息，最后将 `mRecorder` 置空，防止内存泄露。

```java
public void stopRecording() {
    mRecorder.stop();
    mElapsedMillis = (System.currentTimeMillis() - mStartingTimeMillis);
    mRecorder.release();

    getSharedPreferences("sp_name_audio", MODE_PRIVATE)
            .edit()
            .putString("audio_path", mFilePath)
            .putLong("elpased", mElapsedMillis)
            .apply();
    if (mIncrementTimerTask != null) {
        mIncrementTimerTask.cancel();
        mIncrementTimerTask = null;
    }

    mRecorder = null;
}
```

#### 显示录音界面的 RecordAudioDialogFragment

```java
public class RecordAudioDialogFragment extends DialogFragment {

    private static final String TAG = "RecordAudioDialogFragme";

    private int mRecordPromptCount = 0;

    private boolean mStartRecording = true;
    private boolean mPauseRecording = true;

    long timeWhenPaused = 0;

    private FloatingActionButton mFabRecord;
    private Chronometer mChronometerTime;
    private ImageView mIvClose;

    private OnAudioCancelListener mListener;

    public static RecordAudioDialogFragment newInstance() {
        RecordAudioDialogFragment dialogFragment = new RecordAudioDialogFragment();
        Bundle bundle = new Bundle();
        dialogFragment.setArguments(bundle);
        return dialogFragment;
    }

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }

    @Override
    public void onActivityCreated(Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
    }

    @NonNull
    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
        Dialog dialog = super.onCreateDialog(savedInstanceState);
        final AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
        View view = getActivity().getLayoutInflater().inflate(R.layout.fragment_record_audio, null);
        initView(view);

        mFabRecord.setColorNormal(getResources().getColor(R.color.colorAccent));
        mFabRecord.setColorPressed(getResources().getColor(R.color.colorAccent));

        mFabRecord.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (ContextCompat.checkSelfPermission(getActivity(), Manifest.permission.WRITE_EXTERNAL_STORAGE)
                        != PackageManager.PERMISSION_GRANTED) {
                    ActivityCompat.requestPermissions(getActivity()
                            , new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.RECORD_AUDIO}, 1);
                } else {
                    onRecord(mStartRecording);
                    mStartRecording = !mStartRecording;
                }

            }
        });

        mIvClose.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mListener.onCancel();
            }
        });

        builder.setCancelable(false);
        builder.setView(view);
        return builder.create();
    }

    private void initView(View view) {
        mChronometerTime = (Chronometer) view.findViewById(R.id.record_audio_chronometer_time);
        mFabRecord = (FloatingActionButton) view.findViewById(R.id.record_audio_fab_record);
        mIvClose = (ImageView) view.findViewById(R.id.record_audio_iv_close);
    }

    Intent intent;

    private void onRecord(boolean start) {

        intent = new Intent(getActivity(), RecordingService.class);

        if (start) {
            // start recording
            mFabRecord.setImageResource(R.drawable.ic_media_stop);
            //mPauseButton.setVisibility(View.VISIBLE);
            Toast.makeText(getActivity(), "开始录音...", Toast.LENGTH_SHORT).show();
            File folder = new File(Environment.getExternalStorageDirectory() + "/SoundRecorder");
            if (!folder.exists()) {
                //folder /SoundRecorder doesn't exist, create the folder
                folder.mkdir();
            }

            //start Chronometer
            mChronometerTime.setBase(SystemClock.elapsedRealtime());
            mChronometerTime.start();

            //start RecordingService
            getActivity().startService(intent);
            //keep screen on while recording
//            getActivity().getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);


        } else {
            //stop recording
            mFabRecord.setImageResource(R.drawable.ic_mic_white_36dp);
            //mPauseButton.setVisibility(View.GONE);
            mChronometerTime.stop();
            timeWhenPaused = 0;
            Toast.makeText(getActivity(), "录音结束...", Toast.LENGTH_SHORT).show();

            getActivity().stopService(intent);
            //allow the screen to turn off again once recording is finished
            getActivity().getWindow().clearFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
        }
    }

    @Override
    public void onDestroyView() {
        if (!mStartRecording) {
            //stop recording
            mFabRecord.setImageResource(R.drawable.ic_mic_white_36dp);
            //mPauseButton.setVisibility(View.GONE);
            mChronometerTime.stop();
            timeWhenPaused = 0;
            Toast.makeText(getActivity(), "录音结束...", Toast.LENGTH_SHORT).show();

            getActivity().stopService(intent);
            //allow the screen to turn off again once recording is finished
            getActivity().getWindow().clearFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
        }
        super.onDestroyView();
    }

    public void setOnCancelListener(OnAudioCancelListener listener) {
        this.mListener = listener;
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        switch (requestCode) {
            case 1:
                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED
                        && grantResults[1] == PackageManager.PERMISSION_GRANTED) {
                    onRecord(mStartRecording);
                }
                break;
        }
    }

    public interface OnAudioCancelListener {
        void onCancel();
    }
}
```

可以看到在 `RecordAudioDialogFragment` 有一个 `newInstance(int maxTime)` 的静态方法供外部调用，如果想设置录音的最大时长，直接传参数进去就行了。

这个对话框的重点部分就是在 `onCreateDialog()`中，我们先加载了我们自定义的对话框的布局，当点击录音的按钮的时候，先进行相关权限的申请，录音权限 `android.permission.RECORD_AUDIO` 

```java
public Dialog onCreateDialog(Bundle savedInstanceState) {
    Dialog dialog = super.onCreateDialog(savedInstanceState);
    final AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
    View view = getActivity().getLayoutInflater().inflate(R.layout.fragment_record_audio, null);
    initView(view);

    mFabRecord.setColorNormal(getResources().getColor(R.color.colorAccent));
    mFabRecord.setColorPressed(getResources().getColor(R.color.colorAccent));

    mFabRecord.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            if (ContextCompat.checkSelfPermission(getActivity(), Manifest.permission.WRITE_EXTERNAL_STORAGE)
                    != PackageManager.PERMISSION_GRANTED) {
                ActivityCompat.requestPermissions(getActivity()
                        , new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.RECORD_AUDIO}, 1);
            } else {
                onRecord(mStartRecording);
                mStartRecording = !mStartRecording;
            }

        }
    });

    mIvClose.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            mListener.onCancel();
        }
    });

    builder.setCancelable(false);
    builder.setView(view);
    return builder.create();
}
```

申请好权限之后便会调用 `onRecord()` 这个方法，然后将 `boolean mStartRecording` 进行反转，这样就不用写难看的 `if else` 了，直接改变 `mStartRecording` 的值，然后在 `onRecord()` 里面进行处理。

```java
private void onRecord(boolean start) {

        intent = new Intent(getActivity(), RecordingService.class);

        if (start) {
            // start recording
            mFabRecord.setImageResource(R.drawable.ic_media_stop);
            //mPauseButton.setVisibility(View.VISIBLE);
            Toast.makeText(getActivity(), "开始录音...", Toast.LENGTH_SHORT).show();
            File folder = new File(Environment.getExternalStorageDirectory() + "/SoundRecorder");
            if (!folder.exists()) {
                //folder /SoundRecorder doesn't exist, create the folder
                folder.mkdir();
            }

            //start Chronometer
            mChronometerTime.setBase(SystemClock.elapsedRealtime());
            mChronometerTime.start();

            //start RecordingService
            getActivity().startService(intent);
            //keep screen on while recording
//            getActivity().getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);


        } else {
            //stop recording
            mFabRecord.setImageResource(R.drawable.ic_mic_white_36dp);
            //mPauseButton.setVisibility(View.GONE);
            mChronometerTime.stop();
            timeWhenPaused = 0;
            Toast.makeText(getActivity(), "录音结束...", Toast.LENGTH_SHORT).show();

            getActivity().stopService(intent);
            //allow the screen to turn off again once recording is finished
            getActivity().getWindow().clearFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
        }
    }
```

创建了保存录音文件的文件夹，然后根据 `mStartRecording` 的值进行 `RecordingService` 的启动和关闭罢了。在启动时还顺便开始了 `mChronometer` 的计时显示，这是一个 `Android` 原生的显示计时的一个控件。

#### 播放录音的 PlaybackDialogFragment

外部调用这个对话框的时候，只需要传入一个包含录音文件信息的 `RecordingItem`，因为包含的信息比较多，所以最好将 `RecordingItem` 进行序列化。

```java
public static PlaybackDialogFragment newInstance(RecordingItem item) {
    PlaybackDialogFragment f = new PlaybackDialogFragment();
    Bundle b = new Bundle();
    b.putParcelable(ARG_ITEM, item);
    f.setArguments(b);
    return f;
}
```

来看看 `onCreateDialog()` 方法，在加载了布局之后，给 `mSeekBar` 设置监听，`mSeekBar` 是一个显示进度条的控件，当开始播放录音时候，将录音文件的时长，设置进 `mSeekBar` 里面，播放录音的同时，运行 `mSeekBar`，通过监听 `mSeekBar` 的进度，刷新显示的播放进度。

```java
public Dialog onCreateDialog(Bundle savedInstanceState) {

    Dialog dialog = super.onCreateDialog(savedInstanceState);

    AlertDialog.Builder builder = new AlertDialog.Builder(getActivity());
    View view = getActivity().getLayoutInflater().inflate(R.layout.fragment_media_playback, null);

    mFileNameTextView = (TextView) view.findViewById(R.id.file_name_text_view);
    mFileLengthTextView = (TextView) view.findViewById(R.id.file_length_text_view);
    mCurrentProgressTextView = (TextView) view.findViewById(R.id.current_progress_text_view);

    mSeekBar = (SeekBar) view.findViewById(R.id.seekbar);
    ColorFilter filter = new LightingColorFilter
            (getResources().getColor(R.color.green), getResources().getColor(R.color.green));
    mSeekBar.getProgressDrawable().setColorFilter(filter);
    mSeekBar.getThumb().setColorFilter(filter);
    mFileLengthTextView.setText(String.valueOf(mFileLength));
    mSeekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
        @Override
        public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
            if(mMediaPlayer != null && fromUser) {
                mMediaPlayer.seekTo(progress);
                mHandler.removeCallbacks(mRunnable);

                long minutes = TimeUnit.MILLISECONDS.toMinutes(mMediaPlayer.getCurrentPosition());
                long seconds = TimeUnit.MILLISECONDS.toSeconds(mMediaPlayer.getCurrentPosition())
                        - TimeUnit.MINUTES.toSeconds(minutes);
                mCurrentProgressTextView.setText(String.format("%02d:%02d", minutes,seconds));

                updateSeekBar();

            } else if (mMediaPlayer == null && fromUser) {
                prepareMediaPlayerFromPoint(progress);
                updateSeekBar();
            }
        }

        @Override
        public void onStartTrackingTouch(SeekBar seekBar) {
            if(mMediaPlayer != null) {
                // remove message Handler from updating progress bar
                mHandler.removeCallbacks(mRunnable);
            }
        }

        @Override
        public void onStopTrackingTouch(SeekBar seekBar) {
            if (mMediaPlayer != null) {
                mHandler.removeCallbacks(mRunnable);
                mMediaPlayer.seekTo(seekBar.getProgress());

                long minutes = TimeUnit.MILLISECONDS.toMinutes(mMediaPlayer.getCurrentPosition());
                long seconds = TimeUnit.MILLISECONDS.toSeconds(mMediaPlayer.getCurrentPosition())
                        - TimeUnit.MINUTES.toSeconds(minutes);
                mCurrentProgressTextView.setText(String.format("%02d:%02d", minutes,seconds));
                updateSeekBar();
            }
        }
    });

    mPlayButton = (FloatingActionButton) view.findViewById(R.id.fab_play);
    mPlayButton.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            onPlay(isPlaying);
            isPlaying = !isPlaying;
        }
    });

    mFileNameTextView.setText(item.getName());
    mFileLengthTextView.setText(String.format("%02d:%02d", minutes,seconds));

    builder.setView(view);

    // request a window without the title
    dialog.getWindow().requestFeature(Window.FEATURE_NO_TITLE);

    return builder.create();
}
```

当点击播放录音的按钮之后，会调用 `onPlay()` 方法，然后根据 `isPlaying`（标识当前是否播放录音）的值，来调用不同的方法。

```java
private void onPlay(boolean isPlaying){
    if (!isPlaying) {
        //currently MediaPlayer is not playing audio
        if(mMediaPlayer == null) {
            startPlaying(); //start from beginning
        } else {
            resumePlaying(); //resume the currently paused MediaPlayer
        }

    } else {
        pausePlaying();
    }
}
```

 我们最关心的，莫过于 `startPlaying()` 这个方法，这个方法便是来开启播放录音的，我们首先将外部传入的有关的录音信息，设置给 `MediaPlayer`，然后开始调用 `mMediaPlayer.start()` 进行录音的播放，然后调用 `updateSeekbar()` 实时更新进度条的内容。当 `MediaPlayer` 的内容播放完成后，调用 `stopPlaying()` 方法，关闭 `mMediaPlayer`。

```java
private void startPlaying() {
    mPlayButton.setImageResource(R.drawable.ic_media_pause);
    mMediaPlayer = new MediaPlayer();

    try {
        mMediaPlayer.setDataSource(item.getFilePath());
        mMediaPlayer.prepare();
        mSeekBar.setMax(mMediaPlayer.getDuration());

        mMediaPlayer.setOnPreparedListener(new MediaPlayer.OnPreparedListener() {
            @Override
            public void onPrepared(MediaPlayer mp) {
                mMediaPlayer.start();
            }
        });
    } catch (IOException e) {
        Log.e(LOG_TAG, "prepare() failed");
    }

    mMediaPlayer.setOnCompletionListener(new MediaPlayer.OnCompletionListener() {
        @Override
        public void onCompletion(MediaPlayer mp) {
            stopPlaying();
        }
    });

    updateSeekBar();

    //keep screen on while playing audio
    getActivity().getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
} 
```

以上便是本文的全部内容，有关的代码我已经上传到 Github 上了，需要的 [点击这里](https://github.com/zhangmiaocc/SoundRecording)，喜欢的话，欢迎来波 star 和 fork。