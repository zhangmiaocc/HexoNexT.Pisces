---
title: GoogleMap使用介绍
tags:
  - Android
  - Map
categories:
  - Android
  - Map
abbrlink: 50fe3b5b
date: 2019-07-25 16:17:10
---

## 概述

通过本文，你可以知道如何使用 GoogleMap 相关 API、定位当前位置、获取当前所在城市、获取当前位置附近的地点、导航、地点搜索等内容。大致内容，可以查看如下思维导图。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/GoogleMapXmind.png)

<!--more-->

### **特别注意**：

demo 使用的 GoogleMap key 对应我自己电脑的 keystore，如果重新编译项目，生成的apk使用的是你的电脑的 keystore，和我的 keystore 是不一样的，所以要正常运行，

1. 直接下载我编译好的 apk；
2. 用我的项目包名，和你自己的 keystore 的 SHA-1 去申请新的 GoogleMap key。（方法在下面，往下看哈）

## 前期准备

### 网络

因为国内众所周知的网络问题，谷歌地图的页面加载和 API 的使用会出现无效的情况，如果你要使用或调试，首先要确保是可以科学上网的。

### 设备

Android 设备，必须**安装了 Google 服务**。

### 谷歌账号

作为用户，不需要拥有或者登录你的 Google 账号。网络和 Google 服务正常即可。

