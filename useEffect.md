# useEffect

之前就一直再用这个，但是基本上只有一个定义，他是一个执行副作用的函数，也就是在函数组件被渲染后执行的操作，是提到之前的各种创建前 componentDidMount、组件更新 componentDidUpdate 和组件销毁前 componentWillUnmount 的代替，我简单的认为就是一个组件一旦创建的就开始执行的操作。

也就这点理解了

在使用后发现一些东西在此记录下

我还是出奇的喜欢 React

## 使用 setInterval

这个其实在 react 的[官方文档](https://zh-hans.reactjs.org/)中有记录相关的问题

还是官网最贴心

我之前先写了一个在组件内的 setInterval

```jsx
function Test() {
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);

  const randPos = () => {
    Math.floor(Math.random() * 8);
  };

  setInterval(() => {
    setX(randPos);
    setY(randPos);
  }, 1000);

  return (
    <div className="container">
      <Board knightPosition={(x, y)} />
    </div>
  );
}
```

这个就是一个混乱的开始

我估计应该是渲染很多次 setInterval，这个和 react 的机制有关吧（不清楚）

但是在用 useEffect 中，也就是按照 react 官方文档的介绍来做，也就是

```jsx
export default function App() {
  const [count, setCount] = useState(0);
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);

  const randPos = () => Math.floor(Math.random() * 8);

  useEffect(() => {
    const id = setInterval(() => {
      setX(randPos);
      setY(randPos);
      setCount((c) => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return (
    <div className="container">
      <Board knightPosition={[x, y]} />
      <h1>{count}</h1>
    </div>
  );
}
```

这样就可以了，按照这个[问题解释](https://zh-hans.reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)

大致意思就是不再引用之前的 state，而是指定 state 该如何改变

他首先举一个例子

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1); // 这个 effect 依赖于 `count` state
    }, 1000);
    return () => clearInterval(id);
  }, []); // 🔴 Bug: `count` 没有被指定为依赖

  return <h1>{count}</h1>;
}
```

在 useEffect 中直接引用之前 count 值更新，结果因为 effect 执行时，会创建一个闭包，并将 count 值保存在该闭包中，且初值为 0。每隔一秒，回调就会执行 setCount（0+1），因此，count 永远不会超过 1。

指定[count]作为依赖列表就能修复这个 Bug，但是会导致每次改变都会导致定时器被重置，事实上，每个 setInterval 在被清除前（类似于 setTimeout）都会调用一次。但这并不是我们想要的。要解决这个问题，我们可以使用 setState 的函数式更新形式。它允许我们指定 state 该 如何 改变而不用引用 当前 state：（官网解释比我总结更有效）

至于函数更新，我觉得应该是不直接使用 count，而是利用 setCount 的函数式更新，如果新的 state 需要通过使用先前的 state 计算得出，那么可以将函数传递给 setState。该函数将接收先前的 state，并返回一个更新后的值。

```jsx
function Counter({ initialCount }) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount((prevCount) => prevCount - 1)}>-</button>
      <button onClick={() => setCount((prevCount) => prevCount + 1)}>+</button>
    </>
  );
}
```

“+” 和 “-” 按钮采用函数式形式，因为被更新的 state 需要基于之前的 state。但是“重置”按钮则采用普通形式，因为它总是把 count 设置回初始值。

如果你的更新函数返回值与当前 state 完全相同，则随后的重渲染会被完全跳过。

useState 不会自动合并更新对象。你可以用函数式的 setState 结合展开运算符来达到合并更新对象的效果。

```jsx
const [state, setState] = useState({});
setState((prevState) => {
  // 也可以使用 Object.assign
  return { ...prevState, ...updatedValues };
});
```

useReducer 是另一种可选方案，它更适合用于管理包含多个子值的 state 对象。

## useEffect

这个介绍最详细[useEffect](https://zh-hans.reactjs.org/docs/hooks-effect.html)
