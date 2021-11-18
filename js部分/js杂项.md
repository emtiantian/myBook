# js 杂项

## 常见loadsh使用

- cloneDeep
- 节流/防抖
- isEmpty

### js forEach和for in 、Object.keys(system).forEach((key)

- js 中forEach的index 是从0开始的！
- for in不建议对对象进行使用会遍历对象所有属性和方法、应该使用 object.keys来查找所有本对象属性来循环

### yield 委托

- 表达式用于委托给另一个generator 或可迭代对象

```js
//以下代码中，g1() yield 出去的每个值都会在 g2() 的 next() 方法中返回，就像那些 yield 语句是写在 g2() 里一样。
function* g1() {
  yield 2;
  yield 3;
  yield 4;
}

function* g2() {
  yield 1;
  yield* g1();
  yield 5;
}

var iterator = g2();

console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: 4, done: false }
console.log(iterator.next()); // { value: 5, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

### proxy 代理 （vue3 用这种方式监听对象的改变）

### js 全局异常处理

- 通过在 window.onerror 上定义一个事件监听函数，程序中其他代码产生的未被捕获的异常往往就会被 window.onerror 上面注册的监听函数捕获到。并且同时捕获到一些关于异常的信息。

```js
window.onerror = function (message, source, lineno, colno, error) {}
// message：异常信息（字符串）
// source：发生异常的脚本URL（字符串）
// lineno：发生异常的行号（数字）
// colno：发生异常的列号（数字）
// error：Error对象（对象）
// 注意：Safari 和 IE10 还不支持在 window.onerror 的回调函数中使用第五个参数，也就是一个 Error 对象并带有一个追溯栈
// 在Chrome中，window.onerror 能够检测到从别的域引用的script文件中的异常，并且将这些异常标记为Script error。如果你不想处理这些从别的域引入的script文件，那么可以在程序中通过Script error标记将其过滤掉。
// 然而，在Firefox、Safari或者IE11中，并不会引入跨域的JS异常，即使在Chrome中，如果使用 try/catch 将这些讨厌的代码包围，那么Chrome也不会再检测到这些跨域异常。
// 在Chrome中，如果你想通过 window.onerror 来获取到完整的跨域异常信息，那么这些跨域资源必须提供合适的跨域头信息。
```
