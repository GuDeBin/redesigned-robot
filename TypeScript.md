# TypeScript

这个是 TypeScript 的学习笔记，觉得有必要记录下一些关键的概念和困惑

## @types

这个推出是因为很多 JavaScript 的库并没有按照 TypeScript 的类型定义，这个我倒也能理解，我不能理解的是背后的机制，在引入 style-components 后无法通过类型检查，只有再次安装一个@type/style-components 的类型库才行，那么 TypeScript 是如何知道这个的

我猜测是因为@type 里的内容是会被 TypeScript 读取，而且这个属于语言的默认配置，不是项目配置。

在网上也搜索到两篇关于这个概念的解释文章

1. [在 TypeScript 2.0 中使用@types 类型定义](https://www.cnblogs.com/videring/articles/6759957.html)
2. [types 和@types 是什么](https://zhuanlan.zhihu.com/p/194196536)

第一篇解释了，@types 的来历和历史，顺带解释了 TypeScript 是如何读取 Types 的类型定义

第二篇则是详细叙述背后的机制，尤其是 TypeScript 会默认引入 node_modules 下的所有@types 声明，但是开发者也可以通过修改 tsconfig.json 的配置来修改默认的行为.

## FactoryOrInstance

按照翻译的意思是工厂或者实例模式，是设计模式的概念吗，这样一段代码是什么意思或者逻辑呢

```ts
import type { ConnectDropTarget } from "../../types/index.js";
import type { DropTargetHookSpec, FactoryOrInstance } from "../types.js";
/**
 * useDropTarget Hook
 * @param spec The drop target specification (object or function, function preferred)
 * @param deps The memoization deps array to use when evaluating spec changes
 */
export declare function useDrop<
  DragObject = unknown,
  DropResult = unknown,
  CollectedProps = unknown
>(
  specArg: FactoryOrInstance<
    DropTargetHookSpec<DragObject, DropResult, CollectedProps>
  >,
  deps?: unknown[]
): [CollectedProps, ConnectDropTarget];
```

我的理解是这是一个创造对象和实例的函数，也就是输出的参数要有相应的限制，也就是 DragObject、DropResult 和 CollectedProps，这不仅仅是输入的参数限制还有输出的结果限制

这个我还会再来描述
