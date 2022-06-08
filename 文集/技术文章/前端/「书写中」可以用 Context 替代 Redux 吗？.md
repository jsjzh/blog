# 「编辑中」可以用 Context 替代 Redux 吗？

<div align="center">
  <image src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d45a010de01a413ab7101a4479b1ad5e~tplv-k3u1fbpfcp-zoom-1.image" />
</div>

## 大纲「待删」

- 介绍 Context
  - Context + useReducer 可否实现 Redux
  - class 的用法、函数组件的用法
  <!-- - useMemo 是否有效，并没有效果（这个展开来说有点多，React.memo 和 useMemo 我还没搞懂呢） -->
- 对比 Redux
  - Redux 的日常使用方法
- Context 存在的问题
- Redux v6 的思考与改变
- useContextSelector
  - 如何使用
  - 不足（引用）
  - 源码解析
  - react Context 未来展望
- Redux Toolkit 该如何使用
- Demo 的源码地址

```jsx
// 可以计算组件重渲染的次数
import { useRef } from "react";
export default function Counts() {
  const renderCount = useRef(0);
  return (
    <div className="mt-3">
      <p className="dark:text-white">
        Nothing has changed here but I've now rendered:{" "}
        <span className="dark:text-green-300 text-grey-900">
          {renderCount.current++} time(s)
        </span>
      </p>
    </div>
  );
}
```

> 多来点英文的引用
> 记得把 Demo 里面的组件都改成自己的命名方式

## 小剧场

「TODO」

## 前言

「TODO」

## 正文标题一

「TODO」

## 正文标题二

「TODO」

## 正文标题三

「TODO」

## 后语

「TODO」

## 页脚

代码即人生，我甘之如饴。

> 技术不断在变  
> 头脑一直在线  
> 前端路漫漫  
> 我们下期见
>
> by --- 裤裆三重奏

历史文章选集，有兴趣的可以看看哦

- [gayhub@jsjzh](https://github.com/jsjzh)
- [gayhub@jsjzh](https://github.com/jsjzh)
- [gayhub@jsjzh](https://github.com/jsjzh)
- [gayhub@jsjzh](https://github.com/jsjzh)
- [gayhub@jsjzh](https://github.com/jsjzh)

我在这里 [gayhub@jsjzh](https://github.com/jsjzh) 欢迎大家来找我玩儿。如果你看完文章之后有所收获，想要夸夸我，[**一个 Star ✨**](https://github.com/jsjzh/blog)就是对我最好的鼓励。

欢迎小伙伴们直接加我 vx，拉你进群一起搞事情，记得备注一下你是从哪里看到文章的。

> ps: 如果图片失效，可以加我 wechat: kimimi_king

<div align="center">
  <image src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/53fb3e16b1f64ebbb8aee73734371257~tplv-k3u1fbpfcp-watermark.image" />
</div>

## 草稿
