# JavaScript 取余运算符

当一个操作数除以第二个操作数时，取余运算符（%）返回剩余的余数。它与被除数的符号保持一致。

```js
console.log(13 % 5);
// expected output: 3

console.log(-13 % 5);
// expected output: -3

console.log(4 % 2);
// expected output: 0

console.log(-4 % 2);
// expected output: -0

console.log(0 % 8);
// expected output: 0

console.log(7 % 8);
// expected output: 7

console.log(8 % 8);
// expected output: 0
```

## 总结

这个是一个整除后返回余数，但是第一个操作数小于第二个操作数的话，直接返回一个第一个操作数，整除取零
