# React Hook

## 前言

昨天将 React 发布 Hook 的视频[【React Conf 2018】React 的今天和明天中英文双字幕】](https://www.bilibili.com/video/BV1wt411X7e3?p=2&share_source=copy_web&vd_source=fcf62b093541511bddbc5cf94f7c8afe)看了下，主要是 Dan 在讲述与 class 组件相比，hook 在各个方面的不同点，state、context 和 effect 这三个基本 hookAPI，说下我的感受吧

之前在学习 Vue 时了解到了有 react，在尝试后确实有点无法诉说的舒服感，一种我在创造的感觉，而 Vue 给我的感觉有点糟糕，我反复去在前面的 template、script 和 style 上反复折腾，我写起来总有种，我先跑到这里叮叮咣咣的敲几下，就得跑到另一个地方换个工具再叮叮咣咣的敲，一种频繁被打断、被折腾的感觉，束缚感极强。

而 react 则是另外一种感觉，感觉我只需要做我想到的事、想做的事，而不是被束缚的去这工，当然，我觉得这个束缚感有可能对一些人反而是一个安全的保障，一个可以产出的基石，反观 react，则是将这个自由交出去了，react 需要最佳实践，也需要不断更新，就像 Dan 在视频最后对 react 和它的图标的理解，电子本以为已经是最小的物质单位，但是它其实还是可以继续细分，就像 react 的 hook，它可能本身就存在，只是到了今天才被发现使用，等于说 react 是一个通往未知世界的大门，不断探索，没有重点。

而 Vue，可能更加贴近流行、主流，一个简易的，方便入手的工具存在，我个人实在是不喜欢，最终我选择无论如何，react 都是我入门乃至坚持的前端框架，这个可能会变，就像 react 不断探索新的领域一样，但不是今天。

## 正文

react hook，我的理解现在仍然只是一句话，有状态的逻辑，保留了 js 中 class 的数据和方法，但是并未有 class 的复杂而又繁琐的实现方法，将逻辑抽离出来，用组合的形式实现复用而不是继承。在视频里 Dan 说过 hook 的逻辑和思想与之前 class 并不一致，而这种转变是比较艰难的，但是这个是未来，其实这个我倒是没有懂，估计是因为我一开始就用 react hook 入门，而不是 class，但是我对于这个 hook 思想很有兴趣，待我了解一番后再来续写
