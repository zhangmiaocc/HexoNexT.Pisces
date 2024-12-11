---
title: 使用Hexo搭建博客
tags:
  - Hexo
  - 建站
categories:
  - 建站
abbrlink: eb656981
date: 2018-07-11 17:01:27
---

### 准备工作

- [`Hexo`](https://hexo.io/)：快速、简洁且高效的博客框架,官网有[中文文档](https://hexo.io/zh-cn/)
- [`NexT`](https://github.com/iissnan/hexo-theme-next)：Hexo 适用的[主题](http://theme-next.iissnan.com/)
- [`LeanCloud`](https://leancloud.cn/)：数据库(可不用)
- [`GitHub`](https://github.com/)：博客就发布在 GitPage

### 安装&配置 Hexo

#### 安装 Hexo

全局安装 Hexo 官方的脚手架

```cmd
$ npm install -g hexo-cli
```

然后初始化博客，并安装依赖包

```cmd
$ hexo init <folder>
$ cd <folder>
$ npm install
```

> `<folder>` 就是博客的本地文件夹

<!--more-->

#### 配置 Hexo

`网站配置`： <folder>`/_config.yml`

##### 网站

| 参数                     |           描述           |
| ------------------------ | :----------------------: |
| title                    |         网站标题         |
| subtitle                 |        网站副标题        |
| description              |         网站描述         |
| author                   |         您的名字         |
| language                 |      网站使用的语言      |
| timezone                 | 网站时区(默认为电脑时区) |
| avatar(此参数需自己添加) |       网站头像地址       |

##### 网址

| 参数      |      描述      |                      默认值 |
| --------- | :------------: | --------------------------: |
| url       |      网址      |
| root      |   网站根目录   |
| permalink | 文章的链接格式 | `:year/:month/:day/:title/` |

> #### 网站存放在子目录
>
> 如果您的网站存放在子目录中，例如` http://yoursite.com/blog`，则请将您的 `url` 设为` http://yoursite.com/blog` 并把` root` 设为 `/blog/`。

##### 目录

| 参数         |                                             描述                                              |         默认值 |
| ------------ | :-------------------------------------------------------------------------------------------: | -------------: |
| source_dir   |                             资源文件夹，这个文件夹用来存放内容。                              |         source |
| public_dir   |                        公共文件夹，这个文件夹用于存放生成的站点文件。                         |         public |
| \_posts_dir  |                        默认文件夹，这个文件夹用于存放生成的站点文件。                         |        \_posts |
| tag_dir      |                                          标签文件夹                                           |           tags |
| category_dir |                                          分类文件夹                                           |     categories |
| archive_dir  |                                          归档文件夹                                           |       archives |
| about_dir    |                                        关于我们文件夹                                         |          about |
| CNAME        |                        域名文件([`zhangmiao.space`](zhangmiao.space))                         |
| code_dir     |                                      Include code 文件夹                                      | downloads/code |
| i18n_dir     |                                     国际化（i18n）文件夹                                      |          :lang |
| skip_render  | 跳过指定文件的渲染，您可使用 [`glob`](https://github.com/isaacs/node-glob) 表达式来匹配路径。 |

> #### 提示
>
> 如果您刚刚开始接触[`Hexo`](https://hexo.io/)，通常没有必要修改这一部分的值。

#### 分页

| 参数           |                描述                 | 默认值 |
| -------------- | :---------------------------------: | -----: |
| per_page       | 每页显示的文章量 (0 = 关闭分页功能) |     10 |
| pagination_dir |              分页目录               |   page |

##### 扩展

| 参数   |                描述                 |
| ------ | :---------------------------------: |
| theme  | 当前主题名称。值为 false 时禁用主题 |
| deploy |           部署部分的设置            |

> 更多网站参数参考：https://hexo.io/zh-cn/docs/configuration.html

### 添加站内搜索

安装 `hexo-generator-searchdb`

```cmd
$ npm install hexo-generator-searchdb --save
```

`网站配置`： <folder>`/_config.yml`

新增以下内容到任意位置：

```
search:
	path: search.xml
	field: post
	format: html
	limit: 10000
```

### 测试 Hexo

#### 新建文章

运行命令新建一篇文章

```cmd
$ hexo new [layout] <title>
```

#### 启动服务

```cmd
$ hexo server
  或
$ hexo s
```

| 选项         |              描述              |
| ------------ | :----------------------------: |
| -p, --port   |            重设端口            |
| -s, --static |         只使用静态文件         |
| -l, --log    | 启动日记记录，使用覆盖记录格式 |

启动服务器。默认情况下，访问网址为：http://localhost:4000/。

##### 部署网站

```cmd
$ hexo deploy
  或
$ $ hexo d
```

| 选项           |           描述           |
| -------------- | :----------------------: |
| -g, --generate | 部署之前预先生成静态文件 |

#### 清除

```cmd
$ hexo clean
```

清除缓存文件 (`db.json`) 和已生成的静态文件 (`public`)。
在某些情况（尤其是更换主题后），如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令。

> 更多命令参考：https://hexo.io/zh-cn/docs/commands.html

### 安装&配置 NexT 主题

#### 安装 NexT 主题

使用 git 克隆最新版本

```cmd
$ cd <folder>
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

或者直接将 [`hexo-theme-next`](https://github.com/iissnan/hexo-theme-next) 下载下来放到 Hexo 站点目录下的 [`themes/next`] 目录中

#### 启用 NexT 主题

`网站配置`： <folder>/`_config.yml`
搜索`theme `关键字，并将其值更改为 `next`

```
theme: next
```

#### 验证 NexT 主题

> 最好先使用 `hexo clean` 清除 Hexo 的缓存。

运行 `hexo server` 启动本地站点。
此时即可使用浏览器访问 http://localhost:4000 ，检查站点是否正确运行。

#### 主题设定

`主题配置`： <folder>/theme/next/`_config.yml`
搜索` scheme` 关键字，选择使用的主题样式，将你需用启用的 scheme 前面注释 # 去掉并将其他两个 scheme 加上注释即可。

- Muse - 默认 Scheme，这是 NexT 最初的版本，黑白主调，大量留白
- Mist - Muse 的紧凑版本，整洁有序的单栏外观
- Pisces - 双栏 Scheme，小家碧玉似的清新

```
#scheme: Muse
#scheme: Mist
scheme: Pisces
```

#### 设置 菜单

`主题配置`： <folder>/theme/next/`_config.yml`

搜索 `menu` 关键字

#### 设置 头像

`主题配置`： <folder>/theme/next/`_config.yml`

新增字段` avatar`,值设置成头像的链接地址

#### 设置 作者名称

`主题配置`： <folder>/theme/next/`_config.yml`

搜索 `author` 关键字

#### 设置 描述

`主题配置`： <folder>/theme/next/`_config.yml`

搜索 `description` 关键字

#### 设置 首页列表是否显示 阅读更多

`主题配置`： <folder>/theme/next/`_config.yml`

搜索` auto_excerpt` 关键字

将 `enable` 设置为` true`

`length` 设置为期望截取保留的文章长度

#### 集成第三方服务

`主题配置`： <folder>/theme/next/`_config.yml`

> 参考：http://theme-next.iissnan.com/third-party-services.html

### 创建 GitHub

创建好账号之后，先创建一个仓库` New repository`
进入 `Settings` ，找到下方的 `GitHub Pages` ，点击`Choose a theme`选择主题（这个无所谓，最后都会被替换），`Source`指向的就是 GitPage 站点所在的分支。

GitHub 会给分配一个二级域名，GitHub 昵称+github.io

### 部署网站

#### 安装

`hexo-deployer-git`

```cmd
$ npm install hexo-deployer-git --save
```

#### 配置

`网站配置`： <folder>/`_config.yml`

搜索 `deploy` 关键字

- type：git
- repo：github 提交地址
- branch：提交分支

#### 部署

```cmd
$ hexo deploy
  或
$ hexo d
```

如果想在部署之前预先生成下静态文件，可以使用：

```cmd
$ hexo deploy -g
  或
$ hexo d --generate
```

> `$ hexo deploy -g`与`$ hexo generate -d`的效果其实是相同的

> 本地站点不要放在 Git 上，否则执行 deploy 的时候会把本地站点提交上去
