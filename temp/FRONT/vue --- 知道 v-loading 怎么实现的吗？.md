# 标题

## 小剧场

## 前言

相信点进来的大家肯定都用过 element-UI 里面的 `v-loading` 来写加载页面，但是如果让你来写一个的话你会怎么写呢？

众所周知，element-UI 框架的 `v-loading` 有两种使用方式，一种是在需要 loading 的标签上直接使用 `:v-loading="true"`，这种方式官方称为指令，还有一种就是使用 `this.$loading(options)` 来调用，这种方式官方称为服务。

人类对于对于美好的事物总会有趋向性，笔者也不外乎如此，在我扒源码之前还没有一个确切的实现想法，但是十分钟后，我就懂了。

有些人天生就适合你，有的代码天生就适合读，优秀的开源项目都是如此，希望接下来我的解析也可以让你恍然大悟，随后窃笑一声，原来就这样。

如果能不吝打赏一个赞那更是极好。

## 正文

### 使用方式

#### 指令

来回顾一下 v-loading 的指令使用方式

```html
<template>
  <div :v-loading.fullscreen="true">全屏覆盖</div>
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

### 起点

打开你的 `node_modules`，目标 `element-ui`，在 `src` 目录下的 `index.js` 便是引入所有组件的地方，让我看看今天是哪两个小可爱要被我们看光光。

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

`Vue.use()` 这个指令是 `Vue` 用来安装插件的，如果传入的参数是一个对象，则该对象要提供一个 `install` 方法，如果是一个函数，则该函数被视为 `install` 方法，在 `install` 方法调用时，会将 `Vue` 作为参数传入。

如果要了解更多有关 `plugin`，[点击这里了解 `plugin`](https://cn.vuejs.org/v2/guide/plugins.html 'Vue.use')，尤大大的官方文档简直百读不厌。

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

接下来我们就要深入源码了！dokidoki...

### v-loading 指令解析

喝杯水，压抑住激动的心情，我们打开 `packages` 下的 `loading\index.js`，可以看到其对外曝露了 `directive` 指令，来，上路，我们来看他的源码。

看似有百来行的代码，但是客官不要着急，我给你精简一下，贴上主要代码，为了方便解说，在其中我们只取 `fullscreen` 修饰词。

```javascript
import Vue from 'vue'
// 这里就是我们写的比较多的单 .vue 文件了，不拓展开了
// 值得注意的是在这个单文件里面的 data() {} 声明的值
// 我们下面会碰到
import Loading from './loading.vue'
// 老板！Vue.extend() 是什么！
// 下面我会简单介绍一下
const Mask = Vue.extend(Loading)

const loadingDirective = {}
// 还记得 Vue.use() 的使用方法么？若为对象，则需要一个 install 属性
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
  Vue.directive('customLoading', {
    bind: function(el, binding, vnode) {
      // 创建一个子组件，这里和 new Vue(options) 类似
      // 返回一个组件实例
      const mask = new Mask({
        el: document.createElement('div'),
        // 有些人看到这里会迷惑，为什么这个 data 不按照 Vue 官方建议传函数进去呢？
        // 其实这里两者皆可
        // 稍微做一点延展好了，在 Vue 源码里面，data 是惰性求值的
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
        // 看了这段代码应该就不难理解为什么可以穿对象进去了
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
        // 切换显示
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



### 服务

## 后语

后语

## 大纲

大纲

## 页脚

代码即人生，我甘之如饴。

我在这里 [gayhub@jsjzh](https://github.com/jsjzh/blog) 欢迎大家来找我玩儿。

欢迎小伙伴们直接加我，拉你进群一起学习前端呀，记得备注一下你来自哪里哦。

<!-- ![wechat](https://i.loli.net/2019/03/11/5c867208cc9c0.jpg) -->

<!-- ![wechat](https://i.loli.net/2019/03/11/5c86720fbab10.jpg) -->
