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
，这两个 React-Dnd 抽象单位。将 type、item 和副作用 side effects、收集函数 collecting 和你的组件联系到一起。

如果想让你的组件可以拖放，你只需要将这个组件包裹在拖放源里 Drag Source 里即可，每一个 drag source 注册一个 type，还需要实现一个方法从组件中产生 item，它还可以选择一些方法处理 drag 和 drop 事件，这个 drag source 描述（我的觉得是定义）还可以指定 collecting 函数指定给相应的组件

它区分 drop target 和 drag sources 两种，但是 drop target 可以注册多个 type

我的理解是，这两个是 React-DnD 的抽象单位，用于包裹需要拖拽的组件

Backends

后端（是不是内部技术支持）

React-DnD 使用 HTML5 drag and drop API，它可以将 drag DOM 节点包裹在框架内，当作一个整体去拖放，框外预览，等于你在移动时不需要进行任何绘图（绘制 DOM），这个 API 也是处理 drop 事件的唯一方法。

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

这也是教程的一部分，做一个可以拖拽的棋子，原教程中还开了一个玩笑，说这个教程并不能制作一个象棋教程。

开始吧

教程分为三个部分，第一部分是起步，第二部分是制作游戏，第三部分是添加拖拽插件。

第一部分：起步

教程采用 Create-react-app 快速构建一个 react 应用（我实在不想用大写了），

第二部分：创建一个 react 游戏应用

首先，构建一个没有拖拽功能的 React 应用，而这个游戏应用需要以下几个组件

- 棋子，我们孤独的骑士

- 方块，棋盘上的方块

- 棋盘，64 个方块组成的棋盘

让我们来理清楚这里面的逻辑

- 棋子不需要属性，棋子需要位置，但是没必要让棋子知道位置，因为这个通过方块来确认。

- 通过方块提供位置信息很诱人，但这个是不必要的，因为方块需要的渲染的是颜色，默认的方块是有一个白色的属性，它可以接受一个参数：棋子，浏览器的背景我默认选择白色。

- 棋盘会棘手些，直接将棋子当作参数传入毫无意义，因为棋盘还需要什么，他可能需要其他的，但是这些它或许需要拥有，因为棋盘需要知道棋子的位置。在国际象棋上需要一个描述所有棋子、颜色和位置的对象数据，但是对于这个应用，一个数组就足够了，太多很是烦扰。

当前的状态会在哪里存放，放在组件内并不是一个好主意，最好是尽可能减少在组件内使用状态，因为棋盘已经有了一些布局逻辑，不应该让它在负担状态的管理。

好消息是我们不需要关系这些位置，只需要写好组件，假设状态存在某个地方，只需要确保状态可以通过组件属性接收到并正确渲染，然后在考虑如何管理状态。

开始创建组件

我喜欢自下而上的开始，这是我一贯采用的处理方式，作者的意思是在开始时并不考虑最后的成果，或者在完成前都看不到结果如何，但是我可以立即看到当下的成果，这种及时反馈对作者来说很重要（虽然我还是没能理解这段话的全文，大致我认为是，作者采用从下而上的方式搭积木，不到最后完成无法知道结果如何，但是每一步都有及时反馈，等于说是走好每一步，结果是个自然而成的）

作者从棋子开始，因为他没有任何属性传入

♘ 这个是一个 Unicode knight

作者先渲染第一个组件，保证渲染后才开始编写其他的组件，始终保持自己得到最新的反馈，不再“黑暗”中编写组件（这确实是前端最该实践的方法论，始终得到最新的反馈）

一个点：我不适用 JSX 和引入 React 的话，也可以渲染 React 组件，只是在引用时是 any，而我采用 JSX，既有智能提醒，还有引入方变为 JSX.Element，而且在 JSX 中引入 React 不再是暗色，也就是未使用，目前还是采用 jsx 格式写组件，并在开头引入 React
，后续了解 React 的原理再来回答这个现象

遇到一个问题，没有按照教程所示的显示棋盘，而是一个条状的棋格，不是按照 flex 布局。我得看看教程的 CSS

难道是宽度的设置

