---
title: 版本号比较versionCompare
date: 2018-07-18 15:35:45
tags:
- blog
- markdown
- Android 
- Java
categories:
- Android 
- 代码片段
---

``` java
/**
 *   版本号比较versionCompare方法，java实现
 *   if v1 > v2  , return 1;
 *   if v1 < v2  , return 2;
 *   if equal    , return 0;
 *   input error ,return -1;
 */

public class Test {

    public static void main(String[] versions) {
        int result1 = versionCompare("0.1.5", "0.1.5");
        System.out.print("resultCode1:" + result1);

        int result2 = versionCompare("0.2.5", "0.1.5");
        System.out.print("resultCode1:" + result2);

        int result3 = versionCompare("0.1.4", "0.1.5");
        System.out.print("resultCode1:" + result3);

        int result4 = versionCompare("0.1.5c测试", "0.1.5");
        System.out.print("resultCode1:" + result4);


    }

    public static int versionCompare(String v1, String v2) {

        Pattern pattern = Pattern.compile("\\d+(\\.\\d+)*");
        if (!pattern.matcher(v1).matches() || !pattern.matcher(v2).matches()) {
            return -1;
        }
        String[] str1 = v1.split("\\.");
        String[] str2 = v2.split("\\.");

        int length = str1.length < str2.length ? str1.length : str2.length;
        for (int i = 0; i < length; i++) {
            int diff = Integer.valueOf(str1[i]) - Integer.valueOf(str2[i]);
            if (diff == 0) {
                continue;
            } else {
                return diff > 0 ? 1 : 2;
            }
        }
        return 0;
    }
}
```