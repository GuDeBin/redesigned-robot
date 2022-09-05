# useRef

```js
const refContainer = useRef(initialValue);
```

useRef 返回一个可变的 ref 对象，其.current 属性被初始化为传入的参数（initialValue)。返回的 ref 对象在组件的整个生命周期内持续存在。

一个常见的用例便是命令式地访问子组件

```js
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

本质上，useRef 就像是可以在其.current 属性中保存一个可变值的“盒子”。

你应该熟悉 ref 这一种访问 DOM 的主要方式。如果你将 ref 对象以

```html
<div ref="{myRef}"></div>
```

形式传入组件，则无论该节点如何改变，React 都会将 ref 对象的.current 属性设置为相应的 DOM 节点。

然后，useRef（）比 ref 属性更有用。它可以很方便地保存任何可变值，其类似于在 class 中使用实例字段的方式。

这是因为它创建的是一个普通 JavaScript 对象。而 useRef（）和自建一个{current：...}对象的唯一区别是，useRef 会在每次渲染时返回同一个 ref 对象。

请记住，当 ref 对象内容发生变化时，useRef 并不会通知你。变更.current 属性不会引发组件重新渲染。如果想要在 React 绑定或解绑 DOM 节点的 ref 时运行某些代码，则需要使用回调 ref 来实现。

## 自述

在遇到不能直接理解的概念时，我习惯将官网文档，自己写一遍，以前用笔，现在用的是键盘。放慢自己的思考，跟着作者的引导去理解他想说明的东西。

写完这个官网的内容，我有点自己的理解

ref 类似一根线，一个可以将一个组件直接连接到我需要的任何地方和场景，而 current 则是这个节点的对应的属性，React 可以将 ref 对象的 current 属性设置为相应 DOM 节点的属性。

用文档例子说明，一个按钮点击后，输入框聚焦，这里使用 useRef 创建一个 ref 对象，input 引入，这里要说一点是，我之前按照从上到下的逻辑去思考这个代码，始终想不通，ref 创建时没有和子组件建立链接，怎么到下面反倒可以直接用了，其实是反过来的，ref={myRef}可以看作第二步，也就是第一步新建一个 ref，在子组件建立 ref={myRef}，第三步将这个 ref 用作聚焦函数内，在按钮组件调用，这样就完成了，按钮点击输入框聚焦。

这么就可以解释 ref 的应用，其实说是线，钩子更合适
