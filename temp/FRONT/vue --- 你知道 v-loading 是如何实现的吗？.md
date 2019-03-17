# element-ui 源码解析，你知道 v-loading 是如何实现的吗？

## 前言

相信大家肯定都用过 element-ui 里面的 `v-loading` 来写加载，但是如果让你来写一个的话你会怎么写呢？

众所周知，element-ui 框架的 `v-loading` 有两种使用方式，一种是在需要 loading 的标签上直接使用 `:v-loading='true'`，这种方式官方称为指令，还有一种就是使用 `this.$loading(options)` 来调用，这种方式官方称之为服务。

人类对于对于美好的事物总会有趋向性，我也不外乎如此，话不多说，直接扒源码。

有些人天生就适合你，有的代码天生就适合阅读，优秀的开源项目都是如此，希望接下来我的解析也可以让你恍然大悟，随后窃笑一声，原来就这样。

## 正文

### 使用方式

#### 指令

来回顾一下 `v-loading` 的指令使用方式

```html
<template>
  <div :v-loading.fullscreen='true'>全屏覆盖</div>
</template>
```

#### 服务

再来看看服务的使用方式

```javascript
mounted() {
  let loading = this.$loading({ fullscreen: true })
  setTimeout(() => { loading.close() }, 1000)
}
```

心里稍微留点印象，我们简单粗暴一些，直接扒源码吧。

### 起点

打开你的 `node_modules`，目标 `element-ui`，在 `src` 目录下的 `index.js` 便是引入所有组件的地方，让我看看今天是哪两个小可爱要被我们扒光光。

```javascript
// element-ui\src\index.js
// ...
// directive 指令装载
Vue.use(Loading.directive)
// prototype 服务装载
Vue.prototype.$loading = Loading.service
// ...
```

本着循序渐进的原则，我们先从使用较多的指令方式看起。

`Vue.use()` 这个指令是 Vue 用来安装插件的，如果传入的参数是一个对象，则该对象要提供一个 `install` 方法，如果是一个函数，则该函数被视为 `install` 方法，在 `install` 方法调用时，会将 `Vue` 作为参数传入。

