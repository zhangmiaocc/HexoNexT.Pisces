---
title: Flutter Web加载慢问题优化和解决方案
tags:
  - Android
  - Flutter
categories:
  - Android
  - Flutter
abbrlink: eea7bb08
date: 2023-08-09 15:34:32
---

## 优化重点项
将以下资源本地化，打包到项目中
- canvaskit.js和canvaskit.wasm
- KFOmCnqEu92Fr1Me5WZLCzYlKw.ttf
- css2?family=Noto_Sans+SC

ps：最初canvaskit资源上传到阿里云，采用国内镜像资源加载。这次为了保险起见，也放入到本地。
好处：只要服务器正常能访问，这些资源文件就能够正常加载成功，最大程度上规避页面加载异常的风险！
最终项目中添加一些配置文件，如下图：
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809163641.png)
<!--more-->
`index.html`文件中更改canvaskit加载方式为本地
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809163718.png)

## 下载相关
### 下载canvaskit.wasm和canvaskit.js
https://unpkg.com/canvaskit-wasm@0.35.0/bin/canvaskit.js
https://unpkg.com/canvaskit-wasm@0.35.0/bin/canvaskit.wasm
下载地址方式获取：F12 ->Network
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809163733.png)

### 下载KFOmCnqEu92Fr1Me5WZLCzYlKw.ttf
https://fonts.gstatic.com/s/roboto/v20/KFOmCnqEu92Fr1Me5WZLCzYlKw.ttf


### 下载字体CSS
https://fonts.googleapis.com/css2?family=Noto+Sans+SC
下载的样式保存在本地，命名为 localfonts.css
更改CSS中引用的woff2字体文件，更改为本地路径
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809163747.png)

### 字体文件批量下载
```java
curl -O https://fonts.gstatic.com/s/notosanssc/v26/k3kXo84MPvpLmixcA63oeALhLIiP-Q-87KaAaH7rzeAODp22mF0qmF4CSjmPC6A0Rg5g1igg1w.[1-120].woff2
```
下载的文件装入到woff2文件夹放在 web/canvaskit 路径下
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809163811.png)

## 配置相关
执行flutter build web打包命令后生成build/web文件
由于打包后的文件资源引用还是url链接形式，故需要修改build/web目录下的main.dart.js

### 替换远端路径为本地路径
将`https://fonts.gstatic.com/s/roboto/v20/KFOmCnqEu92Fr1Me5WZLCzYlKw.ttf`替换成`canvaskit/KFOmCnqEu92Fr1Me5WZLCzYlKw.ttf`
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809164058.png)
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809164106.png)

将`https://fonts.googleapis.com/css2?family="+A.bF(o," ","+")`替换成`canvaskit/localfonts.css`
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809164133.png)
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809164141.png)

## 脚本自动打包&替换资源引用路径
flutter_build.sh脚本自动打包以及打完包后修改build/web目录下的main.dart.js中资源引用路径替换成本地路径
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809164243.png)
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20230809164254.png)
```shell
#!/bin/zsh

BLACK="\033[30m"
RED="\033[31m"
GREEN="\033[32m"
YELLOW="\033[33m"
BLUE="\033[34m"
PINK="\033[35m"
CYAN="\033[36m"
WHITE="\033[37m"
NORMAL="\033[0;39m"
END="\033[0m"


# 指定文件路径
filepath="./build/web/main.dart.js"

function chooseBuildTypeFunc {

    echo
    echo "[$RED 1 $END]$CYAN 构建release包$END"
    echo "[$CYAN q $END] 退出"
    echo

    echo "$GREEN请输入对应的数字或q退出:$END"
    read param
    echo

    # 检测空格是否存在
    if [[ -z $param ]]; then
        echo "$RED输入有误,请重新输入$END"
        chooseCommitTypeFunc;
        return
    fi
    
    if [ $param = "1" ]; then
        buildFunc;
    elif [ $param = "q" ]; then
        echo
    else
        echo "$RED输入有误,请重新输入$END"
        echo
        chooseCommitTypeFunc;
    fi
}

function buildFunc {

    # 开始打包
    flutter build web --web-renderer canvaskit --dart-define=BROWSER_IMAGE_DECODING_ENABLED=false --release
    # 结束打包

    # 定义 URL
    old_url="https:\/\/fonts.gstatic.com\/s\/roboto\/v20"
    new_url="canvaskit"

    # 使用 sed 命令查找并替换 URL
    sed -i '' "s~$old_url~$new_url~g" $filepath

    # 定义字符串
    old_string="https:\/\/fonts.googleapis.com\/css2?family="
    new_string="canvaskit\/localfonts.css"

    # 使用 sed 命令查找并替换字符串
    # 我们使用 "|" 作为定界符，而不是常用的 "/"
    sed -i '' "s|$old_string|$new_string|g" $filepath

    # 定义所有可能的字母
    LETTERS=({a..z} {A..Z})

    # 使用 for 循环遍历所有字母
    for letter in "${LETTERS[@]}"; do
      # 使用 grep 来匹配字符串
      if grep -Fq "+A.b${letter}(o,\" \",\"+\")" $filepath; then
        # 定义字符串
        old_string="+A.b$letter(o,\" \",\"+\")"
        new_string=""

        # 使用 perl 的 -i 选项进行原地编辑（直接修改文件）
        # 使用 \Q...\E 将目标字符串视为文字而不是正则表达式
        perl -i -pe 's/\Q'$old_string'\E/'$new_string'/g' $filepath
        break # 停止循环，因为我们已经找到了匹配的字符串
      fi
    done

    echo "$CYAN============================ SUCCESS ============================$END"
}
chooseBuildTypeFunc;
```