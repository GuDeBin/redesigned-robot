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
