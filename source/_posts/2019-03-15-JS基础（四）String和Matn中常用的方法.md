---
title: JS基础（四）String和Math中常用的方法
tags:
  - 前端
  - JavaScript
categories:
  - 前端
  - JavaScript
abbrlink: e28a6795
date: 2019-03-15 16:30:00
---

### 字符串中常用的方法

> 索引特点，从0开始 
> length属性，字符串的长度 
> 如果指定索引不存在，会得到undefined 
> 真实项目中,我们经常操作字符串，此时我们需要掌握一些常用方法 
> console.dir(String,prototype)

<!--more-->

#### charAt&&charCodaAt【兼容所有浏览器】

> str.charAt();返回索引指定的字符,当指定索引不存在时返回"", 
> 当指定索引不存在时str[100]返回undefined; 
> str.charCodeAt(),在charAt的基础上，获取指定位置字符的unicode编码 
> Str.fromcharCode()通过unicoed码 得到原有字符 与charCodeAt相反

#### substr&&substring&&slice
> str.substr(n,m):从索引n开始，截取m个字符（第一个参数支持负数） 
str.subtring(n,m):从索引n开始，截取到索引m处，不包含m;（不支持负数） 
str.slice(n,m):从索引n开始，截取到索引m处，不包含m,(支持负数)；
> 
当索引时负数时，是用字符串的总长度加上索引，在按照正数操作 
注意： 
如果只传递1个参数n，则从n截取到末尾 
如果索引超过最大值，则能截取多少是多少 
如果没有传参数，则相当于把整个字符串都截取了（字符串克隆）

#### toUperCase&&toLowerCase
> toUperCase():把字母全部大写 str.toUpperCase() 
toLowerCase():把字母全部小写

#### indexOf&&lastIndexOf【兼容所有浏览器】
> indexOf:获取当前字符首次出现的位置 
lastIndexOf:获取当前字符最后一次出现的位置

**注意 **
如果当前字符串没有出现过，结果为-1；由此可以借用此方法来检查是否具有某元素

#### split

> Str.split:按照某一元素将字符串划分为几组,`返回的是一个数组` 
> 若不存在，则保持原来的Str 
> 支持正则

```javascript
str='wedfg';
str.split('d');
//返回值是个数组
(2) ["we", "fg"]
0: "we"
1: "fg"
length: 2
__proto__: Array(0)
```

#### replace

> Str.replace:实现字符的替换 
> Str.replace(a,b) //用b替换a 
> 执行一次只能替换一个，想替换多个的多次执行，真实项目中一般正和则一起使用；

#### trim&&trimLeft&&trimRight

> Str.trimLeft:去除字符串左边兼容 
> Str.trimRight:去除字符串右边空格 
> Str.trim:去除字符串收尾空格

#### 案例：queryURLParameter

> 获取地址栏中URL地址问号的传递参数值 
> https://www.baidu.com/s?word=谷歌浏览器&tn=93219212_hao_pg&ie=utf-8 
> 问号后面就是我们传递的参数 
> https://www.baidu.com/s?f=8&rsv_bp=1&rsv_idx=1&word=小说&tn=94076069_hao_pg
>
> 我们的目标：把问号传递的参数值给解析出来 
> obj={word:’谷歌浏览器’,tn:93219212_hao_pg,ie:utf-8}
> ```javascript
> function queryURLParameter(url){
> //=>定义一个函数
>      var quesIndex=url.indexOf('?');
>      //获取？的位置
>      var obj={};//定义一个空函数
>      if(quesIndex===-1){
>      //如果？不存在
>      return obj;
>      }
>      url=url.substr(quesIndex+1);
>      //获取?后面的字符串
>      var ary=url.split('&');
>      //用&将字符串划分为数组
>      for(var i=0;i<ary.length;i++){
>          var curAry=ary[i].split('=');
>           //用=将字符串划分为长度为2的数组
>          obj[curAry[0]]= curAry[1];
>          //数组中第一个为属性，第二个为属性值
>       return obj;
>      }       
>  
>  }
> ```

### Math中常用的方法

> 数学函数，但是他是对象类型 
> Math 中为我们提供了很多常用操作数字的方法 
> conlse.dir(Math)查看有很多方法

#### @[abs]

> Math.abs() 取绝对值

#### @[ceil/floor]

> Math.ceil() 向上取整 
> Math.flloor() 向下取整

#### @[round]

> Math.round() 四舍五入

#### @[random]

> Math.random() 获取[0-1)之间的随机小数

```javascript
//获取0-10之间的随机小数[0-10]
Math.round(Math.random()*10)
//获取[]3-15]的随机整数
Math.round(Math.random())*12+3
```

**注意规律**

> 获取[n,m]之间的随机整数 
> `Math.round(Math.random())*(m-n)+n`

#### @[max/min]

> Matn.max:获取一组数据的最大值 
> Math.min:获取一组数据的最小值

##### @[PI]

> Math,PI:获取圆周率

#### @[pow/sqrt]

> Math.pow:获取一个值的多少次幂 
> Math.sqrt 开平方

#### 案例：[验证码](https://www.baidu.com/s?wd=%E9%AA%8C%E8%AF%81%E7%A0%81&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)基本功能实现 （结合Math和String）

> 一般是由后台处理，后台返回给客户端一张图片（图片中包含了验证码） （防止批量注册，前端容易被解析） 
> 验证码形式1、字母数字2、问答3、选择图片4、成语拼图5、图片拼图6、滑动拖拽

案例主要思想(获取4个字母和数字的组合)

> 获取文档中的元素(最后数字要[放进去](https://www.baidu.com/s?wd=%E6%94%BE%E8%BF%9B%E5%8E%BB&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)) 
> 定义一个空数组（用于存放随机获得的字符） 
> 定义一个取值区域（0-9，a-z,A-Z）共62个 
> 创建for循环 
> 获取一个0-61的随机整数（Math.round(Math.random*61)） 
> 利用这个整数获取该位置的字符，charAt()