# React 动画实现

项目来自[react-flip-demo](https://github.com/MinJieLiu/react-flip-demo)

## 开始

首先先说下这个是什么，这是一个 react 动画实现的 demo，觉得很有趣，刨析下

首先看下项目的文件结构

插下，这个项目文件目录是用 mddir 这个 npm 包实现的，简单来说是如下操作

1. 首先是安装

```shell
npm install mddir -g
```

2. cd 到项目目录，并执行 mddir

```shell
cd 项目目录

mddir
```

3. 在项目目录下就会生成一个 directoryList 的 Markdown 文件，内容就是项目文件目录

注意事项

1. mddir 会自动忽视 node_modules 和.git 文件夹

2. 在复制到文档中时，需要用三个反引号包裹，就像 markdown 中输入程序段落一样，不然就是一段乱码

## 回到正题

该项目的文件目录如下

```
|-- project
    |-- .gitignore # git排除文件
    |-- LICENSE # 许可证
    |-- README.md # 自述文件
    |-- index.html # 起始文件
    |-- package-lock.json # npm包
    |-- package.json # npm文件
    |-- tsconfig.json # typescript配置文件
    |-- tsconfig.node.json #项目引用
    |-- vite.config.ts # vite配置
    |-- src # 源码
        |-- App.module.css # app基本样式
        |-- App.tsx # app起始文件
        |-- index.css # 基本样式
        |-- main.tsx # 起始文件
        |-- vite-env.d.ts # vite环境配置
        |-- components # 组件文件夹
        |   |-- Flipper # 过度文件，组件
        |       |-- FlipContext.ts
        |       |-- Flipped.tsx
        |       |-- Flipper.tsx
        |       |-- index.ts
        |-- pages # 页面
            |-- Demo1.tsx # Demo1
            |-- Demo2.tsx # Demo2
            |-- Demo3.tsx # Demo3
            |-- utils.ts # 通用工具文件
            |-- components # 页面组件
            |   |-- doc-components.tsx # 基础的样式组件
            |-- hooks
                |-- useSortProperty.tsx #  封装react-dnd动画的hook库
```

### 其中用到的 npm 包

#### styled-components

这个是关于 CSS 样式，通过看文档和实例，我总结是将关注于全局的 CSS 压缩到关注单个组件的范围内，也就是将关注点放在当下的事情上。隔断 CSS 全局，更加灵活的使用 css

#### lodash-es

这是一个高性能的 js 工具包

#### mac-scrollbar

这个是一个屏幕滚动的 react 组件，作者就是这个项目的作者，有点意思，想尝试下做 npm 包

#### react-dnd 与 react-dnd-html5-backend

这就是 demo3 的拖拽库

#### react

这个不用说了，就是 react 本尊，一切的开始。

#### 疑惑

在 devDependencies 中的@type 类，按照我搜索的资料这些是 typescript 的 type 类库之类的，定义类型，但是我不知道这些是作者引入的，还是用 vite 社区模板直接生成的，应该是 vite 自己生成的，这个以后再去验证，现在先将其排除在外，看看有什么影响。

### create-react-app

```sh
npx create-react-app my-app --template typescript
```

不过这个项目名实在是难取，找一个随机名字吧

congenial-waddle

适宜的摇摆走，这个 GitHub 到底是人工智能生成的还是一个简单的单词拼接

这个是 GitHub 新建库时给的随机名字

### 2022 年 9 月 5 日

还真是慢慢走、慢慢来

首先看下这个公众话文章——[前端必学的动画实现思路](https://mp.weixin.qq.com/s/SG_aswuFU2ai0M2FIo2-BA)

之所以想实现一次，还是因为这类小 Demo 很有意思

下面正式开始学习记录
首先作者抛出的一个观点是，之前前端在实现动画技术时，依赖的技术主要是 animation、transition 和 requestAnimationFrame

其中 requestAnimationFrame 的功能如下

> 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行

> [网址](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)

按照作者之后的描述，一个可添加的数字的随机乱序列表，直觉的实现思路是：将这些数字的 DOM 节点用绝对定位来布局，数字变化后计算 top、left 的值，再配合 transition 实现该动画，这种方式看似简单，其实内部要维护各种位置信息，所有坐标都需要手动管理，相当繁琐，非常不利于后期扩展。

这里就提出一个实现思路，也就是 FLIP

FLIP 是 First、Last、Invert 和 Play 的首字母

First：涉及动画元素的初始状态（比如位置、缩放、透明等）

Last：涉及动画元素的最终状态

Invert：这一步为核心，我理解是运动的相对量，也就是 dX，但是在 DOM 元素改变后并不是立即变化，而是由浏览器集中延迟到下一帧进行统一渲染，所以有一个中间时间，也就是 DOM 位置信息改变了，而浏览器还没有渲染。这个中间时间我们可以用来实现 Invert 过程。作者举例几个实现的 API，分别是浏览器的 setTimeout、React 的 useEffect 和 Vue 的 nextTick

Play：这个从 Invert 到最终状态，也就是动画效果实现阶段，当有了两个点的位置信息，中间的过度状态就可以使用 transition 实现，作者使用的是 Web Animation API 实现，好处是动画执行过程中不会添加 CSS 到 DOM 上，相当干净（我没能理解这个干净，意思是只渲染动画的 DOM 不进一步影响样式）

### 实现

这里首先需要记录 First 和 Last 的位置，计算 Invert 的偏差，用 Map，也就是字典来存储，作者新增的一个方法来创建前后快照

这里有一个知识点我并不太理解，ref，这篇是我关于[ref 的知识总结](./useRef.md)

反复看了几遍才明白其中的逻辑

首先，一个列表，再有一个创建子元素的集合字典，这里有一个知识点，map 也有原型方法，也就是 forEach 遍历各个元素，参数是一个回调函数，函数有两个参数，一个元素的值和对应的键。

这里有一个方法，用于生成快照

```js
function createChildElementRectMap(nodes: HTMLElement | null | undefined) {
  if (!nodes) {
    return new Map();
  }
  const elements = Array.from(nodes.childNodes) as HTMLElement[];
  // 使用节点作为 Map 的 key 存储当前快照，下次直接用 node 引用取值，相当方便
  return new Map(elements.map((node) => [node, node.getBoundingClientRect()]));
}
```

执行逻辑如下

1. 使用一个 lastRef 来引用当前的 DOM
2. 新建一个快照 map 生成函数
3. 在点击事件的函数中将之前的 dom 信息，但是在此之前将使用 setData 将数组添加一条。此时虽然驱动 UI 的数据已经改变，但是 UI 还没有渲染。
4. 在 useLayoutEffect 中，将改变后的 DOM 通过快照生成函数
5. 遍历之前的快照，并进行对比，计算出 invert，并计算出位移 transition，创建一个关键帧 keyframes，在使用 web animate 执行动画。

这里说下 animate 这个 API，两个参数，一个是关键帧，也就是过程动画的两个状态，还有就是一个动画执行的配置函数。

开始复刻下

这里作者使用的是 style-components，我不太喜欢这个

果然遇到 typescript 的问题，也就是类型，有些 npm 包并不支持 typescript，需要额外的 type，这个[TypeScript](./TypeScript.md)中。

有个想法，我实在是不太喜欢这个 style-components，可以尝试原子化的 css，也可以直接用最原始的方式，行内样式

缺少一个 box-sizing: border-box 的全局样式，我直接写在 App.css 中，有点强迫证，犹豫不决的放在哪好，其实一个原则，能达到目的即可

第一个直接实现并没有太多难以理解的难点

按照 first、last、invert 和 play，记录下 first 和 last，计算出 invert，在执行 paly，只要将 React 的渲染的顺序搞明白，就可以直接在其中加入和这些逻辑，现在是一个元素改变后到浏览器渲染存在三个阶段，setState、render 和 effect，记录下 first 的逻辑在 setState 中，将计算 invert 和 paly 放在 useLayoutEffect 中，最后在浏览器渲染开始，就出现动画逻辑。

其实我不太明白的是，这个每次操作都需要手动更新快照什么意思，对于手动这个的理解是重置吗，还是计算 invert

下一步实践是将功能封装程 Hook

作者提出封装的需求

1. 数据变化后自动执行动画
2. 可以不关心任何动画逻辑
3. 不要限制 DOM 结构
4. 用法简单
5. 性能要好

第一部应该是将 first 和 last 快照生成，从操作 setData 中转移出来，而 useLayoutEffect 的逻辑也抽离出来，但是后面的组件封装触及到我的知识盲区。再看文章已经没有什么启发意义，在知道基本的逻辑后，下一步其实就是如何和 React 的机制结合。

突然发现一个点，直接实现版本里没有重置这个功能，我一开始理解是需要重新设置为初始值，但是无法读取之前元素的 left 属性，这个应该是因为设置为初始值时，之前的 DOM 已经被删除，没有了之前的元素信息，也就是无法读取，不过这个应该只是一个判断的问题，为什么作者没有去做，还是这个就是手动更新的意思，就是加了可以重置功能吗

还是继续封装

第一疑问，为啥要这样

```js
export * from "./Flipper";
export * from "./Flipped";
export * from "./FlipContext";

export { default as Flipper } from "./Flipper";
export { default as Flipped } from "./Flipped";
```

后面明明只是用下面两个 API

还有一个，就是，这个导入导出，是将多个模块的导入到一个父模块，再由父模块导出，实践下

遇到第一个概念——Context，这个就是一个代替组件传递 prop 的大号组件，更加灵活而已。

还有一个——MutableRefObject，这个搜索找到的一个[解释](https://cloud.tencent.com/developer/article/1901142)

useRef 返回的对象就是 MutableRefObject，恰如其名，这个就是一个可变的引用对象

又出现一个 memo，按照官网这是一个高阶组件，在相应 props 或者 state 没有改变时不会再次渲染，这个和我之前用的 useMemo 不同，网上找了一圈还是找到一个翻译的解释，按照我的理解这个应该是这么一个意思，memo 是一个高阶组件，是按照组件的逻辑来做性能优化，而 useMemo 是用更细的粒度去做优化，也就是相应的依赖值没有变化，就不会驱动相关函数渲染，[文章地址](https://juejin.cn/post/6991837003537088542)

cloneElement 这个 API

最开始的方式是通过原生方法遍历 DOM，因此我们只能限制子节点一个层级，并且操作方式也脱离的 React 的编写模型，加以改进可以使用 Context 来通信存储：

这是原文，也就是在 useLayoutEffect 中遍历之前的 DOM 节点，并按照计算出来的 invert 来执行 play，作者的意思是这种方式只能遍历子节点，操作方法脱离了 react 的编写模型，那么，react 的编写模型是什么

按照 FlipContext 中的代码逻辑，结合 createContext 的作用，创建一个组件，我觉得应该从组件的角度去思考 Context，也就是它包裹的子组件甚至更深的嵌套，这解释了我为什么没有在 FlipContext 中看到具体的状态值，只是一个架子。

有一个思考总结下

react 是如何构建的，目前看到的代码，将各个逻辑分解，单个组件的逻辑，容器组件的逻辑和贯穿其中的数据流，这一切是如何组织起来的？

还有这几个 Hook，useRef、useMemo、useContext 和 useLayoutEffect，以及 memo 和 createContext

useRef 是一个全局的引用，可以挂载在组件上，useMemo 是一个优化手段，只有在依赖项更新才会执行，useContext 是一个全局的数据流，只有在 context 组件值更新时才会触发，useLayoutEffect 是一个收集全局副作用的函数，并在全局渲染前执行，memo 是一个高阶组件，在 prop 更新时刷新，createContext 则是创建 context 这个全局数据流组件

那我可以单独去实现一次吗，不凭借记忆，而是理解和实现一个思路

首先这个思路是什么

动画的逻辑，也就是比较 first 和 last 之后产生 invert，在渲染前计算出这个将 invert 用动画 play 出来，

而渲染中，state>render>layoutEffect

重新看下，好像理解了作者在第一个实现中说的手动更新快照是怎么回事

```js
//添加数字
function add() {
  setData((prev) => range(prev.length, prev.length + 10).concat(prev));
  lastRectRef.current = createChildElementRectMap(listRef.current);
}

//打乱数组顺序
function shuffleList() {
  setData(shuffle);
  //存储变更前的位置信息
  lastRectRef.current = createChildElementRectMap(listRef.current);
}
```

意思（我猜的）是添加新数字和打乱顺序都需要添加一个更新快照的语句

```js
lastRectRef.current = createChildElementRectMap(listRef.current);
```

这就是一个同样的逻辑在两处复用但没有任何联系，一旦逻辑修改，那就是 ctrl+F 全局寻找了，也是 Dan 在发布 Hook 时说的 class 的问题之一。

我需要理一下

按照之前的 Demo1 的逻辑，也是动画的逻辑，就是 first、last 和 invert，再用 paly 执行，这里就需要按照 react 的执行逻辑来分别获取 first 和 last，计算 invert 后执行 play，这里其实有一个我没能太搞明白的点，react 的执行逻辑，按照作者的理解是 state-render-layoutEffect，也就是数据更改-渲染 DOM-全局副作用或者生命周期，最后才交给浏览器进行绘制。

按照动画的 FLIP 的逻辑结合 react 的机制，第一个 Demo 中的动画实现在我的理解下是如下的

首先将生成快照，也就是 First 和 last 的 DOM，将这个函数抽离出来，在组件内使用 useRef，这个 hook 我理解是一个万能牌，一个可变的 js 对象，可以存储任何可变值，而且它可以挂载在 DOM 节点中，current 就像一个钩子

其次是 useRef 还是组件全生命周期内的变量，这就是。。。。。为啥不直接用一个变量呢，或者 useState，这个可能是因为组件生命周期原因吧。

还有就是 useLayoutEffect 这个 hook，我没能明白这个，之后再 react 的官网上了解到一些，这个类似 effect，但是 effect 是在浏览器完成布局和绘制之后在一个延迟事件中被调用，但是 layoutEffect 则是在所有的 DOM 变更后同步执行。

这也就是能解释下这个 FLIP 的逻辑是如何拆解程 react 的逻辑，首先一个 useRef 钩住 DOM 元素，另一个 useRef 作为对比快照，一个数组来渲染子元素，当 add 方法添加十个元素后，将触发 react 渲染机制，并在 state 时将原有的元素状态赋值到对比快照，在计算完 DOM 后，触发 layoutEffect，开始对比两个，将 invert 计算出来，并渲染动画

而第二个 Demo，也是同样的逻辑，但是按照 react 抽离逻辑，将动画和操作分离，在使用时只用将涉及动画的数据传入组件即可

这里有三个组件，分别是 flipped、flipper 和 flipContext，突然想到这三个的层级是这样，flipper 是父组件，flipped 是子组件，而 flipContext 则是数据通信，将数据放置在组件外。

用文字去梳理则真的比我在大脑去构建容易的多

那三个组件按照这个分离的意义在哪呢，关注点分离，那他们的关注点分别是什么

flipper 是关注什么，全局的比较快照，计算 invert，执行 play，如何实现的呢，

还是顺序，我不明在这个组件的渲染顺序，我记得应该是子组件先渲染，在用到父组件上，在这个 Demo 上，我能猜到她的逻辑吗

还是

我有点不想再继续下去了，这个逻辑有点超出我应用的范围，也就是我懂了之后又能直接写出来吗

还就是我知道她的渲染顺序怎么样呢

还是把之前一篇文章抄一遍自省下

[新拾取的 6 个习惯和方法](https://mp.weixin.qq.com/s/fGYFE3k_P9CiHDJyZUM5IQ)

我的问题在于什么呢

1. 编码太少，就像文章说的，水喝的太少了
2. 对于问题的理解没有到本质层面，这个属于 react 的渲染逻辑，也就是整个框架的逻辑，我在一个简单的组件里短时间根本无法参悟出什么逻辑，而是更多的困惑

解决方法

1. 多看看其他的人的源代码，遇到硬骨头先绕过，就像闪电战突击一样，遇到硬骨头放一个牵制兵力即可，继续吃掉更多的领地，扩大打击范围，当硬骨头被孤立时，被四面包围时，投降只是时间问题。
2. 保留对于 react 的本质思考，保留这个疑问，继续走下去