已经解决了，是没有对容器，也就是最外层的 App-div 做约束，也就是没有宽度和高度,导致内部 item 也是按照一般的顺序排列而已

我删除里 App.css，而是在 App.js 中用一个 div 当作容器包裹住棋盘，然后删除 index.css 内容，增加

```css
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: "Poppins", sans-serif;
}

.container {
  width: 500px;
  margin: 50px auto;
  overflow: auto;
  height: 500px;
  border: 1px solid gray;
  border-radius: 5px;
}
```

可以继续了

他设置棋子随机跳动，用的是定时执行渲染程序，也就是 index.js 的 render 函数

```jsx
import React from "react";
import ReactDOM from "react-dom";
import Board from "./Board";
import { observe } from "./Game";

const root = document.getElementById("root");

observe((knightPosition) =>
  ReactDOM.render(<Board knightPosition={knightPosition} />, root)
);

// Game.js

export function observe(receive) {
  const randPos = () => Math.floor(Math.random() * 8);
  setInterval(() => receive([randPos(), randPos()]), 500);
}
```

而我的是用 useState 设置棋子的位置，结果是越来越来快，以至于很疯狂，我估计渲染机制应该是异步的

```jsx
// App.js
import { useState } from "react";
import Board from "./components/Board";

export default function App() {
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);

  const randPos = () => Math.floor(Math.random() * 8);

  setInterval(() => {
    setX(randPos);
    setY(randPos);
  }, 1000);

  return (
    <div className="container">
      <Board knightPosition={[x, y]} />
    </div>
  );
}
```

结果就是疯了

我打算把 Game 的逻辑下载 App.js 里

## 2022 年 7 月 29 日

还是算了，按照单独的模块执行

还是按照我之前的设置来做吧，只需要将修改棋子位置的事件函数传到每一个方块，让方块反馈棋子的位置，并修改棋子位置重新渲染。

一个愚蠢的错误，useState 重新设置值的方式，是重新输入一个新的数组，要用[]的

```jsx
const [knight, setKnight] = useState([1, 7]);
setKnight(toX, toY); //这个是错误的
setKnight([toX, toY]); //这个是正确的
```

## 2022 年 7 月 30 日

这个 L 型的逻辑是怎么回事呢

首先棋子在现在（0，6）
按照这个逻辑来判断 L 型

```js
const canMoveKnight = (toX, toY) => {
  const [x, y] = knightPosition;
  const dx = toX - x;
  const dy = toY - y;

  return (
    (Math.abs(dx) === 2 && Math.abs(dy) === 1) ||
    (Math.abs(dx) === 1 && Math.abs(dy) === 2)
  );
};
```

这个逻辑的意思是当前的位置与现在的位置之差只能是 1 或者 2

切换一个位置是（1，4），确实是 x 相差 1，y 相差 2

再切换一个位置（2，7），确实 x 相差 2，y 相差 1

也就是按照 X 轴方向是 x 相差 1，y 相差 2

Y 轴则是 x 相差 2，y 相差 1

突然想到一个点，这不就是一个笛卡尔坐标系吗，所谓的 L 型就是一个方向移动一步，一个方向移动 2 步，所以不是 x 就是 y。这就是为什么两个判断条件之间是或的关系，因为只要满足一个方向一步，一个方向 2 步的条件，就可以知道从事了 L 型步骤。

## 添加拖拽

首先是设置 DndProvider，这个需要应用程序的顶层设置，这里我选择的是 App.js 里

```jsx
return (
  <DndProvider backend={HTML5Backend}>
    <div className="container">
      <Board knightPosition={knightPosition} moveKnight={moveKnight} />
    </div>
  </DndProvider>
);
```

在定义一个拖拽的类型

这个需要拖拽的是 knight

```js
export const ItemTypes = {
  KNIGHT: "knight",
};
```

真的很神奇

目前棋子已经可以实现拖拽

就是在需要拖拽的组件内添加一个 useDrag 的 Hook

