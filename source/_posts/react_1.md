---
title: hook原理
date: 2020-7-20 13:00 #文章生成时间，一般不改，当然也可以任意修改
categories: react #分类
tags: [react, hooks] #文章标签

---

#### hoos使用可能产生的疑惑

1. 为什么 useEffect 第二个参数是空数组，就相当于 ComponentDidMount ，只会执行一次？

2. 为什么只能在函数的最外层调用 Hook，不能在循环、条件判断或者子函数中调用？

3. 自定义的 Hook 是如何影响使用它的函数组件的？

4. Capture Value 特性是如何产生的？

`带着疑惑往下看`

#### useState

最简单的 `useState` 用法是这样的：

```
function Counter() {
  var [count, setCount] = useState(0);
 
  return (
    <div>
      <div>{count}</div>
      <Button onClick={() => {setCount(count + 1)}}>
        点击
      </Button>
    </div>
  );
}
```

基于 `useState` 的用法, 我们尝试着自己实现一个 `useState`

```
function useState(initialValue) {
  var state = initialValue;
  function setState(newState) {
    state = newState;
    render();
  }
  return [state, setState];
}
```

这时我们发现，点击 Button 的时候，count 并`不会变化`。

为什么呢？我们没有存储 state，每次渲染 Counter 组件的时候，state 都是`新重置`的。

自然我们就能想到，把 state `提取出来`，存在 useState `外面`。

```
var _state; // 把 state 存储在外面
 
 
function useState(initialValue) {
  _state = _state || initialValue; // 如果没有 _state，说明是第一次执行，把 initialValue 复制给它
  function setState(newState) {
    _state = newState;
    render();
  }
  return [_state, setState];
}
```

`重新渲染组件的时候会读取全局对象的值，拿到的就是最新值了`

#### useEffect

useEffect 是另外一个基础的 Hook，用来处理`副作用`，最简单的用法是这样的：

```
useEffect(() => {
    console.log(count);
 }, [count]);
```

我们知道 useEffect 有几个特点：

1. 有两个参数 `callback` 和 `dependencies` 数组

2. 如果 dependencies 不存在，那么 callback 每次 render 都会执行

3. 如果 dependencies 存在，只有当它`发生了变化`， callback 才会执行



实现一个 useEffect：

```
let _deps; // _deps 记录 useEffect 上一次的 依赖
 
 
function useEffect(callback, depArray) {
  const hasNoDeps = !depArray; // 如果 dependencies 不存在
  const hasChangedDeps = _deps
    ? !depArray.every((el, i) => el === _deps[i]) // 两次的 dependencies 是否完全相等
    : true;
  /* 如果 dependencies 不存在，或者 dependencies 有变化才执行回调函数*/
  if (hasNoDeps || hasChangedDeps) { // dependencies 不存在，或者 dependencies 有变化
    callback();
    _deps = depArray;
  }
}
```

到这里，我们又实现了一个可以工作的 useEffect，似乎没有那么难。

此时我们应该可以解答一个问题：

> 为什么第二个参数是空数组，相当于 componentDidMount ？

因为依赖一直不变化，callback 不会二次执行。



#### Hooks 的复用问题

到现在为止，我们已经实现了可以工作的 useState 和 useEffect。

但是有一个很大的问题：它俩都只能`使用一次`，因为只有一个 _state 和 一个 _deps。

比如

```
const [count, setCount] = useState(0);
const [username, setUsername] = useState('fan');
```

count 和 username 永远是相等的，因为他们共用了一个 _state，并没有地方能分别存储两个值。

我们需要可以`存储多个` _state 和 _deps。



如 《React Hooks: not magic, just arrays》所写，我们可以使用数组，来解决 Hooks 的复用问题。

代码关键在于：

初次渲染的时候，按照 useState，useEffect 的`顺序`，把 state，deps 等`按顺序塞到 memoizedState 数组`中。

更新的时候，`按照顺序`，从 memoizedState 中把上次记录的值拿出来。

```

let memoizedState = []; // hooks 存放在这个数组
let cursor = 0; // 当前 memoizedState 下标
 
 
function useState(initialValue) {
  memoizedState[cursor] = memoizedState[cursor] || initialValue;
  
  const currentCursor = cursor; // 获取当前存储memoizedState下标
  
  function setState(newState) {
    memoizedState[currentCursor] = newState;
    render();
  }
  
  return [memoizedState[cursor++], setState]; // ++自加语法返回当前下标后 才执行加一
  // 返回当前 state，并把 cursor 加 1
}
 
 
function useEffect(callback, depArray) {
  const hasNoDeps = !depArray; // 没有依赖（第二个参数）存在
  
  const deps = memoizedState[cursor]; // 获取当前下标的memoizedState存储的依赖
  
  const hasChangedDeps = deps
    ? !depArray.every((el, i) => el === deps[i]) // every全部相等才会返回true
    : true;
  
  if (hasNoDeps || hasChangedDeps) {
    callback();
    memoizedState[cursor] = depArray;
  }
  
  cursor++;
}
```