作为开发者，必须拥有一个 Google 账号。要知道，谷歌地图是不开源的，要使用他的 API ，必须用你的包名，和编译 Android app 的 keystore 的 SHA-1，去申请App 对应的 Google Map Key。获取 Key 的教程官网讲得很详细，[请看这里](https://developers.google.com/maps/documentation/android-api/signup)

[Google API Console](https://console.developers.google.com/project/_/apiui/credential) 设置完成后，如下图所示：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190725162411.png)

设置好后，还要确保已经 **Enable**了对应的API，**不然会出现数据访问不到的情况**。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190725162433.png)

把这个 API key 填入 *AndroidManifest.xml* ：

```xml
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="your key" />
```

### Map 相关

#### 前提

##### 初始化 Google Map

###### 布局

在你的布局文件里面，只需要如下方式声明一个 Fragment，这个 Fragment 是可以放在 LinearLayout 或 RelativeLayout 下面的。

```xml
<fragment
    android:id="@+id/map"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    map:uiZoomControls="true"
    map:uiCompass = "true"
    class="com.google.android.gms.maps.SupportMapFragment"
    android:layout_below="@id/layout_btn"/>
```



###### Activity

在 *onCreate* 的时候，进行初始化操作。

```java
private GoogleMap mMap;
SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
        .findFragmentById(map);
mapFragment.getMapAsync(new OnMapReadyCallback() {
    @Override
    public void onMapReady(GoogleMap googleMap) {
        //初始化完 GoogleMap
        mMap = googleMap;
    }
});
```

##### 确保 GoogleMap 实例化

在使用 GoogleMap 相关接口前，必须确保 GoogleMap 已经实例化，即已经在 *onMapReady*回调中获取了 GoogleMap实例。

#### 开关类

##### 定位按钮

定位功能必须获取用户的位置权限，判断是非获取了用户权限，没有获取则手动请求权限。

```java
/**
 *
 * 检查定位权限，如果未授权则请求该权限
 * @return  true：已经授权； false：未授权
 */
private boolean checkLocationPermission() {
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION)
            != PackageManager.PERMISSION_GRANTED) {
        // Permission to access the location is missing.
        PermissionUtils.requestPermission(this, LOCATION_PERMISSION_REQUEST_CODE,
                Manifest.permission.ACCESS_FINE_LOCATION, true);
        return false;
    }
    return true;
}

public static void requestPermission(FragmentActivity activity, int requestId,
                                     String permission, boolean finishActivity) {
    if (ActivityCompat.shouldShowRequestPermissionRationale(activity, permission)) {
        // Display a dialog with rationale.
        PermissionUtils.RationaleDialog.newInstance(requestId, finishActivity)
                .show(activity.getSupportFragmentManager(), "dialog");
    } else {
        // Location permission has not been granted yet, request it.
        ActivityCompat.requestPermissions(activity, new String[]{permission}, requestId);

    }
}
```

要让地图上显示开关按钮，只需要设置`mMap.setMyLocationEnabled(true);`

点击此按钮，地图的摄像头就会开始移动，定位到当前设备所在位置，如果要获取点击此按钮的回调，可以设置监听器 `mMap.setOnMyLocationButtonClickListener`

##### 放大／缩小按钮

地图的放大缩小，就以摄像头焦点（地图中心）进行缩放。要出现这个开关，只需要

```java
UiSettings uiSettings = mMap.getUiSettings();
uiSettings.setZoomControlsEnabled(true);
```

##### 指南针按钮

```java
UiSettings uiSettings = mMap.getUiSettings();
uiSettings.setCompassEnabled(true);
```

##### 手势

1. 旋转

   ```java
   UiSettings uiSettings = mMap.getUiSettings();
   uiSettings.setRotateGesturesEnabled(mRotateGesturesEnabled);
   ```

2. 平移

   ```java
   UiSettings uiSettings = mMap.getUiSettings();
   uiSettings.setScrollGesturesEnabled(mScrollGesturesEnabled);
   ```

#### 自定义标注

##### 清除标注

```java
mMap.clear();
```

##### 新增标注

增加一个标注，只需要把当前的经纬度，图标，标题等信息传入 *MarkerOptions* ，之后在 *addMarker* 到 map对象即可。

```java
LatLng latLng = new LatLng(latitude, longitude);

//标记当前坐标
mMap.addMarker(new MarkerOptions()
        .position(latLng)
        .icon(BitmapDescriptorFactory.fromResource(R.drawable.icon_position_small))
        .title(getString(R.string.map_camera_center_location)));
```

#### 摄像头

##### 当前位置

```java
//获取当前摄像头中心点的坐标
double latitude = mMap.getCameraPosition().target.latitude;
double longitude = mMap.getCameraPosition().target.longitude;
```

##### 移动相关监听

1. 开始监听

   ```java
   //摄像头开始滑动监听
   mMap.setOnCameraMoveStartedListener(new GoogleMap.OnCameraMoveStartedListener() {
       @Override
       public void onCameraMoveStarted(int reason) {
           if (reason == GoogleMap.OnCameraMoveStartedListener.REASON_GESTURE) {
               //表示摄像头移动是为了响应用户在地图上做出的手势，如平移、倾斜、通过捏合手指进行缩放或旋转地图
   
           } else if (reason == GoogleMap.OnCameraMoveStartedListener
                   .REASON_API_ANIMATION) {
               //表示 API 移动摄像头是为了响应非手势用户操作，如点按 zoom 按钮、点按 My Location 按钮或点击标记
   
           } else if (reason == GoogleMap.OnCameraMoveStartedListener
                   .REASON_DEVELOPER_ANIMATION) {
               //表示您的应用已发起摄像头移动
           }
       }
   } ;
   ```

2. 取消

   ```java
   //摄像头移动停止状态的监听
   mMap.setOnCameraIdleListener(new GoogleMap.OnCameraIdleListener() {
       @Override
       public void onCameraIdle() {
          
       }
   });
   ```

3. 停止

   ```java
   //摄像头移动中被取消时的监听
   mMap.setOnCameraMoveCanceledListener(new GoogleMap.OnCameraMoveCanceledListener() {
       @Override
       public void onCameraMoveCanceled() {
   
       }
   });
   ```

#### 点击监听

##### 点击

```java
//点击地图上某个坐标
mMap.setOnMapClickListener(new GoogleMap.OnMapClickListener() {
    @Override
    public void onMapClick(LatLng latLng) {
        Toast.makeText(MapActivity.this,
                getString(R.string.map_click_tip, latLng.latitude, latLng.longitude),
                Toast.LENGTH_SHORT).show();
    }
});
```

##### 长按

```java
//长按地图上某个坐标
mMap.setOnMapLongClickListener(new GoogleMap.OnMapLongClickListener() {
    @Override
    public void onMapLongClick(LatLng latLng) {
        Toast.makeText(MapActivity.this,
                getString(R.string.map_long_click_tip, latLng.latitude, latLng.longitude),
                Toast.LENGTH_SHORT).show();
    }
});
```

##### 点击景点

```java
//点击地图上某个景点
mMap.setOnPoiClickListener(new GoogleMap.OnPoiClickListener() {
    @Override
    public void onPoiClick(PointOfInterest pointOfInterest) {
        Toast.makeText(MapActivity.this,
                getString(R.string.map_place_click_tip, pointOfInterest.name, pointOfInterest.placeId, pointOfInterest.latLng.latitude, pointOfInterest.latLng.longitude),
                Toast.LENGTH_SHORT).show();
    }
});
```

#### 快照

快照分两种，一种是直接不管地图有没有加载完，就把当前的地图截屏，如果此时地图未加载完，截取的图片会出现模糊的情况；另外一种是判断地图是否在加载中，如果是，则等加载完毕再截图，如果不是，就直接截图。

```java
//是否等待地图加载完毕
if (mWaitForMapLoaded) {
    //获取加载完的高清图片
    mMap.setOnMapLoadedCallback(new GoogleMap.OnMapLoadedCallback() {
        @Override
        public void onMapLoaded() {
            if (null != mMap) {
                mMap.snapshot(new GoogleMap.SnapshotReadyCallback() {
                    @Override
                    public void onSnapshotReady(Bitmap bitmap) {
                    	//获取bitmap
                    	
                    }
                });
            }
        }
    });
} else {
    //未加载完，就执行快照功能，会导致截取模糊图片
    mMap.snapshot(new GoogleMap.SnapshotReadyCallback() {
        @Override
        public void onSnapshotReady(Bitmap bitmap) {
        	//获取bitmap
        
        }
    });
}
```

### GoogleApiClient

#### 前提

1. 初始化 GoogleApiClient

   在 onCreate 的时候进行初始化操作

   ```java
   /**
    * 初始化 google client 用于获取地点信息
    */
   private void createGoogleApiClient() {
       if (mGoogleApiClient == null) {
           mGoogleApiClient = new GoogleApiClient
                   .Builder(this)
                   .addApi(Places.GEO_DATA_API)
                   .addApi(Places.PLACE_DETECTION_API)
                   .addApi(LocationServices.API)
                   .addConnectionCallbacks(new GoogleApiClient.ConnectionCallbacks() {
                       @Override
                       public void onConnected(@Nullable Bundle bundle) {
                           //连接成功
                           mConnected = true;
                       }
   
                       @Override
                       public void onConnectionSuspended(int i) {
                           //连接暂停
                       }
                   })
                   .addOnConnectionFailedListener(new GoogleApiClient.OnConnectionFailedListener() {
                       @Override
                       public void onConnectionFailed(@NonNull ConnectionResult connectionResult) {
                           //连接失败
                           mConnected = false;
                       }
                   })
                   .enableAutoManage(this, new GoogleApiClient.OnConnectionFailedListener() {
                       @Override
                       public void onConnectionFailed(@NonNull ConnectionResult connectionResult) {
                           //连接失败
                           mConnected = false;
                       }
                   })
                   .build();
       }
   }
   ```

2. 在 onStart() 的时候连接，在 onStop() 的时候，断开连接。

   ```java
   @Override
   protected void onStart() {
       super.onStart();
       if (null != mGoogleApiClient) {
           mGoogleApiClient.connect();
       }
   }
   
   @Override
   protected void onStop() {
       super.onStop();
       if (null != mGoogleApiClient) {
           mGoogleApiClient.disconnect();
       }
   }
   ```

3. 使用位置相关 API 的前提，必须确保用户授予位置权限。方法同上的 *checkLocationPermission()*

#### 获取当前定位的经纬度坐标

##### 科普

- WGS-84

  国际标准的坐标系，国际标准的 GPS 设备定位获取的就是这种坐标。简称 **地球坐标系**。

- GCJ-02

  在我们国家，据说是为了保密，我们不使用 WGS-84 坐标，而是使用经过加密的 GCJ-02，高德地图，谷歌地图（国内板块）都是使用这个坐标系。这个就是俗称的 **火星坐标系**。

- 其他坐标系

  比如百度地图，他用的是他们家的 BD-09 坐标，这个只适用于百度相关产品。搜狗地图也有自己的坐标。

- 格式
  注意到谷歌地图的坐标是 latitude, longitude 格式，即 纬度，经度 格式。和国内的百度，高德坐标写法是反过来的。国内的一般是 经度，纬度 的方式。

##### API

```java
Location lastLocation = LocationServices.FusedLocationApi.getLastLocation(mGoogleApiClient);
if (null != lastLocation) {
    double latitude = lastLocation.getLatitude();
    double longitude = lastLocation.getLongitude();
} else {
  
}
```

刚开始我也以为这么简单就可以了，在测试设备上确实可以获取到坐标，但是实际上，大多数情况，在第一次运行定位的时候，获取的 Location 对象是为null。所以还需要注册位置变化的监听。等监听到位置信息后，移除此监听，防止不断监听引起高耗电现象。这部分百度地图用得很方便，他封装好了，但是谷歌地图就要自己实现。

```java
private long UPDATE_INTERVAL = 10 * 1000;  /* 10 secs */
private long FASTEST_INTERVAL = 1500; /* 1.5 sec */
private final int MAX_TIME = 2; //最多定位次数
private int time;
if (null != lastLocation) {
    //.....
} else {
	//如果获取不到位置信息，注册位置变化监听
	regLocationUpdates();
}
private void regLocationUpdates() {
    if (!checkLocationPermission()) {
        showContentText(getString(R.string.cilent_permission_failed));
        return;
    }

    LocationRequest locationRequest = LocationRequest.create()
            .setPriority(LocationRequest.PRIORITY_HIGH_ACCURACY)
            .setInterval(UPDATE_INTERVAL)
            .setFastestInterval(FASTEST_INTERVAL);

    LocationServices.FusedLocationApi.requestLocationUpdates(mGoogleApiClient,
            locationRequest, new LocationListener() {
                @Override
                public void onLocationChanged(Location location) {
                    time++;

                    //如果获取到位置信息，则移除位置变化监听
                    if (null != location) {
                        LocationServices.FusedLocationApi.removeLocationUpdates(mGoogleApiClient, this);
                        //获取定位的经纬度
                        double latitude = location.getLatitude();
                        double longitude = location.getLongitude();
						return;
                    }

                    //如果超过最大的定位次数则停止位置变化监听
                    if (time == MAX_TIME) {
                        //移除位置变化监听
                        LocationServices.FusedLocationApi.removeLocationUpdates(mGoogleApiClient, this);
						//获取当前位置失败
                    }
                }
            });
}
```

现在终于获取到定位坐标了，等等，好像不太对，这个坐标和我实际位置好像有不少的偏差…..我打开了 GoogleMap 这个官方的 APP，点击了他的定位。这下就懵逼了，怎么官方的这个是没问题了，误差很小….

我明明用的是 GoogleMap 的 API，为什么定位会不一样？

原因在刚才的坐标系里面，谷歌地图的国内板块是用 GCJ-02，但是定位 API 获取的坐标是国际标准坐标 WGS-84，所以需要把 WGS-84 转化 GCJ-02。

那谷歌地图 APP 上为什么可以呢？

我猜测，谷歌地图在访问网络的时候，会进行位置判断，如果是国内坐标，就进行转换，国外坐标就不转换。

**WGS-84 转化 GCJ-02 方法**

```java
static double a = 6378245.0;
static double ee = 0.00669342162296594323;

/**
 * 把 WGS-84 转换成 GCJ-02
 * @param wgLoc
 * @return
 */
public static LatLng transformFromWGSToGCJ(LatLng wgLoc) {

    //如果在国外，则默认不进行转换
    if (outOfChina(wgLoc.latitude, wgLoc.longitude)) {
        //return new LatLng(wgLoc.latitude, wgLoc.longitude);
        return wgLoc;
    }

    double dLat = transformLat(wgLoc.longitude - 105.0,
            wgLoc.latitude - 35.0);
    double dLon = transformLon(wgLoc.longitude - 105.0,
            wgLoc.latitude - 35.0);
    double radLat = wgLoc.latitude / 180.0 * Math.PI;
    double magic = Math.sin(radLat);
    magic = 1 - ee * magic * magic;
    double sqrtMagic = Math.sqrt(magic);
    dLat = (dLat * 180.0)/ ((a * (1 - ee)) / (magic * sqrtMagic) * Math.PI);
    dLon = (dLon * 180.0) / (a / sqrtMagic * Math.cos(radLat) * Math.PI);

    return new LatLng(wgLoc.latitude + dLat, wgLoc.longitude + dLon);
}


private static double transformLat(double x, double y) {
    double ret = -100.0 + 2.0 * x + 3.0 * y + 0.2 * y * y + 0.1 * x * y
            + 0.2 * Math.sqrt(x > 0 ? x : -x);
    ret += (20.0 * Math.sin(6.0 * x * Math.PI) + 20.0 * Math.sin(2.0 * x
            * Math.PI)) * 2.0 / 3.0;
    ret += (20.0 * Math.sin(y * Math.PI) + 40.0 * Math.sin(y / 3.0
            * Math.PI)) * 2.0 / 3.0;
    ret += (160.0 * Math.sin(y / 12.0 * Math.PI) + 320 * Math.sin(y
            * Math.PI / 30.0)) * 2.0 / 3.0;
    return ret;
}

private static double transformLon(double x, double y) {
    double ret = 300.0 + x + 2.0 * y + 0.1 * x * x + 0.1 * x * y + 0.1
            * Math.sqrt(x > 0 ? x : -x);
    ret += (20.0 * Math.sin(6.0 * x * Math.PI) + 20.0 * Math.sin(2.0 * x
            * Math.PI)) * 2.0 / 3.0;
    ret += (20.0 * Math.sin(x * Math.PI) + 40.0 * Math.sin(x / 3.0
            * Math.PI)) * 2.0 / 3.0;
    ret += (150.0 * Math.sin(x / 12.0 * Math.PI) + 300.0 * Math.sin(x
            / 30.0 * Math.PI)) * 2.0 / 3.0;
    return ret;
}

/**
 * 判断是否在中国以外
 * @param lat
 * @param lon
 * @return
 */
public static boolean outOfChina(double lat, double lon) {
    if (lon < 72.004 || lon > 137.8347)
        return true;
    if (lat < 0.8293 || lat > 55.8271)
        return true;
    return false;
}
```

#### 根据经纬度获取附近地点

通过经纬度获取对应的地理位置信息，这个叫做**反地理编码请求**，以前百度地图有个 API `mGeoCoder.reverseGeoCode(mReverseGeoCodeOption);`可以直接使用，Google地图也有类似的，只不过在我使用过程中存在 bug。

心急想马上能用的，可以自己调到 **Web API** 中的 【根据经纬度获取附近地点】章节。

此处存在的问题：如果坐标切换为国外，就会造成获取数据为null。即使修改地区 mGeocoder = new Geocoder(this, Locale.JAPAN) 也无效。

1. 初始化 **Geocoder**

   ```java
   mGeocoder = new Geocoder(this, Locale.getDefault());
   //设置区域
   //mGeocoder = new Geocoder(this, Locale.JAPAN);
   ```

2. 在子线程获取数据

   ```java
   List<Address> addressList =  mGeocoder.getFromLocation(latitude, longitude, maxResult);
   if (null != addressList && addressList.size() > 0) {
       //遍历获取附近地点信息
       for (Address address : addressList) {
       //省
       String adminArea = address.getAdminArea();
       //市
       String city = address.getLocality();
       //地址
       String feature = address.getFeatureName();
       } 
   } else {
       //获取附近地点失败
   }
   ```

### startActivity方式

#### 地点搜索

1. 打开 Activity

   `private static final int REQUEST_CODE_AUTOCOMPLETE = 2;`

   ```java
   /**
    * 打开搜索的 Activity
    */
   private void openAutocompleteActivity() {
       try {
           // MODE_FULLSCREEN 全屏方式启动一个 Activity
           // MODE_OVERLAY 启动浮在界面上的控件
           Intent intent = new PlaceAutocomplete.IntentBuilder(PlaceAutocomplete.MODE_OVERLAY)
                   .build(this);
           startActivityForResult(intent, REQUEST_CODE_AUTOCOMPLETE);
       } catch (GooglePlayServicesRepairableException e) {
           GoogleApiAvailability.getInstance().getErrorDialog(this, e.getConnectionStatusCode(), 0).show();
       } catch (GooglePlayServicesNotAvailableException e) {
           String message = "Google Play Services is not available: " +
                   GoogleApiAvailability.getInstance().getErrorString(e.errorCode);
           Toast.makeText(this, message, Toast.LENGTH_SHORT).show();
       }
   }
   ```

2. 在 *onActivityResult* 回调中获取搜索的地点信息

   ```java
   @Override
       protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    		if (requestCode == REQUEST_CODE_AUTOCOMPLETE) {
               if (resultCode == RESULT_OK) {
                   Place place = PlaceAutocomplete.getPlace(this, data);
                   String placeText = null;
                   if (null != place) {
                       placeText = "place.getId() = " + place.getId()
                                 + "\nplace.getName() = " + place.getName()
                                 + "\nplace.getLatLng().latitude = " + place.getLatLng().latitude
                                 + "\nplace.getLatLng().longitude = " + place.getLatLng().longitude
                                 + "\nplace.getAddress() = " +place.getAddress()
                                 + "\nplace.getPhoneNumber() = " + place.getPhoneNumber()
                                 + "\nplace.getLocale() = " + place.getLocale()
                                 + "\n.......";
                   }
           
                   Toast.makeText(this, getString(R.string.start_by_activity_btn_search_place_result, placeText), Toast.LENGTH_LONG).show();
               } else if (resultCode == PlaceAutocomplete.RESULT_ERROR) {
               	//错误码
                   Status status = PlaceAutocomplete.getStatus(this, data);
               } else if (resultCode == RESULT_CANCELED) {
               	//取消
               }
           } 
    
    }
   ```

#### 导航

```java
/**
 * 开启导航
 * @param latitude
 * @param longitude
 */
private void startNavigation(double latitude, double longitude) {
    try {
        Uri gmmIntentUri = Uri.parse("google.navigation:q="+latitude+","+longitude+"&mode=d");
        Intent mapIntent = new Intent(Intent.ACTION_VIEW, gmmIntentUri);
        mapIntent.setPackage("com.google.android.apps.maps");
        startActivity(mapIntent);
    } catch (Exception e) {
        //提示未安装google map
       	
        //开启google map下载界面
        showGoogleMapDownloadView();
    }
}

/**
 * 开启google map下载界面
 */
private void showGoogleMapDownloadView() {
    Uri uri = Uri.parse("market://details?id=com.google.android.apps.maps");
    Intent intent = new Intent(Intent.ACTION_VIEW, uri);
    this.startActivity(intent);
}
```

#### 附近地点

1. 获取定位权限

   获取方法和前面一样，使用 *checkLocationPermission()*

2. 打开 Activity，开启附近地点选择

   `private static final int PLACE_PICKER_REQUEST = 3;`

   ```java
   PlacePicker.IntentBuilder builder = new PlacePicker.IntentBuilder();
   try {
       startActivityForResult(builder.build(this), PLACE_PICKER_REQUEST);
   } catch (GooglePlayServicesRepairableException e) {
       e.printStackTrace();
   } catch (GooglePlayServicesNotAvailableException e) {
       e.printStackTrace();
   }
   ```

3. 在 onActivityResult 获取选择的地点信息

   ```java
   @Override
   protected void onActivityResult(int requestCode, int resultCode, Intent data) {
     if (requestCode == PLACE_PICKER_REQUEST) {
         if (resultCode == RESULT_OK) {
             Place place = PlacePicker.getPlace(this, data);
             //place.getName() .... 
         }
     }
   }
   ```

## Places

### 当前位置及附近地点

#### API

```java
private final int mMaxEntries = 5;
/**
 * 获取当前位置及附近地点
 */
private void getCurrentPlaces(){
    if (!mConnected) {
        return;
    }

    if (!checkLocationPermission()) {
    	//未授权定位
    	return;
    }

    PendingResult<PlaceLikelihoodBuffer> result = Places.PlaceDetectionApi
            .getCurrentPlace(mGoogleApiClient, null);
    result.setResultCallback(new ResultCallback<PlaceLikelihoodBuffer>() {
        @Override
        public void onResult(@NonNull PlaceLikelihoodBuffer likelyPlaces) {
            int i = 0;
            String[] likelyPlaceNames = new String[mMaxEntries];
            String[] likelyPlaceAddresses = new String[mMaxEntries];
            String[] likelyPlaceAttributions = new String[mMaxEntries];
            LatLng[] likelyPlaceLatLngs = new LatLng[mMaxEntries];
            for (PlaceLikelihood placeLikelihood : likelyPlaces) {
                // Build a list of likely places to show the user. Max 5.
                likelyPlaceNames[i] = (String) placeLikelihood.getPlace().getName();
                likelyPlaceAddresses[i] = (String) placeLikelihood.getPlace().getAddress();
                likelyPlaceAttributions[i] = (String) placeLikelihood.getPlace()
                        .getAttributions();
                likelyPlaceLatLngs[i] = placeLikelihood.getPlace().getLatLng();

                i++;

                //String placeId = (String) placeLikelihood.getPlace().getId();

                if (i > (mMaxEntries - 1)) {
                    break;
                }
            }
            likelyPlaces.release();

            StringBuilder builder = new StringBuilder();
            for (String placeName : likelyPlaceNames) {
                builder.append(placeName + "\n");
            }

            //显示地点列表
            //builder.toString()
        }
    });
}
```

### 根据 PlaceID 获取对应地点

在 *onResult* 返回的 places 一般只有一个，所以取第一个元素，就是 id 对应的地点信息。

```java
/**
 * 通过placeId获取对应的位置信息
 * @param placeId
 */
private  void getPlaceById(String placeId){
    if (TextUtils.isEmpty(placeId)) {
        return;
    }

    Places.GeoDataApi.getPlaceById(mGoogleApiClient, placeId)
            .setResultCallback(new ResultCallback<PlaceBuffer>() {
                @Override
                public void onResult(PlaceBuffer places) {
                    if (places.getStatus().isSuccess() && places.getCount() > 0) {
                        Place myPlace = places.get(0);
                        //获取id对应的Place
                    } else {
                        //没有获取到数据
                    }
                    places.release();
                }
            });
}
```

## Web API

这里说的 Web API 是指：通过拼接 url 的方式，向 Google 服务器请求数据，服务器会返回一段 JSON，我们本地再用 fastjson 解析，获取对应的数据。

### 根据坐标获取所在城市

这里的 URL 可以这样拼接

```java
private static final String GOOGLE_MAP_URL = "https://maps.google.com/maps/api/geocode/json?language=%1$s&sensor=true&latlng=%2$s,%3$s";
```



其中 *%1$s* 对应的是语言，比如我要返回的是中文，那么对于的就是 *zh-CN*，*%2$s %3$s* 对应的就是纬度和经度。

```java
private static final String DEFAULT_LANGUAGE = "zh-CN";
/**
 * 拼接url(默认设置语言为中文)
 *
 * @param latitude
 * @param longitude
 */
public static String getGoogleMapUrl(double latitude, double longitude) {
    return String.format(GOOGLE_MAP_URL, DEFAULT_LANGUAGE, Double.valueOf(latitude), Double.valueOf(longitude));
}
```

这样，外面只需要直接调这个方法，参数传入纬度、经度，就会返回拼接好的 URL。

获取了 URL，我们就可以异步访问网络，去获取数据了。这里主要讲下思路，详细代码，可以自己查看 [Demo](https://github.com/ansuote/GoogleMapDemo)。

以Demo为例，拼接的URL为：

```java
https://maps.google.com/maps/api/geocode/json?language=zh-CN&sensor=true&latlng=22.536817569098282,113.97451490163802
```

获取的 JSON 如下

```json
{
    "results": [
        {
            "address_components": [
                {
                    "long_name": "9028",
                    "short_name": "9028",
                    "types": [
                        "street_number"
                    ]
                },
                {
                    "long_name": "深南大道",
                    "short_name": "深南大道",
                    "types": [
                        "route"
                    ]
                },
                {
                    "long_name": "华侨城",
                    "short_name": "华侨城",
                    "types": [
                        "neighborhood",
                        "political"
                    ]
                },
                {
                    "long_name": "南山区",
                    "short_name": "南山区",
                    "types": [
                        "political",
                        "sublocality",
                        "sublocality_level_1"
                    ]
                },
                {
                    "long_name": "深圳市",
                    "short_name": "深圳市",
                    "types": [
                        "locality",
                        "political"
                    ]
                },
                {
                    "long_name": "广东省",
                    "short_name": "广东省",
                    "types": [
                        "administrative_area_level_1",
                        "political"
                    ]
                },
                /**
                 * 篇幅原因，省略其余数据
                 */
    "status": "OK"
}
```

其中 type 对应的值是 **locality** 的就是**城市名字**，**political** 代表**政治实体**。

关键代码如下：

```java
/**
 * 根据经纬度获取对应的城市
 * @param latitude
 * @param longitude
 */
private void getCityByLatlngWeb(double latitude, double longitude) {
    String urlString = GoogleMapUrlUtil.getGoogleMapUrl(latitude, longitude);
    if (URLUtil.isNetworkUrl(urlString)) {
        new GeocodeTask().execute(urlString);
    }
}
class GeocodeTask extends AsyncTask<String, Void, JSONObject> {
    @Override
    protected void onPreExecute() {
        
    }

    @Override
    protected com.alibaba.fastjson.JSONObject doInBackground(String... params) {
    	//请求网络，并且转化为 JSONObject 对象
        return GoogleMapUrlUtil.returnResult(HttpUtils.doGet(params[0]));
    }

    @Override
    protected void onPostExecute(com.alibaba.fastjson.JSONObject result) {
        if (null != result) {
            JSONArray jsonArray = result.getJSONArray("results");
            if (null != jsonArray) {
                Object firstObj = jsonArray.get(0);
                if (null != firstObj) {
                    GeocodeBean bean = JSON.parseObject(firstObj.toString(), GeocodeBean.class);
                    //获取所在城市
                    String city = getLocality(bean);
                    
                }
            }
        }

    }
}
public class GeocodeBean {
    private List<AddressComponent> address_components;
    private String formatted_address;
    //geometry
    private String place_id;
    private List<String> types;
 
 	//set, get 方法自己补充哈
}
/**
 * 从数据集里面获取所在城市
 * @param bean
 * @return
 */
private String getLocality(GeocodeBean bean) {
    boolean isFound = false;
    if (null != bean) {
        List<AddressComponent> list = bean.getAddress_components();
        if (null != list) {
            for (AddressComponent address : list) {
                List<String> types = address.getTypes();
                for (String type : types) {
                    if ("locality".equals(type)) {
                        isFound = true;
                        break;
                    }
                }
                if (isFound) {
                    return address.getShort_name();
                }
            }
        }
    }

    return null;
}
```

### 根据经纬度获取附近地点

前面获取对应城市中用的 URL 很方便，基本上没有限制。但是获取附近地点的就没这么好了，在拼接 URL 的时候，需要加上谷歌授权给你的 *Web API key*。这个是官方推荐的做法，详情可以查看[官网的介绍](https://developers.google.com/maps/documentation/geocoding/intro)

#### 特别注意

这里有一点要特别注意的，这里说的 *Web API key* 必须要重新申请的。之前我们使用 GoogleMap 的时候已经申请了 KEY，但是选项选择的是**【Android apps】**，只是作用于 Android Map 相关 API，这个时候访问 Web API 必须重新申请多一个 KEY。申请方法和前面一样，只是选项为**【None】**即可。

设置完之后如果一般间隔几分钟就可以调用，如果不行，就要手动开启服务。[点击此处开启](https://console.developers.google.com/apis/api/places_backend?project=_),选择对应的项目，**【启用】** *Google Places API Web Service* 服务。

```java
private static final String GOOGLE_MAP_PLACES_URL = "https://maps.googleapis.com/maps/api/place/nearbysearch/json?language=%1$s&location=%2$s,%3$s&radius=%4$s&type=%5$s&key=%6$s";
```

这下参数有点多了哈，前面3个和之前一样，分别对应语言、纬度、经度。第四个参数是查询地点的半径多大，第五个是类型，这里我使用的是 **point_of_interest** 意思是**已经命名的景点**，[其他类型可以查看官网](https://developers.google.com/maps/documentation/geocoding/intro#Types)，最后一个参数是你的 APP 申请的 Key值。

关键代码如下：

```java
/**
 * 获取经纬度对应的附近地点
 * @param latitude
 * @param longitude
 */
private void getPlacesByLatLngWeb(double latitude, double longitude) {
    String url = GoogleMapUrlUtil.getGoogleMapPlacesUrl(latitude, longitude);
    if (URLUtil.isNetworkUrl(url)) {
        new NearbyPlacesTask().execute(url);
    }
}
class NearbyPlacesTask extends AsyncTask<String, Void, JSONObject> {

    @Override
    protected JSONObject doInBackground(String... params) {
        return GoogleMapUrlUtil.returnResult(HttpUtils.doGet(params[0]));
    }

    @Override
    protected void onPostExecute(JSONObject result) {
        if (null != result) {
            JSONArray jsonArray = result.getJSONArray("results");
            if (null != jsonArray) {
                List<NearbyPlaceBean> list = JSON.parseArray(jsonArray.toString(), NearbyPlaceBean.class);
                //遍历 list 可以获取对应的附近地点信息
                //list的size能比较大，可以根据项目需求，指截取前面10个。
            }
        }

    }
}
public class NearbyPlaceBean {
    //geometry
    //icon
    private String id;      //1f7541b5f729cdc8bc8bb546f205848c50315af7
    private String name;    //澎柏白金假日公寓
    //photos
    private String place_id;    //ChIJX9_kRAXsAzQRKmc97njB67c
    //reference
    //scope
    //types
    private String vicinity;    //深圳市宝安区

    private GeometryBean geometry;
    
    //set，get 自己补充哈
}
```

## 实战：附近地点推荐

可以像上面那样使用 Activity 的方式，弹出 Google 自带的地点推荐／选取界面，但是这样的UI定制性低，不能按照项目需求显示界面。所以根据上面学习到的东西，我重新写了个类似的界面，实现地点推荐、搜索、选择和截图功能的功能。考虑到频繁调接口会损耗请求次数，所以我设置了个半径，超过半径的才重新请求数据。

## 限制／收费

前面我把类型分为 Map，Places主要原因就是，他们的收费标准是不同的。[详情可见](https://developers.google.com/maps/pricing-and-plans/#details)

| Android                       | 标准方案                                                     | 高级                                                         |
| :---------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| Google Maps Android API       | 不受限制地免费使用。[1](https://developers.google.com/maps/pricing-and-plans/#sup_1) | 定价基于所需数量。如需了解详细信息，请参阅[Premium Plan使用率和限制](https://developers.google.com/maps/premium/usage-limits)。 |
| Google Places API for Android | 默认每天 1,000 次免费请求，[信用卡验证](https://developers.google.com/places/android-api/usage#query-limits)后可增至每天 150,000 次免费请求。符合要求的应用可免费提升。[详情](https://developers.google.com/places/android-api/usage) | —-                                                           |

| Web 服务                      | 标准                                                         | 高级                                                         |
| :---------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| Google Places API Web Service | 每天 150,000 次免费请求（[信用卡验证](https://developers.google.com/places/web-service/usage)后）。 | 定价基于所需数量。如需了解详细信息，请参阅[Premium Plan使用率和限制](https://developers.google.com/maps/premium/usage-limits)。 |

## 学习资料

### 官方 Demo

官方 Demo 要跑起来，必须像前面的方式一样，去申请对应的 Key。

#### [android-samples-apiDemos](https://github.com/googlemaps/android-samples/tree/master/ApiDemos)

介绍 Map 相关 API。

#### [android-play-places](https://github.com/googlesamples/android-play-places/)

介绍 Places 相关 API 。包括地点搜索，附近地点选择，地点补全等。

#### [android-maps-utils](https://github.com/googlemaps/android-maps-utils)

**点聚合 Clustering** 可以通过这个 demo 学习，GoogleMap的点聚合和百度是一样的用法，外层代码基本上是一样的。

### 工具类网页

1. [API Console](https://console.developers.google.com/apis/credentials?project=savvy-generator-167702) （Key 管理控制台）
2. [Google Map API 查询](https://developers.google.com/android/reference/com/google/android/gms/maps/package-summary)
3. 坐标反查 (通过经纬度查对应地点)
   [GoogleMap](https://www.google.com/maps)
   (谷歌地图直接把经纬度输入输入框即可查询，例如输入：22.536817569098282,113.97451490163802 )
   [高德地图](http://lbs.amap.com/console/show/picker)
   [百度地图](http://api.map.baidu.com/lbsapi/getpoint/)

### 相关参考
[1]官方 Map 教程 https://developers.google.com/maps/documentation/android-api/
[2]官方 Places 教程 https://developers.google.com/places/android-api/
[3]启动 GoogleMap https://developers.google.com/maps/documentation/android-api/intents
[4]Android使用intent调取导航或者地图 https://blog.csdn.net/qwer4755552/article/details/51659833
[5]关于地图和偏移的那些事 https://blog.csdn.net/sanjay_f/article/details/48493699
[6]地图坐标转换大全 [http://www.eoeandroid.com/forum.php?mod=viewthread&tid=332419](http://www.eoeandroid.com/forum.php?mod=viewthread&tid=332419)
[7]Show Popup when Location access is disable by user (Andorid Google Maps) https://stackoverflow.com/questions/24160472/show-popup-when-location-access-is-disable-by-user-andorid-google-maps
[8]How to show enable location dialog like Google maps? https://stackoverflow.com/questions/29801368/how-to-show-enable-location-dialog-like-google-maps