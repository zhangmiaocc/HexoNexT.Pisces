---
title: Flutter cached_network_image 图片加载流程分析
tags:
  - Android
  - Flutter
categories:
  - Android
  - Flutter
abbrlink: a931c363
date: 2021-09-28 17:14:46
---

## 使用

组件[CachedNetworkImage](https://link.juejin.cn?target=https%3A%2F%2Fpub.dev%2Fpackages%2Fcached_network_image)可以支持直接使用或者通过`ImageProvider`。

**引入依赖**

```yaml
dependencies:
  cached_network_image: ^3.1.0
```

执行`flutter pub get`，项目中使用

**Import it**

```dart
import 'package:cached_network_image/cached_network_image.dart';
```

**添加占位图**

```dart
CachedNetworkImage(
        imageUrl: "http://via.placeholder.com/350x150",
        placeholder: (context, url) => CircularProgressIndicator(),
        errorWidget: (context, url, error) => Icon(Icons.error),
     ),
```

**进度条展示**

```dart
CachedNetworkImage(
        imageUrl: "http://via.placeholder.com/350x150",
        progressIndicatorBuilder: (context, url, downloadProgress) => 
                CircularProgressIndicator(value: downloadProgress.progress),
        errorWidget: (context, url, error) => Icon(Icons.error),
     ),
```

<!--more-->

**原生组件Image配合**

```dart
Image(image: CachedNetworkImageProvider(url))
```

**使用占位图并提供provider给其他组件使用**

```dart
CachedNetworkImage(
  imageUrl: "http://via.placeholder.com/200x150",
  imageBuilder: (context, imageProvider) => Container(
    decoration: BoxDecoration(
      image: DecorationImage(
          image: imageProvider,
          fit: BoxFit.cover,
          colorFilter:
              ColorFilter.mode(Colors.red, BlendMode.colorBurn)),
    ),
  ),
  placeholder: (context, url) => CircularProgressIndicator(),
  errorWidget: (context, url, error) => Icon(Icons.error),
),
```

这样就可以加载网络图片了，而且，图片加载完成时，就被缓存到本地了，首先看下图片的加载流程

> 官网说了，它现在不包含缓存，缓存功能实际上是另一个库`flutter_cache_manager`中实现的

## 原理

### 加载&显示

> 这里我们仅梳理图片加载和缓存的主流程，对于一些其他分支流程，或无关参数不做过多分析

首先，页面上使用的构造函数接收了一个必传参数imageUrl，用于生成ImageProvider提供图片加载

```dart
class CachedNetworkImage extends StatelessWidget{
  /// image提供
  final CachedNetworkImageProvider _image;

  /// 构造函数
  CachedNetworkImage({
    Key key,
    @required this.imageUrl,
    /// 省略部分
    this.cacheManager,
    /// ...
  })  : assert(imageUrl != null),
        /// ...
        _image = CachedNetworkImageProvider(
          imageUrl,
          headers: httpHeaders,
          cacheManager: cacheManager,
          cacheKey: cacheKey,
          imageRenderMethodForWeb: imageRenderMethodForWeb,
          maxWidth: maxWidthDiskCache,
          maxHeight: maxHeightDiskCache,
        ),
        super(key: key);
  
  @override
  Widget build(BuildContext context) {
    var octoPlaceholderBuilder =
        placeholder != null ? _octoPlaceholderBuilder : null;
    var octoProgressIndicatorBuilder =
        progressIndicatorBuilder != null ? _octoProgressIndicatorBuilder : null;
    /// ...

    return OctoImage(
      image: _image,
      /// ...
    );
  }
}
```

这里可以看到，构造函数初始化了一个本地变量`_image` 类型是`CachedNetworkImageProvider`，它继承ImageProvider提供图片加载，看下它的构造函数

```dart
/// 提供网络图片加载Provider并缓存
abstract class CachedNetworkImageProvider
    extends ImageProvider<CachedNetworkImageProvider> {
  /// Creates an object that fetches the image at the given URL.
  const factory CachedNetworkImageProvider(
    String url, {
    int maxHeight,
    int maxWidth,
    String cacheKey,
    double scale,
    @Deprecated('ErrorListener is deprecated, use listeners on the imagestream')
        ErrorListener errorListener,
    Map<String, String> headers,
    BaseCacheManager cacheManager,
    ImageRenderMethodForWeb imageRenderMethodForWeb,
  }) = image_provider.CachedNetworkImageProvider;

  /// 可选cacheManager. 默认使用 DefaultCacheManager()
  /// 当运行在web时,cacheManager没有使用.
  BaseCacheManager get cacheManager;

  /// 请求url.
  String get url;

  /// 缓存key
  String get cacheKey;
  
  /// ...

  @override
  ImageStreamCompleter load(
      CachedNetworkImageProvider key, DecoderCallback decode);
}
```

它的构造函数调用了`image_provider.CachedNetworkImageProvider`的实例在`_image_provider_io.dart`中是加载的具体实现类

```dart
/// IO implementation of the CachedNetworkImageProvider; the ImageProvider to
/// load network images using a cache.
class CachedNetworkImageProvider
    extends ImageProvider<image_provider.CachedNetworkImageProvider>
    implements image_provider.CachedNetworkImageProvider {
  /// Creates an ImageProvider which loads an image from the [url], using the [scale].
  /// When the image fails to load [errorListener] is called.
  const CachedNetworkImageProvider(
    this.url, {
  /// ...
  })  : assert(url != null),
        assert(scale != null);

  @override
  final BaseCacheManager cacheManager;
	/// ...

  @override
  Future<CachedNetworkImageProvider> obtainKey(
      ImageConfiguration configuration) {
    return SynchronousFuture<CachedNetworkImageProvider>(this);
  }

  /// 核心方法加载图片入口
  @override
  ImageStreamCompleter load(
      image_provider.CachedNetworkImageProvider key, DecoderCallback decode) {
    final chunkEvents = StreamController<ImageChunkEvent>();
    /// 多图加载
    return MultiImageStreamCompleter(
      codec: _loadAsync(key, chunkEvents, decode),
      chunkEvents: chunkEvents.stream,
      scale: key.scale,
      informationCollector: () sync* {
        yield DiagnosticsProeperty<ImageProvider>(
          'Image provider: $this \n Image key: $key',
          this,
          style: DiagnosticsTreeStyle.errorProperty,
        );
      },
    );
  }
```

这里的load方法即是图片加载的启动入口，它会在页面可见时被调用

它返回了一个`MultiImageStreamCompleter`传入`_loadAsync`，看下这个方法

```dart
 /// 异步加载
  Stream<ui.Codec> _loadAsync(
    CachedNetworkImageProvider key,
    StreamController<ImageChunkEvent> chunkEvents,
    DecoderCallback decode,
  ) async* {
    assert(key == this);
    try {
      /// 默认缓存管理器
      var mngr = cacheManager ?? DefaultCacheManager();
      assert(
          mngr is ImageCacheManager || (maxWidth == null && maxHeight == null),
          'To resize the image with a CacheManager the '
          'CacheManager needs to be an ImageCacheManager. maxWidth and '
          'maxHeight will be ignored when a normal CacheManager is used.');

      /// 下载逻辑放在ImageCacheManager，得到下载stream
      var stream = mngr is ImageCacheManager
          ? mngr.getImageFile(key.url,
              maxHeight: maxHeight,
              maxWidth: maxWidth,
              withProgress: true,
              headers: headers,
              key: key.cacheKey)
          : mngr.getFileStream(key.url,
              withProgress: true, headers: headers, key: key.cacheKey);

      await for (var result in stream) {
        if (result is FileInfo) {
          var file = result.file;
          var bytes = await file.readAsBytes();
          var decoded = await decode(bytes);
          /// 下载完成返回结果
          yield decoded;
        }
      }
    } catch (e) {
      /// ...
    } finally {
      await chunkEvents.close();
    }
  }
}
```

这里我们看到了默认缓存管理器`cacheManager`创建的地方，为`DefaultCacheManager`，那么它如何缓存的呢，后边再分析。

下载的逻辑也是放在了`ImageCacheManager`下了，返回结果是一个`stream`完成多图下载的支持，下载完成通过yield 返回给ui解码最终显示。

```dart
MultiImageStreamCompleter`支持多图加载继承自`ImageStreamCompleter
/// An ImageStreamCompleter with support for loading multiple images.
class MultiImageStreamCompleter extends ImageStreamCompleter {
  /// The constructor to create an MultiImageStreamCompleter. The [codec]
  /// should be a stream with the images that should be shown. The
  /// [chunkEvents] should indicate the [ImageChunkEvent]s of the first image
  /// to show.
  MultiImageStreamCompleter({
    @required Stream<ui.Codec> codec,
    @required double scale,
    Stream<ImageChunkEvent> chunkEvents,
    InformationCollector informationCollector,
  })  : assert(codec != null),
        _informationCollector = informationCollector,
        _scale = scale {
    /// 显示逻辑
    codec.listen((event) {
      if (_timer != null) {
        _nextImageCodec = event;
      } else {
        _handleCodecReady(event);
      }
    }, onError: (dynamic error, StackTrace stack) {
      reportError(
        context: ErrorDescription('resolving an image codec'),
        exception: error,
        stack: stack,
        informationCollector: informationCollector,
        silent: true,
      );
    });
    /// ...
    }
  }
  /// 处理解码完成
  void _handleCodecReady(ui.Codec codec) {
    _codec = codec;
    assert(_codec != null);

    if (hasListeners) {
      _decodeNextFrameAndSchedule();
    }
  }
  /// 解码下一帧并绘制
  Future<void> _decodeNextFrameAndSchedule() async {
    try {
      _nextFrame = await _codec.getNextFrame();
    } catch (exception, stack) {
      reportError(
        context: ErrorDescription('resolving an image frame'),
        exception: exception,
        stack: stack,
        informationCollector: _informationCollector,
        silent: true,
      );
      return;
    }
    if (_codec.frameCount == 1) {
      // ImageStreamCompleter listeners removed while waiting for next frame to
      // be decoded.
      // There's no reason to emit the frame without active listeners.
      if (!hasListeners) {
        return;
      }

      // This is not an animated image, just return it and don't schedule more
      // frames.
      _emitFrame(ImageInfo(image: _nextFrame.image, scale: _scale));
      return;
    }
    _scheduleAppFrame();
  }
}
```

这里做了显示逻辑，和最终转化成flutter上帧的处理，`_scheduleAppFrame`完成发送帧的处理

### 下载&缓存

上边的`mngr`调用了`ImageCacheManager`中的`getImageFile`方法现在就到了`flutter_cache_manager`这个三方库当中，它是被隐式依赖的，文件是`image_cache_manager.dart`

```dart
mixin ImageCacheManager on BaseCacheManager {
	Stream<FileResponse> getImageFile(
	    String url, {
	    String key,
	    Map<String, String> headers,
	    bool withProgress,
	    int maxHeight,
	    int maxWidth,
	  }) async* {
	    if (maxHeight == null && maxWidth == null) {
	      yield* getFileStream(url,
	          key: key, headers: headers, withProgress: withProgress);
	      return;
	    }
	  /// ...
	 }
  /// ...
}

getFileStream`方法实现在子类`cache_manager.dart`文件中的`CacheManager
class CacheManager implements BaseCacheManager {
	 /// 缓存管理
  CacheStore _store;

  /// Get the underlying store helper
  CacheStore get store => _store;

  /// 下载管理
  WebHelper _webHelper;

  /// Get the underlying web helper
  WebHelper get webHelper => _webHelper;
  
  /// 从下载或者缓存读取file返回stream
  @override
  Stream<FileResponse> getFileStream(String url,
      {String key, Map<String, String> headers, bool withProgress}) {
    key ??= url;
    final streamController = StreamController<FileResponse>();
    _pushFileToStream(
        streamController, url, key, headers, withProgress ?? false);
    return streamController.stream;
  }
  
  Future<void> _pushFileToStream(StreamController streamController, String url,
      String key, Map<String, String> headers, bool withProgress) async {
    key ??= url;
    FileInfo cacheFile;
    try {
      /// 缓存判断
      cacheFile = await getFileFromCache(key);
      if (cacheFile != null) {
        /// 有缓存直接返回
        streamController.add(cacheFile);
        withProgress = false;
      }
    } catch (e) {
      print(
          'CacheManager: Failed to load cached file for $url with error:\n$e');
    }
    /// 没有缓存或者过期下载
    if (cacheFile == null || cacheFile.validTill.isBefore(DateTime.now())) {
      try {
        await for (var response
            in _webHelper.downloadFile(url, key: key, authHeaders: headers)) {
          if (response is DownloadProgress && withProgress) {
            streamController.add(response);
          }
          if (response is FileInfo) {
            streamController.add(response);
          }
        }
      } catch (e) {
        assert(() {
          print(
              'CacheManager: Failed to download file from $url with error:\n$e');
          return true;
        }());
        if (cacheFile == null && streamController.hasListener) {
          streamController.addError(e);
        }
      }
    }
    unawaited(streamController.close());
  }
}

```

缓存判断逻辑在`CacheStore`提供两级缓存

```dart
class CacheStore {
  Duration cleanupRunMinInterval = const Duration(seconds: 10);
	/// 未下载完成缓存
  final _futureCache = <String, Future<CacheObject>>{};
  /// 已下载完缓存
  final _memCache = <String, CacheObject>{};

  /// ...

  Future<FileInfo> getFile(String key, {bool ignoreMemCache = false}) async {
    final cacheObject =
        await retrieveCacheData(key, ignoreMemCache: ignoreMemCache);
    if (cacheObject == null || cacheObject.relativePath == null) {
      return null;
    }
    final file = await fileSystem.createFile(cacheObject.relativePath);
    return FileInfo(
      file,
      FileSource.Cache,
      cacheObject.validTill,
      cacheObject.url,
    );
  }

  Future<void> putFile(CacheObject cacheObject) async {
    _memCache[cacheObject.key] = cacheObject;
    await _updateCacheDataInDatabase(cacheObject);
  }

  Future<CacheObject> retrieveCacheData(String key,
      {bool ignoreMemCache = false}) async {
    /// 判断是否已缓存过
    if (!ignoreMemCache && _memCache.containsKey(key)) {
      if (await _fileExists(_memCache[key])) {
        return _memCache[key];
      }
    }
    /// 未缓存的 已加入futureCache中的key直接返回
    if (!_futureCache.containsKey(key)) {
      final completer = Completer<CacheObject>();
      /// 未加入的添加到futureCache
      unawaited(_getCacheDataFromDatabase(key).then((cacheObject) async {
        if (cacheObject != null && !await _fileExists(cacheObject)) {
          final provider = await _cacheInfoRepository;
          await provider.delete(cacheObject.id);
          cacheObject = null;
        }

        _memCache[key] = cacheObject;
        completer.complete(cacheObject);
        unawaited(_futureCache.remove(key));
      }));
      _futureCache[key] = completer.future;
    }
    return _futureCache[key];
  }
	/// ...
  /// 更新到数据库
  Future<dynamic> _updateCacheDataInDatabase(CacheObject cacheObject) async {
    final provider = await _cacheInfoRepository;
    return provider.updateOrInsert(cacheObject);
  }
}
```

`_cacheInfoRepository`缓存仓库是`CacheObjectProvider`使用的数据库缓存对象

```dart
class CacheObjectProvider extends CacheInfoRepository
    with CacheInfoRepositoryHelperMethods {
  Database db;
  String _path;
  String databaseName;

  CacheObjectProvider({String path, this.databaseName}) : _path = path;

	/// 打开
  @override
  Future<bool> open() async {
    if (!shouldOpenOnNewConnection()) {
      return openCompleter.future;
    }
    var path = await _getPath();
    await File(path).parent.create(recursive: true);
    db = await openDatabase(path, version: 3,
        onCreate: (Database db, int version) async {
      await db.execute('''
      create table $_tableCacheObject ( 
        ${CacheObject.columnId} integer primary key, 
        ${CacheObject.columnUrl} text, 
        ${CacheObject.columnKey} text, 
        ${CacheObject.columnPath} text,
        ${CacheObject.columnETag} text,
        ${CacheObject.columnValidTill} integer,
        ${CacheObject.columnTouched} integer,
        ${CacheObject.columnLength} integer
        );
        create unique index $_tableCacheObject${CacheObject.columnKey} 
        ON $_tableCacheObject (${CacheObject.columnKey});
      ''');
    }, onUpgrade: (Database db, int oldVersion, int newVersion) async {
      /// ...
    return opened();
  }

  @override
  Future<dynamic> updateOrInsert(CacheObject cacheObject) {
    if (cacheObject.id == null) {
      return insert(cacheObject);
    } else {
      return update(cacheObject);
    }
  }

  @override
  Future<CacheObject> insert(CacheObject cacheObject,
      {bool setTouchedToNow = true}) async {
    var id = await db.insert(
      _tableCacheObject,
      cacheObject.toMap(setTouchedToNow: setTouchedToNow),
    );
    return cacheObject.copyWith(id: id);
  }

  @override
  Future<CacheObject> get(String key) async {
    List<Map> maps = await db.query(_tableCacheObject,
        columns: null, where: '${CacheObject.columnKey} = ?', whereArgs: [key]);
    if (maps.isNotEmpty) {
      return CacheObject.fromMap(maps.first.cast<String, dynamic>());
    }
    return null;
  }

  @override
  Future<int> delete(int id) {
    return db.delete(_tableCacheObject,
        where: '${CacheObject.columnId} = ?', whereArgs: [id]);
  }
}
```

可见数据库缓存的是`CacheObject`对象，保存了url、key、relativePath等信息

```dart
class CacheObject {
  static const columnId = '_id';
  static const columnUrl = 'url';
  static const columnKey = 'key';
  static const columnPath = 'relativePath';
  static const columnETag = 'eTag';
  static const columnValidTill = 'validTill';
  static const columnTouched = 'touched';
  static const columnLength = 'length';
}
```

没有缓存下调用了`_webHelper.downloadFile`方法

```dart
class WebHelper {
  WebHelper(this._store, FileService fileFetcher)
      : _memCache = {},
        fileFetcher = fileFetcher ?? HttpFileService();

  final CacheStore _store;
  @visibleForTesting
  final FileService fileFetcher;
  final Map<String, BehaviorSubject<FileResponse>> _memCache;
  final Queue<QueueItem> _queue = Queue();

  ///Download the file from the url
  Stream<FileResponse> downloadFile(String url,
      {String key,
      Map<String, String> authHeaders,
      bool ignoreMemCache = false}) {
    key ??= url;
    if (!_memCache.containsKey(key) || ignoreMemCache) {
      var subject = BehaviorSubject<FileResponse>();
      _memCache[key] = subject;
      /// 下载或者加入队列
      unawaited(_downloadOrAddToQueue(url, key, authHeaders));
    }
    return _memCache[key].stream;
  }
  
  Future<void> _downloadOrAddToQueue(
    String url,
    String key,
    Map<String, String> authHeaders,
  ) async {
    //如果太多请求被执行，加入队列等待
    if (concurrentCalls >= fileFetcher.concurrentFetches) {
      _queue.add(QueueItem(url, key, authHeaders));
      return;
    }

    concurrentCalls++;
    var subject = _memCache[key];
    try {
      await for (var result
          in _updateFile(url, key, authHeaders: authHeaders)) {
        subject.add(result);
      }
    } catch (e, stackTrace) {
      subject.addError(e, stackTrace);
    } finally {
      concurrentCalls--;
      await subject.close();
      _memCache.remove(key);
      _checkQueue();
    }
  }
  
   ///下载资源
  Stream<FileResponse> _updateFile(String url, String key,
      {Map<String, String> authHeaders}) async* {
    var cacheObject = await _store.retrieveCacheData(key);
    cacheObject = cacheObject == null
        ? CacheObject(url, key: key)
        : cacheObject.copyWith(url: url);
    /// 请求得到response
    final response = await _download(cacheObject, authHeaders);
    yield* _manageResponse(cacheObject, response);
  }
  
  
  Stream<FileResponse> _manageResponse(
      CacheObject cacheObject, FileServiceResponse response) async* {
    /// ...
    if (statusCodesNewFile.contains(response.statusCode)) {
      int savedBytes;
      await for (var progress in _saveFile(newCacheObject, response)) {
        savedBytes = progress;
        yield DownloadProgress(
            cacheObject.url, response.contentLength, progress);
      }
      newCacheObject = newCacheObject.copyWith(length: savedBytes);
    }
		/// 加入缓存
    unawaited(_store.putFile(newCacheObject).then((_) {
      if (newCacheObject.relativePath != oldCacheObject.relativePath) {
        _removeOldFile(oldCacheObject.relativePath);
      }
    }));

    final file = await _store.fileSystem.createFile(
      newCacheObject.relativePath,
    );
    yield FileInfo(
      file,
      FileSource.Online,
      newCacheObject.validTill,
      newCacheObject.url,
    );
  }
  
  Stream<int> _saveFile(CacheObject cacheObject, FileServiceResponse response) {
    var receivedBytesResultController = StreamController<int>();
    unawaited(_saveFileAndPostUpdates(
      receivedBytesResultController,
      cacheObject,
      response,
    ));
    return receivedBytesResultController.stream;
  }
  
  Future _saveFileAndPostUpdates(
      StreamController<int> receivedBytesResultController,
      CacheObject cacheObject,
      FileServiceResponse response) async {
    /// 根据路径创建file
    final file = await _store.fileSystem.createFile(cacheObject.relativePath);

    try {
      var receivedBytes = 0;
      /// 写文件
      final sink = file.openWrite();
      await response.content.map((s) {
        receivedBytes += s.length;
        receivedBytesResultController.add(receivedBytes);
        return s;
      }).pipe(sink);
    } catch (e, stacktrace) {
      receivedBytesResultController.addError(e, stacktrace);
    }
    await receivedBytesResultController.close();
  }

}
```

## 总结

`cached_network_image`图片加载流程依赖`ImageProvider`，缓存和下载逻辑放在另一个库`flutter_cache_manager`下载文件在`WebHelper`中提供队列管理，依赖传入`FileService`做具体获取文件方便扩展默认实现`HttpFileService`，下载完成后路径保存在`CacheObject`保存在`sqflite`数据库
