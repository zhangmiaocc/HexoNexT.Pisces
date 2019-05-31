---
title: Weex 中使用Vue.js
tags:
  - Android
  - Weex
categories:
  - Android
  - Weex
abbrlink: 937a42e9
date: 2018-07-21 15:51:25
---

- 只含有运行时的构建版本
- 平台的差异
  - 执行环境
  - DOM
  - 样式
  - 事件
- Web 渲染器
- 单文件组件
  - 编译目标
  - 使用weex-loader
- 支持的功能
  - 全局配置
  - 全局 API
  - 选项
  - 生命周期钩子
  - 实例属性
  - 实例方法
  - 模板指令
  - 特殊属性
  - 内置组件

<!--more-->

在 WeexSDK [v0.10.0](https://github.com/alibaba/weex/releases/tag/v0.10.0) （发布于 2016 年 2 月 17 日）以及后续的版本中，集成了 v2 版本的 Vue.js。Vue 是一套构建用户界面的渐进式框架，详情请参阅其[官方网站](https://cn.vuejs.org/)。

> 如果没有特别指示，文章中的 "Vue.js" 或者 "Vue" 都指的是 v2 版本的 Vue。

## 只含有运行时的构建版本

如果你熟悉 Vue.js，你应该知道 Vue.js 有两种构建版本: [**运行时 + 编译器** 与 **只包含运行时**](https://cn.vuejs.org/v2/guide/installation.html#运行时-编译器-vs-只包含运行时)。它们之间的区别在于编译器是否需要能够在运行时编译 `template` 选项。由于运行时构建版本比完整版本的构建版本轻约 30%（Vue 官方估算），为了更好的性能和更小的代码体积，Weex 集成的是运行时版本的 Vue。

具体来说，差异如下：

- 定义组件时不支持 [`template`](https://cn.vuejs.org/v2/api/#template) 选项。
- 不支持使用 [`x-templates`](https://cn.vuejs.org/v2/guide/components.html#X-Templates)。
- 不支持使用 [`Vue.compile`](https://cn.vuejs.org/v2/api/#Vue-compile)。

## 平台的差异

Vue.js 最初是为 Web 平台设计的。虽然可以基于Weex开发原生应用程序，但是仍然存在许多[Weex 与 Web 平台的差异](https://weex.apache.org/zh/guide/platform-difference.html)。

与 Web 平台的主要差异是: 执行环境、DOM、样式和事件。

### 执行环境

Weex 主要用于编写多页的应用程序，每个页面都对应了原生开发中的 *View* 或者 *Activity*，并且保持自己的上下文。即使 Weex 的所有页面都使用的都是同一个 Javascript 引擎的实例(virtual machine)，每个页面是执行环境也是互相隔离的（基于 Sandbox 技术）。

> 使用 [BroadcastChannel](https://weex.apache.org/zh/docs/api/broadcast-channel.html) 可以实现跨页通信。

具体来讲，每个页面的 Vue 变量都是不同的实例，即使是写在 Vue 上的“全局”配置（`Vue.config.xxx`）也只会影响 Weex 上的单个页面。

在此基础上，一些 Vue 的 SPA （单页面应用）技术，如 [Vuex](https://vuex.vuejs.org/zh-cn/) 和 [vue-router](https://router.vuejs.org/zh-cn/) 也将单页内生效。更通俗地说，“页面”概念在 SPA 技术中是虚拟的，但在 Weex 上却是真实的。即便如此，Vuex 和 vue-router 都是独立的库，都有自己的概念和使用场景，仍然可以在 Weex 里[使用 Vuex 和 vue-router](https://weex.apache.org/zh/guide/advanced/use-vuex-and-vue-router.html)。

### DOM

因为在 Android 和 iOS 上没有 DOM（Document Object Model），如果你要手动操作和生成 DOM 元素的话可能会遇到一些兼容性问题。在你使用现代前端框架的情况下，操作数据与组件而不是生成的元素是一个比较好的做法。

一些与 DOM 相关的特性，比如 `v-html`，`vm.$el`，`template` 选项，在不同的平台上可能无法获得相同的反应。

准确来说，[`vm.$el`](https://cn.vuejs.org/v2/api/#vm-el)属性类型在web环境下是`HTMLElement`，但是在移动端并没有这个类型。实际上，它是一个由 *Weex 文档对象模型* 定义的特殊数据结构。

### 样式

样式表和 CSS 规则是由 Weex js 框架和原生渲染引擎管理的。要实现完整的 CSS 对象模型（CSSOM：CSS Object Model）并支持所有的 CSS 规则是非常困难的，而且没有这个必要。

出现性能考虑，**Weex 目前只支持单个类选择器，并且只支持 CSS 规则的子集**。详情请参阅 *通用样式* 与 *文本样式*。

在 Weex 里， 每一个 Vue 组件的样式都是 *scoped*。

### 事件

目前在 Weex 里不支持事件冒泡和捕获，因此 Weex 原生组件不支持[事件修饰符](https://cn.vuejs.org/v2/guide/events.html#事件修饰符)，例如`.prevent`，`.capture`，`.stop`，`.self` 。

此外，[按键修饰符](https://cn.vuejs.org/v2/guide/events.html#按键修饰符)以及[系统修饰键](https://cn.vuejs.org/v2/guide/events.html#系统修饰键) 例如 `.enter`，`.tab`，`.ctrl`，`.shift` 在移动端基本没有意义，在 Weex 中也不支持。

## Web 渲染器

如果你想在网络上呈现你的页面，你需要 [weex-vue-render](https://github.com/weexteam/weex-vue-render) 来实现它。

`weex-vue-render`是 Vue DSL 的 Web 渲染器， 它在 Web 上实现了 Weex 的内置组件和内置模块。详情请参阅[这里](https://github.com/weexteam/weex-vue-render)。

## 单文件组件

Vue 中的[单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)（即`*.vue`文件）是一种特殊的文件格式，扩展名为`.vue`。这个模板会在构建时便于到`render`函数里。

此外，所有的编辑器里都支持一个好的[语法高亮插件](https://github.com/vuejs/awesome-vue#source-code-editing)。

> 在 Weex 中使用单个文件组件语法是一种很好的做法。

TIP

在 Weex 中使用 Vue 的单个文件组件语法是一种最佳实践。

因为针对 Weex 的 Web 平台的编译工具并不一样，如果你直接写的 `render` 函数，则绕过了 weex-loader 编译模板的过程，这样的话你需要自行处理平台差异的细节。

### 编译目标

因为平台的差异以及为了提高网络性能，`*.vue`文件需要用两种不同的方式来编译：

- 对于 Web 平台来说，你可以用任何正式的方式来编译源文件，例如 使用 **Webpack + vue-loader** 或者 **Browserify + vueify** 来编译`*.vue`文件。
- 对于安卓与 iOS 平台来说， 你需要使用 [weex-loader](https://github.com/weexteam/weex-loader) 来编译`*.vue`文件。

不同的平台使用不同的`bundles`，可以充分利用平台原有的特性，减少构建时的兼容性代码。但是源代码仍然是一样的，唯一的区别是编译它的方法。

### 使用weex-loader

[weex-loader](https://github.com/weexteam/weex-loader) 是一个 webpack 的 [loader](https://webpack.js.org/concepts/loaders/#using-loaders)，它能把`*.vue`文件转化为简单的javascript 模块用于安卓以及 iOS 平台。所有的特性和配置都是跟 [vue-loader](https://vue-loader-v14.vuejs.org/zh-cn/) 一样的。

需要注意的是，如果 Webpack 的 *entry* 配置项是一个 `*.vue` 文件的话，你仍需要传递一个额外的 `entry` 参数作为标记。

```js
const webpackConfig = {
  // Add the entry parameter for the .vue file
  entry: './path/to/App.vue?entry=true'

  /* ... */

  use: {
    loaders: [{
      // matches the .vue file path which contains the entry parameter
      test: /\.vue(\?^^]+)?$/,
      loaders: ['weex-loader']
    }]
  }
}
```

**如果你现在用的是.js文件做入口文件，你不需要写那些额外的参数。** 推荐 webpack 配置的入口文件使用 javascript 文件。

```js
{
  entry: './path/to/entry.js'
}
```

TIP

无论什么情况下都使用 javascript 文件作为入口文件。

## 支持的功能

### 全局配置

> Vue “全局”配置只会影响 Weex 上的单一页面，配置不会在不同的 Weex 页面之间共享。

| Vue 全局配置                                                 | 是否支持   | 说明                |
| ------------------------------------------------------------ | ---------- | ------------------- |
| [Vue.config.silent](https://cn.vuejs.org/v2/api/#silent)     | **支持**   | -                   |
| [Vue.config.optionMergeStrategies](https://cn.vuejs.org/v2/api/#optionMergeStrategies) | **支持**   | -                   |
| [Vue.config.devtools](https://cn.vuejs.org/v2/api/#devtools) | **不支持** | 只在 Web 环境下支持 |
| [Vue.config.errorHandler](https://cn.vuejs.org/v2/api/#errorHandler) | **支持**   | -                   |
| [Vue.config.warnHandler](https://cn.vuejs.org/v2/api/#warnHandler) | **支持**   | -                   |
| [Vue.config.ignoredElements](https://cn.vuejs.org/v2/api/#ignoredElements) | **支持**   | 不推荐              |
| [Vue.config.keyCodes](https://cn.vuejs.org/v2/api/#keyCodes) | **不支持** | 在移动端无用        |
| [Vue.config.performance](https://cn.vuejs.org/v2/api/#performance) | **不支持** | 与 *devtools* 一样  |
| [Vue.config.productionTip](https://cn.vuejs.org/v2/api/#productionTip) | **支持**   | -                   |

### 全局 API

| Vue 全局 API                                                | 是否支持   | 说明                                                         |
| ----------------------------------------------------------- | ---------- | ------------------------------------------------------------ |
| [Vue.extend](https://cn.vuejs.org/v2/api/#Vue-extend)       | **支持**   | -                                                            |
| [Vue.nextTick](https://cn.vuejs.org/v2/api/#Vue-nextTick)   | **支持**   | -                                                            |
| [Vue.set](https://cn.vuejs.org/v2/api/#Vue-set)             | **支持**   | -                                                            |
| [Vue.delete](https://cn.vuejs.org/v2/api/#Vue-delete)       | **支持**   | -                                                            |
| [Vue.directive](https://cn.vuejs.org/v2/api/#Vue-directive) | **支持**   | -                                                            |
| [Vue.filter](https://cn.vuejs.org/v2/api/#Vue-filter)       | **支持**   | -                                                            |
| [Vue.component](https://cn.vuejs.org/v2/api/#Vue-component) | **支持**   | -                                                            |
| [Vue.use](https://cn.vuejs.org/v2/api/#Vue-use)             | **支持**   | -                                                            |
| [Vue.mixin](https://cn.vuejs.org/v2/api/#Vue-mixin)         | **支持**   | -                                                            |
| [Vue.version](https://cn.vuejs.org/v2/api/#Vue-version)     | **支持**   | -                                                            |
| [Vue.compile](https://cn.vuejs.org/v2/api/#Vue-compile)     | **不支持** | Weex 用的是 [只包含运行时构建](https://cn.vuejs.org/v2/guide/installation.html#运行时-编译器-vs-只包含运行时) |

### 选项

| Vue 选项                                                     | 是否支持   | 说明                                                         |
| ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ |
| [data](https://cn.vuejs.org/v2/api/#data)                    | **支持**   | -                                                            |
| [props](https://cn.vuejs.org/v2/api/#props)                  | **支持**   | -                                                            |
| [propsData](https://cn.vuejs.org/v2/api/#propsData)          | **支持**   | -                                                            |
| [computed](https://cn.vuejs.org/v2/api/#computed)            | **支持**   | -                                                            |
| [methods](https://cn.vuejs.org/v2/api/#methods)              | **支持**   | -                                                            |
| [watch](https://cn.vuejs.org/v2/api/#watch)                  | **支持**   | -                                                            |
| [el](https://cn.vuejs.org/v2/api/#el)                        | **支持**   | 在移动端`el`的值是无意义的                                   |
| [template](https://cn.vuejs.org/v2/api/#template)            | **不支持** | Weex 用的是 [只包含运行时构建](https://cn.vuejs.org/v2/guide/installation.html#运行时-编译器-vs-只包含运行时) |
| [render](https://cn.vuejs.org/v2/api/#render)                | **支持**   | 不推荐                                                       |
| [renderError](https://cn.vuejs.org/v2/api/#renderError)      | **支持**   | -                                                            |
| [directives](https://cn.vuejs.org/v2/api/#directives)        | **支持**   | -                                                            |
| [filters](https://cn.vuejs.org/v2/api/#filters)              | **支持**   | -                                                            |
| [components](https://cn.vuejs.org/v2/api/#components)        | **支持**   | -                                                            |
| [parent](https://cn.vuejs.org/v2/api/#parent)                | **支持**   | 不推荐                                                       |
| [mixins](https://cn.vuejs.org/v2/api/#mixins)                | **支持**   | -                                                            |
| [extends](https://cn.vuejs.org/v2/api/#extends)              | **支持**   | -                                                            |
| [provide/inject](https://cn.vuejs.org/v2/api/#provide-inject) | **支持**   | 不推荐                                                       |
| [name](https://cn.vuejs.org/v2/api/#name)                    | **支持**   | -                                                            |
| [delimiters](https://cn.vuejs.org/v2/api/#delimiters)        | **支持**   | 不推荐                                                       |
| [functional](https://cn.vuejs.org/v2/api/#functional)        | **支持**   | -                                                            |
| [model](https://cn.vuejs.org/v2/api/#model)                  | **支持**   | -                                                            |
| [inheritAttrs](https://cn.vuejs.org/v2/api/#inheritAttrs)    | **支持**   | -                                                            |
| [comments](https://cn.vuejs.org/v2/api/#comments)            | **不支持** | -                                                            |

### 生命周期钩子

Vue 组件的实例生命周期钩子将在特定的阶段发出，详情请参考 Vue 组件的[生命周期图示](https://cn.vuejs.org/v2/guide/instance.html#生命周期图示)。

| Vue 生命周期钩子                                            | 是否支持   | 说明                                  |
| ----------------------------------------------------------- | ---------- | ------------------------------------- |
| [beforeCreate](https://cn.vuejs.org/v2/api/#beforeCreate)   | **支持**   | -                                     |
| [created](https://cn.vuejs.org/v2/api/#created)             | **支持**   | -                                     |
| [beforeMount](https://cn.vuejs.org/v2/api/#beforeMount)     | **支持**   | -                                     |
| [mounted](https://cn.vuejs.org/v2/api/#mounted)             | **支持**   | 和 Web 端不完全一样（下文有详解）     |
| [beforeUpdate](https://cn.vuejs.org/v2/api/#beforeUpdate)   | **支持**   | -                                     |
| [updated](https://cn.vuejs.org/v2/api/#updated)             | **支持**   | -                                     |
| [activated](https://cn.vuejs.org/v2/api/#activated)         | **不支持** | 不支持`<keep-alive>`                  |
| [deactivated](https://cn.vuejs.org/v2/api/#deactivated)     | **不支持** | 不支持`<keep-alive>`                  |
| [beforeDestroy](https://cn.vuejs.org/v2/api/#beforeDestroy) | **支持**   | -                                     |
| [destroyed](https://cn.vuejs.org/v2/api/#destroyed)         | **支持**   | -                                     |
| [errorCaptured](https://cn.vuejs.org/v2/api/#errorCaptured) | **支持**   | 在 Vue 2.5.0+， Weex SDK 0.18+ 中新增 |

关于 "mounted" 生命周期

和浏览不同的是，Weex 的渲染流程是**异步**的，而且渲染出来的结果都是原生系统中的 View，这些数据都无法被 javascript 直接获取到。因此在 Weex 上，Vue 的 `mounted` 生命周期在当前组件的 virtual-dom (Vue 里的 VNode) 构建完成后就会触发，此时相应的原生视图未必已经渲染完成。

### 实例属性

| Vue 实例属性                                                 | 是否支持 | 说明                    |
| ------------------------------------------------------------ | -------- | ----------------------- |
| [vm.$data](https://cn.vuejs.org/v2/api/#vm-data)             | **支持** | -                       |
| [vm.$props](https://cn.vuejs.org/v2/api/#vm-props)           | **支持** | -                       |
| [vm.$el](https://cn.vuejs.org/v2/api/#vm-el)                 | **支持** | 移动端没有`HTMLElement` |
| [vm.$options](https://cn.vuejs.org/v2/api/#vm-options)       | **支持** | -                       |
| [vm.$parent](https://cn.vuejs.org/v2/api/#vm-parent)         | **支持** | -                       |
| [vm.$root](https://cn.vuejs.org/v2/api/#vm-root)             | **支持** | -                       |
| [vm.$children](https://cn.vuejs.org/v2/api/#vm-children)     | **支持** | -                       |
| [vm.$slots](https://cn.vuejs.org/v2/api/#vm-slots)           | **支持** | -                       |
| [vm.$scopedSlots](https://cn.vuejs.org/v2/api/#vm-scopedSlots) | **支持** | -                       |
| [vm.$refs](https://cn.vuejs.org/v2/api/#vm-refs)             | **支持** | -                       |
| [vm.$isServer](https://cn.vuejs.org/v2/api/#vm-isServer)     | **支持** | 永远是`false`           |
| [vm.$attrs](https://cn.vuejs.org/v2/api/#vm-attrs)           | **支持** | -                       |
| [vm.$listeners](https://cn.vuejs.org/v2/api/#vm-listeners)   | **支持** | -                       |

### 实例方法

| Vue 实例方法                                                 | 是否支持   | 说明                      |
| ------------------------------------------------------------ | ---------- | ------------------------- |
| [vm.$watch()](https://cn.vuejs.org/v2/api/#vm-watch)         | **支持**   | -                         |
| [vm.$set()](https://cn.vuejs.org/v2/api/#vm-set)             | **支持**   | -                         |
| [vm.$delete()](https://cn.vuejs.org/v2/api/#vm-delete)       | **支持**   | -                         |
| [vm.$on()](https://cn.vuejs.org/v2/api/#vm-on)               | **支持**   | -                         |
| [vm.$once()](https://cn.vuejs.org/v2/api/#vm-once)           | **支持**   | -                         |
| [vm.$off()](https://cn.vuejs.org/v2/api/#vm-off)             | **支持**   | -                         |
| [vm.$emit()](https://cn.vuejs.org/v2/api/#vm-emit)           | **支持**   | -                         |
| [vm.$mount()](https://cn.vuejs.org/v2/api/#vm-mount)         | **不支持** | 你不需要手动安装 Vue 实例 |
| [vm.$forceUpdate()](https://cn.vuejs.org/v2/api/#vm-forceUpdate) | **支持**   | -                         |
| [vm.$nextTick()](https://cn.vuejs.org/v2/api/#vm-nextTick)   | **支持**   | -                         |
| [vm.$destroy()](https://cn.vuejs.org/v2/api/#vm-destroy)     | **支持**   | -                         |

### 模板指令

| Vue 指令                                            | 是否支持   | 说明                                                         |
| --------------------------------------------------- | ---------- | ------------------------------------------------------------ |
| [v-text](https://cn.vuejs.org/v2/api/#v-text)       | **支持**   | -                                                            |
| [v-html](https://cn.vuejs.org/v2/api/#v-html)       | **不支持** | Weex 中没有 HTML 解析器，这不是很好的实现                    |
| [v-show](https://cn.vuejs.org/v2/api/#v-show)       | **不支持** | 不支持 `display: none;`                                      |
| [v-if](https://cn.vuejs.org/v2/api/#v-if)           | **支持**   | -                                                            |
| [v-else](https://cn.vuejs.org/v2/api/#v-else)       | **支持**   | -                                                            |
| [v-else-if](https://cn.vuejs.org/v2/api/#v-else-if) | **支持**   | -                                                            |
| [v-for](https://cn.vuejs.org/v2/api/#v-for)         | **支持**   | -                                                            |
| [v-on](https://cn.vuejs.org/v2/api/#v-on)           | **支持**   | 不支持[事件修饰符](https://cn.vuejs.org/v2/guide/events.html#事件修饰符) |
| [v-bind](https://cn.vuejs.org/v2/api/#v-bind)       | **支持**   | -                                                            |
| [v-model](https://cn.vuejs.org/v2/api/#v-model)     | **支持**   | -                                                            |
| [v-pre](https://cn.vuejs.org/v2/api/#v-pre)         | **支持**   | -                                                            |
| [v-cloak](https://cn.vuejs.org/v2/api/#v-cloak)     | **不支持** | 只支持单类名选择器                                           |
| [v-once](https://cn.vuejs.org/v2/api/#v-once)       | **支持**   | -                                                            |

### 特殊属性

| Vue 特殊属性                                          | 是否支持 | 说明                                  |
| ----------------------------------------------------- | -------- | ------------------------------------- |
| [key](https://cn.vuejs.org/v2/api/#key)               | **支持** | -                                     |
| [ref](https://cn.vuejs.org/v2/api/#ref)               | **支持** | -                                     |
| [slot](https://cn.vuejs.org/v2/api/#slot)             | **支持** | -                                     |
| [slot-scope](https://cn.vuejs.org/v2/api/#slot-scope) | **支持** | 在 Vue 2.5.0+， Weex SDK 0.18+ 中新增 |
| [scope](https://cn.vuejs.org/v2/api/#scope)           | **支持** | 不推荐                                |
| [is](https://cn.vuejs.org/v2/api/#is)                 | **支持** | -                                     |

### 内置组件

| Vue 内置组件                                                 | 是否支持   | 说明                                                         |
| ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ |
| [component](https://cn.vuejs.org/v2/api/#component)          | **支持**   | -                                                            |
| [transition](https://cn.vuejs.org/v2/api/#transition)        | **不支持** | 在移动端 *enter* 与 *leave* 的概念可能有点不同， 并且 Weex 不支持`display: none;` |
| [transition-group](https://cn.vuejs.org/v2/api/#transition-group) | **不支持** | 跟 *transition* 一样                                         |
| [keep-alive](https://cn.vuejs.org/v2/api/#keep-alive)        | **不支持** | 移动端的原生组件不能被前端缓存                               |
| [slot](https://cn.vuejs.org/v2/api/#slot)                    | **支持**   | -                                                            |

