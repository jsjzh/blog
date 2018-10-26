
![Vue](https://user-gold-cdn.xitu.io/2018/5/23/16388a8621702f70?w=400&h=400&f=png&s=3451)
## 前言 ##
最近开发的页面以及功能大都以表格为主，接口获取来的 `JSON` 数据大都是需要经过处理，比如时间戳需要转换，或者状态码的转义。对于这样的问题，各大主流框架都提供了类似于过滤的方法，在 `Vue` 中，一般是在页面上定义 `filter` 然后在模板文件中使用 `|` 进行处理。

这种方法和以前的遍历数组洗数据是方便了许多，但是，当我发现在许多的页面都有相同的 `filter` 的时候，每个页面都要复制一遍就显的很蛋疼，遂决定用 `Vue.mixin()` 实现**一次代码，无限复用**。

最后，还可以将所有的 `filter` 包装成一个 `vue` 的插件，使用的时候调用 `Vue.use()` 即可，甚至可以上传 `npm` 包，开发不同的项目的时候可以直接 `install` 使用。（考虑到最近更新的比较快，遂打包上传这步骤先缓缓，等版本稍微稳定了之后来补全）

## 正文 ##

闲话说够，开始正题。

### Vue.mixin 为何物 ###
学习一个新的框架或者 `API` 的时候，最好的途径就是上官网，这里附上 [Vue.mixin()[点我查看]](https://cn.vuejs.org/v2/guide/mixins.html#%E5%85%A8%E5%B1%80%E6%B7%B7%E5%85%A5) 官方说明。

一句话解释，`Vue.mixin()` 可以把你创建的自定义方法混入**所有的** `Vue` 实例。

示例代码
```javascript
Vue.mixin({
  created: function(){
    console.log("success")
  }
})
```
跑起你的项目，你会发现在控制台输出了一坨 `success`。

效果出来了意思也就出来了，所有的 `Vue` 实例的 `created` 方法都被**改成**了我们自定义的方法。

### 使用 Vue.mixin() ###
接下来的思路很简单，我们整合所有的 `filter` 函数到一个文件，在 `main.js` 中引入即可。

在上代码之前打断一下，代码很简单，但是我们可以写的更加规范化，关于如何做到规范，在 `Vue` 的官网有比较详细的 [风格指南[点我查看]](https://cn.vuejs.org/v2/style-guide/#%E7%A7%81%E6%9C%89%E5%B1%9E%E6%80%A7%E5%90%8D-%E5%BF%85%E8%A6%81) 可以参考。

因为我们的自定义方法会在所有的实例中混入，如果按照以前的方法，难免会有覆盖原先的方法的危险，按照官方的建议，混入的自定义方法名增加前缀 `$_` 用作区分。

创建一个 `config.js` 文件，用于保存状态码对应的含义，将其暴露出去
```javascript
export const typeConfig = {
  1: "type one",
  2: "type two",
  3: "type three"
}
```
再创建一个 `filters.js` 文件，用于保存所有的自定义函数
```javascript
import { typeConfig } from "./config"
export default {
  filters: {
    $_filterType: (value) => {
      return typeConfig[value] || "type undefined"
    }
  }
}
```
最后，在 `main.js` 中引入我们的 `filters` 方法集
```javascript
import filter from "./filters"
Vue.mixin(filter)
```
接下来，我们就可以在 `.vue` 的模板文件中随意使用自定义函数了
```javascript
<template>
  <div>{{typeStatus | $_filterType}}<div>
</template>
```

### 包装插件 ###
接下来简单应用一下 `Vue` 中插件的制作方法。创建插件之后，就可以 `Vue.use(myPlugin)` 来使用了。

首先附上插件的 [官方文档[点我查看]](https://cn.vuejs.org/v2/guide/plugins.html)。

一句话解释，包装的插件需要一个 `install` 的方法将插件装载到 `Vue` 上。

关于 `Vue.use()` 的源码
```javascript
function initUse (Vue) {
  Vue.use = function (plugin) {
    var installedPlugins = (this._installedPlugins || (this._installedPlugins = []));
    if (installedPlugins.indexOf(plugin) > -1) {
      return this
    }

    // additional parameters
    var args = toArray(arguments, 1);
    args.unshift(this);
    if (typeof plugin.install === 'function') {
      plugin.install.apply(plugin, args);
    } else if (typeof plugin === 'function') {
      plugin.apply(null, args);
    }
    installedPlugins.push(plugin);
    return this
  };
}
```
很直观的就看到他在最后调用了 `plugin.install` 的方法，我们要做的就是处理好这个 `install` 函数即可。

上代码

`config.js` 文件依旧需要，这里保存了所有状态码对应的转义文字

创建一个 `myPlugin.js` 文件，这个就是我们编写的插件
```javascript
import { typeConfig } from "./config"

myPlugin.install = (Vue) => {
  Vue.mixin({
    filters: {
      $_filterType: (value) => {
        return typeConfig[value] || "type undefined"
      }
    }
  })
}
export default myPlugin
```
插件的 `install` 函数的第一个参数为 `Vue` 的实例，后面还可以传入一些自定义参数。

在 `main.js` 文件中，我们不用 `Vue.mixin()` 转而使用 `Vue.use()` 来完成插件的装载。
```javascript
import myPlugin from "./myPlugin"
Vue.use(myPlugin)
```
至此，我们已经完成了一个小小的插件，并将我们的状态码转义过滤器放入了所有的 Vue 实例中，在 `.vue` 的模板文件中，我们可以使用 {{ typeStatus | $_filterType }} 来进行状态码转义了。

## 结语 ##
`Vue.mixin()` 可以将自定义的方法混入**所有的** `Vue` 实例中。

本着快速开发的目的，一排脑门想到了这个方法，但是这是否是一个好方法有待考证，虽然不是说担心会对原先的代码造成影响，但是**所有的** `Vue` 实例也包括了**第三方模板**。

**本文可以随意转载，只要附上原文地址即可。**

**如果您认为我的博文对您有所帮助，请不吝赞赏，点赞也是让我动力满满的手段 =3=。**

## 待完善 ##
### 发布 npm 包 ###

## 新增项 ##
### 在 v-html 中骚骚的使用 filter （2018年05月28日） ###

最近碰到一个问题，也是关于状态码过滤的，但是实现的效果是希望通过不同的状态码显示不同的 `icon` 图标，这个就不同于上面的文本过滤了，上文使用的 `{{ styleStatus | $_filterStyleStatus }}` 是利用 `v-text` 进行渲染，若碰到需要渲染 `html` 标签就头疼了。

按照前人的做法，是这样的，我随意上一段代码。
```html
...
<span v-if="item.iconType === 1" class="icon icon-up"></span>
<span v-if="item.iconType === 2" class="icon icon-down"></span>
<span v-if="item.iconType === 3" class="icon icon-left"></span>
<span v-if="item.iconType === 4" class="icon icon-right"></span>
...
```
不！这太不 fashion 太不 cool，我本能的拒绝这样的写法，但是，问题还是要解决，我转而寻找他法。

在看 `Vue` 的文档的时候，其中一个 `API` `$options` [官方文档[点我查看]](https://cn.vuejs.org/v2/api/#vm-options) 就引起了我的注意，我正好需要一个可以访问到当前的 `Vue` 实例的 `API`，一拍脑袋，方案就成了。

首先，还是在 `config.js` 文件中定义一个状态码对应对象，这里我们将其对应的内容设为 `html` 段落。
```javascript
export const iconStatus = {
  1: "<span class='icon icon-up'></span>",
  2: "<span class='icon icon-down'></span>",
  3: "<span class='icon icon-left'></span>",
  4: "<span class='icon icon-right'></span>"
}
```
接着，我们在 `filters.js` 文件中引入他，写法还是和以前的 `filters` 一样。
```javascript
import { iconStatus } from "./config"
export default {
  $_filterIcon: (value) => {
      return iconStatus[value] || "icon undefined"
  }
}
```
重头戏在这里，我们在模板文件中需要渲染的地方，使用 `v-html` 来进行渲染。
```html
<span v-html="$options.filters.$_filterIcon(item.iconType)"></span>
```

大功告成，以后需要根据状态码来渲染 `icon` 的地方都可以通过这个办法来完成了。

事实证明，懒并不一定是错误的，关键看懒的方向，虽然本篇博客写的标题是 **偷懒** ，但其实我只是对于重复性的无意义的搬运代码而感到厌烦，在寻找对应解决办法的时候可是一点都没偷懒，相反的，在寻求更快更好更方便的方法的时候，我逐渐找回了当初敲代码的乐趣。