```jsx
import React from "react";
import { ItemTypes } from "../Constants";
import { useDrag } from "react-dnd";

function Knight() {
  const [{ isDragging }, drag] = useDrag(() => ({
    type: ItemTypes.KNIGHT,
    collect: (monitor) => ({
      isDragging: !!monitor.isDragging(),
    }),
  }));

  return (
    <span
      ref={drag}
      style={{
        fontSize: "45px",
        fontWeight: "bold",
        cursor: "move",
        opacity: isDragging ? 0.5 : 1,
      }}
    >
      ♘
    </span>
  );
}

export default Knight;
```

ref 引用的方式，前面的 isDragging 是来自后面的 collect 函数，这个函数传入的第一个参数，monitor 是监视器，现在返回一个对象，对象里有一个参数 isDragging，表示是否处于拖拽状态，而这个状态就是来自第一个 monitor 的 isDragging 属性

好吧，我都快绕晕了

如果我只是需要用的话，那我根本不需要了解背后的原理，直接按照最佳实践使用即可。

有一个新的属性，ref，也就是引用，按照 react 文档解释

> 在典型的 React 数据流中，props 是父组件与子组件交互的唯一方式。要修改一个子组件，你需要使用新的 props 来重新渲染它。但是，在某些情况下，你需要在典型数据流之外强制修改子组件。被修改的子组件可能是一个 React 组件的实例，也可能是一个 DOM 元素。对于这两种情况，React 都提供了解决办法。

按照我的理解，就是代替 prop 来修改子组件的方式。也就是 react-dnd 在全局引用这个组件。我还需要更多的编码，见到更多的项目

现在已经解决了拖拽 Drag source 的问题，下一个是如何实现放 drop source 的目标

这个是 react 核心开发、redux 作者，也是这个拖拽库的作者之一，我滴神啊

愚蠢的组件和聪明的组件，难道果断抛弃原来的组件，拯救原来的还不如直接切掉，直接从可以改造的 square 开始，重新设置 BoardSquare 函数

按照作者之前的文章[演示组件和容器组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

按照两个功能来作为区分，一个展示另一个是容器

简单来说，这也算是关注点分离，将每个组件只关注与自己的任务。

> 不过这个观点是 15 年的，在 19 年作者已经不再坚持这个观点，因为 react hook 的出现，将复杂逻辑的可以采用 hook 封装，而不用在将复杂的状态逻辑与组件其他分开

现在作者在这骗教程认为是将原来的组件复杂化是一个愚蠢的决定，应该将新的逻辑分装在一个新的组件，并包裹原来的组件。

## 2022 年 8 月 5 日

回到这个教程上，现在新建的这个组件我还真的不知道什么意思，大概应该是将一些 drop 逻辑放在这个上面个。

## 2022 年 8 月 8 日

我遇到一个问题了，现在要求解决棋子落下的问题，也就是需要知道放置的位置，按照教程是采用了驱动重现渲染的 render 函数，可我采用的是事件驱动，也就是用点击事件设置 state，是通过 setState 函数来做的

现在一个思路是将修改位置的函数从最顶端的 App 中一种传入到 BoardSquare

## 2022 年 8 月 17 日

没想到拖了这么久，分身乏术啊

继续

只剩下放置的动画特效

首先回忆下

react-dnd 这个拖拽库实现拖动特效和放置特效，简单来说就是两个 API，每个 api 包含项目和类型，收集函数和监视器，项目和类型定义拖拽物，监视和收集函数反馈位置。

目前已经实现了拖，这个是在棋子内实现，一个 useDrag 的 Hook 函数，返回参数第一个是收集函数的特性，一个是该组件的 ref，我也不知道这个 ref 是个什么意思。

我了个擦，我全忘了

最后是还有放置时的动作，但是我不想再按照教程去做了，直接看完成的代码

作者还是说的对，如何开始一个项目，是应该从自下而上，就这个棋盘，棋盘、棋框和棋子，这三个，那么首先单个实现棋子.

实在是不应该在完美和完成之间犹豫，完成优于完美

难道采用 export const 会主动让编辑器识别，自动导入吗

并不是，只是在组件中。。。。这只是一个编码风格吗

基本的页面已经实现了，现在要增加功能