#### 图片解读

如果还是不清楚，可以看下面的图。

我们用图来描述 memoizedState 及 cursor 变化的过程。

###### 1. 初始化



![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9kak82ZjVwZW5FY3FqZGZHQzlQVHhDNVVXaE9pYmZsdFlFUFBralhReVVGVUU3aGY2VkJqZGtKTHJlNlF5WGpKT3dIZFp3V0VZUHVBQ2p4YzRncmZmYVEvNjQw?x-oss-process=image/format,png)

###### 2. 初次渲染

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9kak82ZjVwZW5FY3FqZGZHQzlQVHhDNVVXaE9pYmZsdFlTWVhNaFFNR2tnZ25oRDdPN1BIeWJXd1F1T3VpY0wxQlZIaHFpYlpUZ0VCSnVNdVJuM3ZwSUVzdy82NDA?x-oss-process=image/format,png)

###### 3. 事件触发

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9kak82ZjVwZW5FY3FqZGZHQzlQVHhDNVVXaE9pYmZsdFlqVHNLcldYVHNlV3lKR3k0enAxNnRpY085UWJFT0wycTlSNE9RMkpTZVZlaWFpYnQyaGljaWJ6bDJFQS82NDA?x-oss-process=image/format,png)

###### 4. Re-Render

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9kak82ZjVwZW5FY3FqZGZHQzlQVHhDNVVXaE9pYmZsdFlsdFdpYVJZeU1ZZ25oUHEzWW0wZkw3bW9yM1p5U1g3dmRLR1RpY3Z5QWlhb1UySnZHNHdadkhLMncvNjQw?x-oss-process=image/format,png)

到这里，我们实现了一个可以任意复用的 useState 和 useEffect。



`理解：useState存的是值 useEffect存的是依赖，setState的时候改变的是闭包缓存的那个下标的memoizedState的值， 然后下标会重置为0并且组件会重新执行走一遍。一直改变的是memoizedState，而hooks只是函数会重新执行。`



同时，也可以解答几个问题：

Q：为什么只能在函数最外层调用 Hook？为什么不要在循环、条件判断或者子函数中调用？

A：memoizedState 数组是`按hook定义的顺序`来放置数据的，如果 hook `顺序变化`，memoizedState 并`不会感知到`。

Q：自定义的 Hook 是如何影响使用它的函数组件的？

A：共享同一个 memoizedState，共享同一个顺序。

Q："Capture Value" 特性是如何产生的？

A：每一次 ReRender 的时候，都是重新去执行函数组件了，对于之前已经执行过的函数组件，并不会做任何操作。

#### 真正的 React 实现

虽然我们用数组基本实现了一个可用的 Hooks，了解了 Hooks 的原理，但在 React 中，实现方式却有一些差异的。

React 中是通过类似单链表的形式来代替数组的。

源码地址：https://github.com/facebook/react/blob/5f06576f51ece88d846d01abd2ddd575827c6127/packages/react-reconciler/src/ReactFiberHooks.js#L336

通过 next 按顺序串联所有的 hook。

https://github.com/facebook/react/blob/5f06576f51ece88d846d01abd2ddd575827c6127/packages/react-reconciler/src/ReactFiberHooks.js#L477



```
type Hooks = {
  memoizedState: any, // 指向当前渲染节点 Fiber
  baseState: any, // 初始化 initialState， 已经每次 dispatch 之后 newState
  baseUpdate: Update<any> | null,// 当前需要更新的 Update ，每次更新完之后，会赋值上一个 		  	update，方便 react 在渲染错误的边缘，数据回溯
  queue: UpdateQueue<any> | null,// UpdateQueue 通过
  next: Hook | null, // link 到下一个 hooks，通过 next 串联每一 hooks
}
 
type Effect = {
  tag: HookEffectTag, // effectTag 标记当前 hook 作用在 life-cycles 的哪一个阶段
  create: () => mixed, // 初始化 callback
  destroy: (() => mixed) | null, // 卸载 callback
  deps: Array<mixed> | null,
  next: Effect, // 同上
};
```

memoizedState，cursor 是存在哪里的？

如何和每个函数组件一一对应的？

我们知道，react 会生成一棵组件树（或Fiber 单链表），树中每个节点对应了一个组件。

hooks 的数据就作为组件的一个信息，存储在这些节点上，伴随组件一起出生，一起死亡。