如果要了解更多有关 plugin，[点击这里了解 plugin](https://cn.vuejs.org/v2/guide/plugins.html 'Vue.use')，尤大大的官方文档简直百读不厌。

但是老板！这 Loading 下的两个是啥玩意儿呢！

来，我们看这里。

```javascript
import directive from './src/directive'
import service from './src/index'

export default {
  // 这里为什么有个 install 呢
  // 当你使用单组件单注册的时候就会调用这里了
  // 效果和下面一样，挂载指令，挂载服务
  install(Vue) {
    Vue.use(directive)
    Vue.prototype.$loading = service
  },
  // 就是上面的 Loading.directive
  directive,
  // 就是上面的 Loading.service
  service
}
```

接下来我们终于要深入源码了！dokidoki...

### `v-loading` 指令解析

喝杯水，压抑住激动的心情，我们打开 `packages` 下的 `loading\index.js`，可以看到其对外曝露了 `directive` 指令，来，上路，我们来看他的源码。

看似有百来行的代码，但是客官不要着急，我给你精简一下，贴上主要代码，为了方便解说，在其中我们只取 `fullscreen` 修饰词。

```javascript
import Vue from 'vue'
// 这里就是我们写的比较多的单 .vue 文件了，不拓展开了
// 值得注意的是在这个单文件里面的 data() {} 声明的值
// 我们下面会碰到
import Loading from './loading.vue'
// 老板！Vue.extend() 是什么！
// 代码片段之后我会简单介绍
const Mask = Vue.extend(Loading)

const loadingDirective = {}
// 还记得 Vue.use() 的使用方法么？若传入的是对象，该对象需要一个 install 属性
loadingDirective.install = Vue => {
  // 这里处理显示、消失 loading
  const toggleLoading = (el, binding) => {
    // 若绑定值为 truthy 则插入 loading 元素
    // binding 值为 directive 的几个钩子中会接受到的参数
    if (binding.value) {
      if (binding.modifiers.fullscreen) {
        insertDom(document.body, el, binding)
      }
      // 不然则将其设为不可见
      // 从上往下读我们是第一次看到 visible 属性
      // 别急，往下看，这个属性可以其实就是单文件 loading.vue 里面的
      // data() { return { visible: false } }
    } else {
      el.instance.visible = false
    }
  }

  const insertDom = (parent, el, binding) => {
    // 将 loading 设为可见
    el.instance.visible = true
    // appendChild 添加的元素若为同一个，则不会重复添加
    // 我们 el.mask 所指的为同一个 dom 元素
    // 因为我们只在 bind 的时候给 el.mask 赋值
    // 并且在组件存在期间，bind 只会调用一次
    parent.appendChild(el.mask)
  }
  // 在此注册 directive 指令
  Vue.directive('loading', {
    bind: function(el, binding, vnode) {
      // 创建一个子组件，这里和 new Vue(options) 类似
      // 返回一个组件实例
      const mask = new Mask({
        el: document.createElement('div'),
        // 有些人看到这里会迷惑，为什么这个 data 不按照 Vue 官方建议传函数进去呢？
        // 其实这里两者皆可
        // 稍微做一点延展好了，在 Vue 源码里面，data 是延迟求值的
        // 贴一点 Vue 源码上来
        // return function mergedInstanceDataFn() {
        //   let instanceData = typeof childVal === 'function'
        //     ? childVal.call(vm, vm)
        //     : childVal;
        //   let defaultData = typeof parentVal === 'function'
        //     ? parentVal.call(vm, vm)
        //     : parentVal;
        //   if (instanceData) {
        //     return mergeData(instanceData, defaultData)
        //   } else {
        //     return defaultData
        //   }
        // }
        // instanceData 就是我们现在传入的 data: {}
        // defaultData 就是我们 loading.vue 里面的 data() {}
        // 看了这段代码应该就不难理解为什么可以传对象进去了
        data: {
          fullscreen: !!binding.modifiers.fullscreen
        }
      })
      // 将创建的子类挂载到 el 上
      // 在 directive 的文档中建议
      // 应该保证除了 el 之外其他参数（binding、vnode）都是只读的
      el.instance = mask
      // 挂载 dom
      el.mask = mask.$el
      // 若 binding 的值为 truthy 运行 toogleLoading
      binding.value && toggleLoading(el, binding)
    },
    update: function(el, binding) {
      // 若旧不等于新值得时候（一般都是由 true 切换为 false 的时候）
      if (binding.oldValue !== binding.value) {
        // 切换显示或消失
        toggleLoading(el, binding)
      }
    },
    unbind: function(el, binding) {
      // 当组件 unbind 的时候，执行组件销毁
      el.instance && el.instance.$destroy()
    }
  })
}
export default loadingDirective
```

#### `Vue.extend` 是什么

在平时的代码中该方法我们主动调用的不多，但是在我们注册组件的时候，比如，`Vue.component('my-component', options)`，这个时候会自动调用 `Vue.extend`，直接上源码吧，该代码是当调用 `Vue.component()` 的时候将会执行的。

ps：稍微做一下延展，对于 `.vue` 单文件，想必大家都能猜到是如何运作的了，首先会把 `<template>` 标签的内容转为 `render()` 函数，说到这个 `render()` 函数，我又想安利一波 JSX 了（打住！），然后就接着走 `Vue.component()` 这条线路注册组件了。

```javascript
...
if (type === 'component' && isPlainObject(definition)) {
  definition.name = definition.name || id
  definition = this.options._base.extend(definition)
}
...
```

而在 `extend` 里面又做了什么事情呢？我很想直接贴上源码再来分析，但是这样就超出我们今天要说的范围了，一言以蔽之， `Vue.extend` 接受参数并返回一个构造器，`new` 该构造器可以返回一个组件实例。

相信到了这里大家已经对如何实现 `v-loading` 有了一定的了解了，勤快的小伙伴已经卷了袖子开干了，但是文章还没完，分析完指令模式我们还要看看服务模式，稍作休息，上路。

### 服务

如果打开开发者模式看过两种 `loading` 的方式，应该会注意到指令模式和服务模式的区别，最直观的就是若有 `fullscreen` 参数，指令模式下不会移除生成的 `dom` 元素，而在服务模式下会移除生成的 `dom` 元素。

我的废话太多了，直接上源码，同样的，我会提取我们需要关注的代码片段方便分析，有了指令模式的基础，看服务模式的就没什么难度了。

```javascript
import Vue from 'vue'
import loadingVue from './loading.vue'
// 和指令模式一样，创建实例构造器
const LoadingConstructor = Vue.extend(loadingVue)
// 定义变量，若使用的是全屏 loading 那就要保证全局的 loading 只有一个
let fullscreenLoading
// 这里可以看到和指令模式不同的地方
// 在调用了 close 之后就会移除该元素并销毁组件
LoadingConstructor.prototype.close = function() {
  setTimeout(() => {
    if (this.$el && this.$el.parentNode) {
      this.$el.parentNode.removeChild(this.$el)
    }
    this.$destroy()
  }, 3000)
}

const Loading = (options = {}) => {
  // 若调用 loading 的时候传入了 fullscreen 并且 fullscreenLoading 不为 falsy
  // fullscreenLoading 只会在下面赋值，并且指向了 loading 实例
  if (options.fullscreen && fullscreenLoading) {
    return fullscreenLoading
  }
  // 这里就不用说了吧，和指令中是一样的
  let instance = new LoadingConstructor({
    el: document.createElement('div'),
    data: options
  })
  let parent = document.body
  // 直接添加元素
  parent.appendChild(instance.$el)
  // 将其设置为可见
  // 另外，写到这里的时候我查阅了相关的资料
  // 自己以前一直理解 nextTick 是在 dom 元素更新完毕之后再执行回调
  // 但是发现可能并不是这么回事，后续我会继续研究
  // 如果干货足够的话我会写一篇关于 nextTick ui-render microtask macrotask 的文章
  Vue.nextTick(() => {
    instance.visible = true
  })
  // 若传入了 fullscreen 参数，则将实例存储
  if (options.fullscreen) {
    fullscreenLoading = instance
  }
  // 返回实例，方便之后能够调用原型上的 close() 方法
  return instance
}
export default Loading
```

关于代码里的 `fullscreenLoading` 变量，根由 `element-ui` 官方的说明我们应该能了解个大概，这是为了保证覆盖整个页面的 loading 实例只有一个才存在的，官方文档说明如下。

> 需要注意的是，以服务的方式调用的全屏 Loading 是单例的：若在前一个全屏 Loading 关闭前再次调用全屏 Loading，并不会创建一个新的 Loading 实例，而是返回现有全屏 Loading 的实例

## 后语

至此文章主体内容已经结束了，看起来只是一个 `v-loading` 的功能但却延伸出去很多的内容，我还精简了很多代码，所以我这只是管中窥豹很小的一部分内容，更多的内容推荐大家也去读读源码，你会发现不一样的世界。

有些人和我说，看源码的时候一看开头，十来个模块引入，一看组件，百来行，瞬间就软掉了，没有看下去的欲望了，但怎么说呢，这个时候我推荐可以先从你有些了解的功能开始看起，比如 `v-loading`，看到这个就知道是 `directive`，再源码里看的时候关注关键点，比如 `binding`、`el`、`vnode`，很快就能理清楚代码的含义了。

**如果身边有更优秀的人那还好，可以以他为目标，但是当你一枝独秀，身边的人都不如自己，你不知自己该如何成长的时候，这个时候就应该去看看源码，从源码中学习，从源码提升自己。**

> 该如何看待阅读？一天的饭钱就能买到别人可能一辈子的心血，多么值钱的买卖。--- 无名

虽然源码可能不能称作是一辈子的心血，但是想象一下，看源码的过程中就好像和作者面对面在交谈，这个模块怎么安排，那个算法怎么优化，看 `Vue` 的源码更是了，好像面对面和尤大大在聊天，这这这，想想就湿了啊！（眼角，为什么？因为太感动了）

这篇文章只是我在和大家分享一些优秀的人的心血，结合了一些自己的理解，希望大家看完整篇文章之后能够有一些收获有一些沉淀，当然卷起袖子直接写一个自己的 `v-custom-loading` 就更好了。

我在平时看源码的过程中会发现不少有意思的代码，如果有兴趣的话可以来我的项目里看看，里面就有我自己写的 `v-custom-loading`，还结合了一些自己的想法，比如将组件实例挂载的时候，我推荐如下写法。

```javascript
const context = '@@loadingContext'

...
el[context] = { instance: mask }
...
```

这么写的原因就是不要污染 `el` 元素本身有的属性，毕竟有可能自己定义的属性会和 `dom` 原有的属性冲突。

另外我在 [vue-element-admin](https://github.com/PanJiaChen/vue-element-admin) 项目中提了 `pr` 用的就是这种写法。具体可以看 [vue-element-admin issue 1704](https://github.com/PanJiaChen/vue-element-admin/issues/1704) 和 [vue-element-admin issue 1705](https://github.com/PanJiaChen/vue-element-admin/pull/1705)。

ps：最近面试题泛滥，我想如果把标题改成《面试题解析，你知道 v-loading 该如何实现么？》会不会更有人关注一些？（狗头保平安）

## 页脚

代码即人生，我甘之如饴。

我在这里 [gayhub@jsjzh](https://github.com/jsjzh/blog) 欢迎大家来找我玩儿。

小伙伴们可以直接加我或者加群，我们一起学前端、看源码、学算法，前端进阶，加油。

![wechat](https://i.loli.net/2019/03/11/5c867208cc9c0.jpg)

![wechat](https://i.loli.net/2019/03/17/5c8e0dfc3dadf.jpg)
