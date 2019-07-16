---
title: Android_SDK版本号与APILevel的对应关系
tags:
  - Android
  - SDK
  - API
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: 46f6283e
date: 2018-07-18 16:51:29
---

### 一、Android各版本对应的SDK版本 

|                                                              |                                                              |                          |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------ | ------------------------------------------------------------ |
| 平台版本                                                     | API                                                     | VERSION_CODE             | 备注                                                         |
| [Android 9.0](https://developer.android.com/preview/)                  | [28](https://developer.android.com/sdk/api_diff/28/changes)    | `Android P Preview`（9.0）                            |[平台亮点](https://developer.android.com/preview/) |
| [Android 8.1](https://developer.android.com/about/versions/oreo/android-8.0)                  | [27](https://developer.android.com/sdk/api_diff/26/changes.html)   | `Oreo`（8.1）                            |[平台亮点](https://developer.android.com/about/versions/oreo/) |
| [Android 8.0](https://developer.android.com/about/versions/oreo/android-8.0)                  | [26](https://developer.android.com/sdk/api_diff/26/changes.html)   | `Oreo`（8.0）                            |[平台亮点](https://developer.android.com/about/versions/oreo/) |
| [Android 7.1](https://developer.android.com/about/versions/nougat/android-7.1.html)               | [25](https://developer.android.com/sdk/api_diff/25/changes.html)   | `Nougat`（牛轧糖）     | [平台亮点](https://developer.android.com/about/versions/nougat/index.html) |
| [Android 7.0](https://developer.android.com/about/versions/nougat/android-7.0.html) | [24](https://developer.android.com/sdk/api_diff/24/changes.html) | `N`（牛轧糖）                       | [平台亮点](https://developer.android.com/about/versions/nougat/index.html) |
| [Android 6.0](https://developer.android.com/about/versions/marshmallow/android-6.0.html) | [23](https://developer.android.com/sdk/api_diff/23/changes.html) | `M`（棉花糖）                      | [平台亮点](https://developer.android.com/about/versions/marshmallow/index.html) |
| [Android 5.1](https://developer.android.com/about/versions/android-5.1.html) | [22](https://developer.android.com/sdk/api_diff/22/changes.html) | `LOLLIPOP_MR1`（棒棒糖）           | [平台亮点](https://developer.android.com/about/versions/lollipop.html) |
| [Android 5.0](https://developer.android.com/about/versions/android-5.0.html) | [21](https://developer.android.com/sdk/api_diff/21/changes.html) | `LOLLIPOP` （棒棒糖）              |                                                              |
| Android 4.4W                                                 | [20](https://developer.android.com/sdk/api_diff/20/changes.html) | `KITKAT_WATCH`（奇巧巧克力）          | 仅限 KitKat for Wearables                                    |
| [Android 4.4](https://developer.android.com/about/versions/android-4.4.html) | [19](https://developer.android.com/sdk/api_diff/19/changes.html) | `KITKAT` （奇巧巧克力）                | [平台亮点](https://developer.android.com/about/versions/kitkat.html) |
| [Android 4.3](https://developer.android.com/about/versions/android-4.3.html) | [18](https://developer.android.com/sdk/api_diff/18/changes.html) | `JELLY_BEAN_MR2`（果冻豆）         | [平台亮点](https://developer.android.com/about/versions/jelly-bean.html) |
| [Android 4.2、4.2.2](https://developer.android.com/about/versions/android-4.2.html) | [17](https://developer.android.com/sdk/api_diff/17/changes.html) | `JELLY_BEAN_MR1`（果冻豆）         | [平台亮点](https://developer.android.com/about/versions/jelly-bean.html#android-42) |
| [Android 4.1、4.1.1](https://developer.android.com/about/versions/android-4.1.html) | [16](https://developer.android.com/sdk/api_diff/16/changes.html) | `JELLY_BEAN` （果冻豆）            | [平台亮点](https://developer.android.com/about/versions/jelly-bean.html#android-41) |
| [Android 4.0.3、4.0.4](https://developer.android.com/about/versions/android-4.0.3.html) | [15](https://developer.android.com/sdk/api_diff/15/changes.html) | `ICE_CREAM_SANDWICH_MR1`（冰激凌三明治） | [平台亮点](https://developer.android.com/about/versions/android-4.0-highlights.html) |
| [Android 4.0、4.0.1、4.0.2](https://developer.android.com/about/versions/android-4.0.html) | [14](https://developer.android.com/sdk/api_diff/14/changes.html) | `ICE_CREAM_SANDWICH`（冰激凌三明治）     |                                                              |
| [Android 3.2](https://developer.android.com/about/versions/android-3.2.html) | [13](https://developer.android.com/sdk/api_diff/13/changes.html) | `HONEYCOMB_MR2` （蜂巢）         |                                                              |
| [Android 3.1.x](https://developer.android.com/about/versions/android-3.1.html) | [12](https://developer.android.com/sdk/api_diff/12/changes.html) | `HONEYCOMB_MR1`（蜂巢）         | [平台亮点](https://developer.android.com/about/versions/android-3.1-highlights.html) |
| [Android 3.0.x](https://developer.android.com/about/versions/android-3.0.html) | [11](https://developer.android.com/sdk/api_diff/11/changes.html) | `HONEYCOMB` （蜂巢）             | [平台亮点](https://developer.android.com/about/versions/android-3.0-highlights.html) |
| [Android 2.3.4 Android 2.3.3](https://developer.android.com/about/versions/android-2.3.3.html) | [10](https://developer.android.com/sdk/api_diff/10/changes.html) | `GINGERBREAD_MR1`（姜饼）        | [平台亮点](https://developer.android.com/about/versions/android-2.3-highlights.html) |
| [Android 2.3.2 Android 2.3.1 Android 2.3](https://developer.android.com/about/versions/android-2.3.html) | [9](https://developer.android.com/sdk/api_diff/9/changes.html) | `GINGERBREAD`（姜饼）            |                                                              |
| [Android 2.2.x](https://developer.android.com/about/versions/android-2.2.html) | [8](https://developer.android.com/sdk/api_diff/8/changes.html) | `FROYO`（冻酸奶）                  | [平台亮点](https://developer.android.com/about/versions/android-2.2-highlights.html) |
| [Android 2.1.x](https://developer.android.com/about/versions/android-2.1.html) | [7](https://developer.android.com/sdk/api_diff/7/changes.html) | `ECLAIR_MR1` （埃克拉）            | [平台亮点](https://developer.android.com/about/versions/android-2.0-highlights.html) |
| [Android 2.0.1](https://developer.android.com/about/versions/android-2.0.1.html) | [6](https://developer.android.com/sdk/api_diff/6/changes.html) | `ECLAIR_0_1`（埃克拉）             |                                                              |
| [Android 2.0](https://developer.android.com/about/versions/android-2.0.html) | [5](https://developer.android.com/sdk/api_diff/5/changes.html) | `ECLAIR`（埃克拉）                 |                                                              |
| [Android 1.6](https://developer.android.com/about/versions/android-1.6.html) | [4](https://developer.android.com/sdk/api_diff/4/changes.html) | `DONUT`（甜甜圈）                  | [平台亮点](https://developer.android.com/about/versions/android-1.6-highlights.html) |
| [Android 1.5](https://developer.android.com/about/versions/android-1.5.html) | [3](https://developer.android.com/sdk/api_diff/3/changes.html) | `CUPCAKE`（纸杯蛋糕）                | [平台亮点](https://developer.android.com/about/versions/android-1.5-highlights.html) |
| [Android 1.1](https://developer.android.com/about/versions/android-1.1.html) | 2                                                            | `BASE_1_1`               |                                                              |
| Android 1.0                                                  | 1                                                            | `BASE`    |                |                                                              |

### 二、Android各版本的市场占有率和对应JDK版本

| Version                                                      | Codename           | API   | Distribution |
| ------------------------------------------------------------ | ------------------ | ----- | ------------ |
| [2.3.3 - 2.3.7](https://developer.android.com/about/versions/android-2.3.3.html) | Gingerbread        | 10    | 0.3%         |
| [4.0.3 - 4.0.4](https://developer.android.com/about/versions/android-4.0.html) | Ice Cream Sandwich | 15    | 0.4%         |
| [4.1.x](https://developer.android.com/about/versions/android-4.1.html) | Jelly Bean         | 16    | 1.5%         |
| [4.2.x](https://developer.android.com/about/versions/android-4.2.html) | Jelly Bean         | 17                 | 2.2%  |
| [4.3](https://developer.android.com/about/versions/android-4.3.html) |  Jelly Bean         |18                 | 0.6%  |
| [4.4](https://developer.android.com/about/versions/android-4.4.html) | KitKat             | 19    | 10.3%        |
| [5.0](https://developer.android.com/about/versions/android-5.0.html) | Lollipop           | 21    | 4.8%         |
| [5.1](https://developer.android.com/about/versions/android-5.1.html) | Lollipop           | 22                 | 17.6% |
| [6.0](https://developer.android.com/about/versions/marshmallow/index.html) | Marshmallow        | 23    | 25.5%        |
| [7.0](https://developer.android.com/about/versions/nougat/index.html) | Nougat             | 24    | 22.9%        |
| [7.1](https://developer.android.com/about/versions/nougat/android-7.1.html) | Nougat             | 25                 | 8.2%  |
| [8.0](https://developer.android.com/about/versions/oreo/index.html) | Oreo               | 26    | 4.9%         |
| [8.1](https://developer.android.com/about/versions/oreo/android-8.1.html) | Oreo               |27                 | 0.8%  |              |

<!--more-->
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190716110502.png)

*以 7 天为周期收集的数据（截止于 2018 年 1 月 8 日）。 未显示任何分布份额不足 0.1% 的版本。*

### 三、屏幕尺寸和密度

|        | ldpi | mdpi | tvdpi | hdpi  | xhdpi | xxhdpi | Total |
| ------ | ---- | ---- | ----- | ----- | ----- | ------ | ----- |
| Small  | 0.4% |      |       |       |       | 0.1%   | 0.5%  |
| Normal |      | 0.9% | 0.3%  | 27.3% | 39.3% | 23.3%  | 91.1% |
| Large  |      | 2.4% | 1.5%  | 0.4%  | 0.7%  | 0.5%   | 5.5%  |
| Xlarge |      | 1.8% |       | 0.6%  | 0.5%  |        | 2.9%  |
| Total  | 0.4% | 5.1% | 1.8%  | 28.3% | 40.5% | 23.9%  |       | |

*以 7 天为周期收集的数据（截止于 2018 年 1 月 8 日）。 未显示任何分布份额不足 0.1% 的屏幕配置。*

数据来源：[Android信息中心](https://developer.android.com/about/dashboards/)

