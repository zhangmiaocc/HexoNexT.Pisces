---
title: 'Unable to resolve dependency for '':trunk@debug/compileClasspath'''
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: 4cae3fd9
date: 2018-12-04 16:50:17
---

```
repositories {
        google()
        jcenter()
    }
```

Go to `File->Settings->Build, Execution, Deployment->Gradle->Uncheck Offline work option.`

<!-- more-->

