# useState

来自[官网](https://zh-hans.reactjs.org/docs/hooks-state.html)

useState 是允许你在 React 函数组件中添加 state 的 Hook

## 作用

调用 useState 方法的时候做了什么? 它定义一个 “state 变量”。我们的变量叫 count， 但是我们可以叫他任何名字，比如 banana。这是一种在函数调用时保存变量的方式 —— useState 是一种新方法，它与 class 里面的 this.state 提供的功能完全相同。一般来说，在函数退出后变量就会”消失”，而 state 中的变量会被 React 保留。

useState 需要哪些参数？ useState() 方法里面唯一的参数就是初始 state。不同于 class 的是，我们可以按照需要使用数字或字符串对其进行赋值，而不一定是对象。在示例中，只需使用数字来记录用户点击次数，所以我们传了 0 作为变量的初始 state。（如果我们想要在 state 中存储两个不同的变量，只需调用 useState() 两次即可。）

useState 方法的返回值是什么？ 返回值为：当前 state 以及更新 state 的函数。这就是我们写 const [count, setCount] = useState() 的原因。这与 class 里面 this.state.count 和 this.setState 类似，唯一区别就是你需要成对的获取它们。如果你不熟悉我们使用的语法，我们会在本章节的底部介绍它。
