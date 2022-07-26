# React-Dnd

## 概述

一个 React 的拖放库

这是一个拖拽动画库，是一个采用数据驱动的拖拽库，简单来说，他的思路是不再 dom 节点或者传统的拖拽，而是数据，从官网的概述来看（我的英语实在糟糕，翻译全靠翻译软件），通过监视器来时刻监控组件的运动数据，并反馈给组件参数。

其中 Monitors（监控）、connectors（连接）、Drag Sources（拖拽源）和 DropTargets（拖拽目标）是实现的逻辑

## 我的理解

这里说说我的理解

它有些概念是类似 Flux 和 Redux，这两个是 React 组件的状态管理库，Flux 我不清楚，但是 Redux 我用过，确实麻烦，基本思路还是 state、dispatch、action 和 Reducer，事件触发一个 dispatch，发出一个 action，更新 store 的 state，并通知所有相关的组件进行更新

react-dnd 分为项目（这个翻译好怪，我觉得应该是拖拽的一个状态对象），在 Redux 中更新的不是 HTML 元素，而是驱动他的数据，也就是 react 的观念，用数据驱动 UI 的变化，我们只关注 UI 的本质，即数据变化，我们决定数据的逻辑，而渲染 UI 交给 react 来渲染，关注点分离。

react-dnd 的意思也是如此，不关注组件或者说 DOM 元素被拖拽，而是面向数据，某个类型的项目被拖拽了，这个项目我们不关注它是什么，而是他的状态数据。

什么是项目呢，按照官网的定义就是一个 JavaScript 对象，用于描述拖拽。在官网举的两个例子，一个卡片拖拽时，item 就是一个描述卡片的对象，在象棋游戏中，item 就是棋子的对象。最后解释，将拖拽数据描述为一个 JavaScript 对象有助于关注点分离。

什么是 type 呢，type 是 item 的独特描述，也就是类型的意思，举得还是那个例子，看板应用中 card 就是一个拖拽 card 的类型，list 就是可拖拽 list 的类型，我觉得大致意思就像门类，type 就像接口一样，描述一类对象有什么定义和方法。

至于为什么要有 type，官网解释意思是随着项目的成长，你或许需要更多的拖拽 item，但是为了避免性的旧的拖拽对后续新增的 item 有反应，需要隔绝，type 可以做到，就像 Redux 的 action 一样，列举出对应的动作，但是互不干涉。

Monitors

这个概念一开始就是表达作者的一个理念，拖放有两个状态，一个是正在进行，一个是停止，即便是正在进行，也有一个稳定的状态。

而 React-DnD 通过监视器内部存储的状态将拖放的状态暴漏给组件，监视器可以让你根据拖放的状态更新组件。

对于每个需要更新自己拖拽状态的组件，可以定义一个收集函数，将监视器的拖拽数据反馈给组件，当作一个沟通组件和拖拽监视器的桥梁。

Connectors

连接

如果你用的是 react 来处理 DOM 节点，那么程序怎么知道需要监听哪些 DOM 节点，我理解是 react 使用的是虚拟 DOM 节点，也就是自己先计算好需要更新的 DOM 节点，在将其渲染给真实 DOM 中，那么问题来了，拖放如何连接 DOM 节点

实际上，在上面那个收集函数中连接器当作第一个参数（后面开始使用单词代替关键词），在 collect 函数中除了返回拖拽件的状态，还有连接拖拽目标函数

这个 connectors 功能，告诉 React-DnD，我们组件的 DOM 根节点是有效的拖拽目标，拖拽悬停事件应有 React-DnD 后端处理。在内部，它通过回调函数 ref 附加到你指定的 React 元素上。这个函数返回 connectors 记忆，他不会打断 React 的 shouldComponentUpdate 优化。

其实我不太理解这个功能，连接器，将拖拽 item 连接 react，将拖拽目标包裹在一个 React-DnD 根节点下，他的悬停事件交给 React-DnD 后端处理。在内部中通过回调函数 ref 传递到 React 元素上，这样就不会影响 shouldComponentUpdate 优化。

Drag Sources and Drop Targets

拖拽源和拖拽目标

我们现在涵盖了关于这个 DOM 的后端，item 数据和 type 代表和收集函数，通过监视器 monitors 和连接器 connectors，将你的对拖拽组件的描述注入到你的组件。但是，我们如何配置属性注入到我们的组件，我们如何在拖拽时执行 React 的副作用。用 Drag source 和 drop targets
，这两个 React-Dnd 抽象单位。将 type、item 和副作用 side effects、收集函数 collectingyi 和你的组件联系到一起。

如果想让你的组件可以拖放，你只需要将这个组件包裹在拖放源里 Drag Source 里即可，每一个 drag source 注册一个 type，还需要实现一个方法从组件中产生 item，它还可以选择一些方法处理 drag 和 drop 事件，这个 drag source 描述（我的觉得是定义）还可以指定 collecting 函数指定给相应的组件

它区分 drop target 和 drag sources 两种，但是 drop target 可以注册多个 type

我的理解是，这两个是 React-DnD 的抽象单位，用于包裹需要拖拽的组件

Backends

后端（是不是内部技术支持）

React-DnD 使用 HTML5 drag and drop API，它可以将 drap DOM 节点包裹在框架内，当作一个整体去拖放，框外预览，等于你在移动时不需要进行任何绘图（绘制 DOM），这个 API 也是处理 drop 事件的唯一方法。

HTML5 drag and drop API 无法在触摸屏上起作用。

这也是为什么 HTML5 可以在 React——DnD 上实现拖放的原因，你也可以编写自己的拖放支持，传入到 React-DnD 后端。

这个类库对于大部分 web 应用是足够的，还有一个触摸后端应用于移动设备。

backends 与 React 相互作用，可以抽象的处理不同浏览器的 DOM 事件，尽管与 React 有相似之处，但是 React-DnD 对于 React 没有依赖性，在内部中，backends 是将 DOM 事件转化为 React-DnD 内部可以处理的 Redux 动作（也就是数据驱动）

Hook vs Higher-Order Components

React-DnD 包含以下几点

- item 对象和 types 类型

- DnD 状态变量

- monitor 监视 DnD 状态变量

- collector 函数将监视器的输出变成属性

- connectors 连接器连接 DnD 状态和视图节点（例如 DOM 节点）

功能都采用 hook 封装

## 最后总结下

以上述半翻译、半理解的内容来看，React-DnD 使用抽象来理解拖拽，首先用一个 item 来描述拖拽，type 定义，监视函数反馈 state，收集函数将 state 变成属性，而连接器连接着 DnD 和 node 节点，它采用的是一种容器包裹住需要拖拽功能组件，又避免干扰到原本的组件系统，做到关注点分离和解耦。

## 实践一个游戏

这也是教程的一部分，做一个可以拖拽的棋子，原教程中
