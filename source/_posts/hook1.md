---
title: 学习useEffect完整指南笔记 #文章页面上的显示名称，一般是中文
date: 2020-8-3 13:00 #文章生成时间，一般不改，当然也可以任意修改
categories: react hooks #分类
tags: [react hooks] #文章标签，可空，多标签请用格式，注意:后面有个空格
description: 源地址/ #附加一段文章摘要，字数最好在140字以内，会出现在meta的description里面

---



https://zhuanlan.zhihu.com/p/92211533



[]: https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e	"不是魔术，只是数组"



[]: https://overreacted.io/zh-hans/a-complete-guide-to-useeffect	"useEffect完整指南"



#### 那Effect中的清理又是怎样的呢？



有些 effects 可能需要有一个清理步骤。本质上，它的目的是消除副作用（effect)，比如取消订阅。

```javascript
 useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.id, handleStatusChange);
    };
  });
```

假设第一次渲染的时候`props`是`{id: 10}`，第二次渲染的时候是`{id: 20}`。

你*可能*会认为发生了下面的这些事：



- React 清除了 `{id: 10}`的effect。
- React 渲染`{id: 20}`的UI。
- React 运行`{id: 20}`的effect。

(事实并不是这样。)

​		如果依赖这种心智模型，你可能会认为清除过程“看到”的是旧的props因为它是在重新渲染之前运行的，新的effect“看到”的是新的props因为它是在重新渲染之后运行的。这种心智模型直接来源于class组件的生命周期。不过**它并不精确**。让我们来一探究竟。

React只会在[浏览器绘制](https://medium.com/@dan_abramov/this-benchmark-is-indeed-flawed-c3d6b5b6f97f)后运行effects。这使得你的应用更流畅因为大多数effects并不会阻塞屏幕的更新。Effect的清除同样被延迟了。

**上一次的effect会在重新渲染后被清除：**



- **React 渲染`{id: 20}`的UI。**
- 浏览器绘制。我们在屏幕上看到`{id: 20}`的UI。
- **React 清除`{id: 10}`的effect。**
- React 运行`{id: 20}`的effect。

你可能会好奇：如果清除上一次的effect发生在props变成`{id: 20}`之后，那它为什么还能“看到”旧的`{id: 10}`？

`***组件内的每一个函数（包括事件处理函数，effects，定时器或者API调用等等）会捕获定义它们的那次渲染中的props和state。***`

现在答案显而易见。effect的清除并不会读取“最新”的props。它只能读取到定义它的那次渲染中的props值：

```react
// First render, props are {id: 10}
function Example() {
  // ...
  useEffect(
    // Effect from first render
    () => {
      ChatAPI.subscribeToFriendStatus(10, handleStatusChange);
      // Cleanup for effect from first render  这里有闭包会缓存上次的， 清除副作用
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(10, handleStatusChange);
      };
    }
  );
  // ...
}

// Next render, props are {id: 20}
function Example() {
  // ...
  useEffect(
    // Effect from second render
    () => {
      ChatAPI.subscribeToFriendStatus(20, handleStatusChange);
      // Cleanup for effect from second render
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(20, handleStatusChange);
      };
    }
  );
  // ...
}
```

 		王国会崛起转而复归尘土，太阳会脱落外层变为白矮星，最后的文明也迟早会结束。但是第一次渲染中effect的清除函数只能看到`{id: 10}`这个props。

​		这正是为什么React能做到在绘制后立即处理effects — 并且默认情况下使你的应用运行更流畅。如果你的代码需要依然可以访问到老的props。

#### 同步， 而非生命周期

​		React统一描述了初始渲染和之后的更新。这降低了你程序的[熵](https://overreacted.io/the-bug-o-notation/)。

比如我有个组件像下面这样：

```jsx
function Greeting({ name }) {
  return (
    <h1 className="Greeting">
      Hello, {name}
    </h1>
  );
}
```

​		我先渲染`<Greeting name="Dan" />`然后渲染`<Greeting name="Yuzhi" />`，和我直接渲染`<Greeting name="Yuzhi" />`并没有什么区别。在这两种情况中，我最后看到的都是“Hello, Yuzhi”。

​		在React世界中。**重要的是目的，而不是过程。**这就是JQuery代码中 `$.addClass` 或 `$.removeClass`这样的调用（过程）和React代码中声明CSS类名*应该是什么*（目的）之间的区别。

​		React会根据我们当前的props和state同步到DOM。**“mount”和“update”之于渲染并没有什么区别。

​		你应该以相同的方式去思考effects。**`useEffect`使你能够根据props和state\*同步\*React tree之外的东西。**

```jsx
function Greeting({ name }) {
  useEffect(() => {
    document.title = 'Hello, ' + name;
  });
  return (
    <h1 className="Greeting">
      Hello, {name}
    </h1>
  );
}
```

​		这就是和大家熟知的*mount/update/unmount*心智模型之间细微的区别。理解和内化这种区别是非常重要的。**如果你试图写一个effect会根据是否第一次渲染而表现不一致，你正在逆潮而动。**如果我们的结果依赖于过程而不是目的，我们会在同步中犯错。

#### 两种诚实告知依赖的方法

有两种诚实告知依赖的策略。你应该从第一种开始，然后在需要的时候应用第二种。

**第一种策略是在依赖中包含所有effect中用到的组件内的值。**让我们在依赖中包含`count`：

```jsx
useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1);
  }, 1000);
  return () => clearInterval(id);
}, [count]);
```

​		现在依赖数组正确了。虽然它可能不是*太理想*但确实解决了上面的问题。现在，每次`count`修改都会重新运行effect，并且定时器中的`setCount(count + 1)`会正确引用某次渲染中的 `count`值：

```jsx
// First render, state is 0
function Counter() {
  // ...
  useEffect(
    // Effect from first render
    () => {
      const id = setInterval(() => {
        setCount(0 + 1); // setCount(count + 1)
      }, 1000);
      return () => clearInterval(id);
    },
    [0] // [count]
  );
  // ...
}

// Second render, state is 1
function Counter() {
  // ...
  useEffect(
    // Effect from second render
    () => {
      const id = setInterval(() => {
        setCount(1 + 1); // setCount(count + 1)
      }, 1000);
      return () => clearInterval(id);
    },
    [1] // [count]
  );
  // ...
}
```

这能[解决问题](https://codesandbox.io/s/0x0mnlyq8l)但是我们的定时器会在每一次`count`改变后清除和重新设定。这应该不是我们想要的结果

**第二种策略是修改effect内部的代码以确保它包含的值只会在需要的时候发生变更。**我们不想告知错误的依赖 - 我们只是修改effect使得依赖更少。

让我们来看一些移除依赖的常用技巧。

##### 让Effects自给自足

我们想去掉effect的`count`依赖。

```jsx
 useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
  }, [count]);
```

​		为了实现这个目的，我们需要问自己一个问题：**我们为什么要用`count`？**可以看到我们只在`setCount`调用中用到了`count`。在这个场景中，我们其实并不需要在effect中使用`count`。当我们想要根据前一个状态更新状态的时候，我们可以使用`setState`的[函数形式](https://reactjs.org/docs/hooks-reference.html#functional-updates)：

```jsx
useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);
```

​		我喜欢把类似这种情况称为“错误的依赖”。是的，因为我们在effect中写了`setCount(count + 1)`所以`count`是一个必需的依赖。但是，我们真正想要的是把`count`转换为`count+1`，然后返回给React。可是React其实已经知道当前的`count`。**我们需要告知React的仅仅是去递增状态 - 不管它现在具体是什么值。**

​		尽管effect只运行了一次，第一次渲染中的定时器回调函数可以完美地在每次触发的时候给React发送`c => c + 1`更新指令。它不再需要知道当前的`count`值。因为React已经知道了

##### 解耦来自Actions的更新

我们来修改上面的例子让它包含两个状态：`count` 和 `step`。我们的定时器会每次在count上增加一个`step`值：

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  const [step, setStep] = useState(1);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + step);
    }, 1000);
    return () => clearInterval(id);
  }, [step]);

  return (
    <>
      <h1>{count}</h1>
      <input value={step} onChange={e => setStep(Number(e.target.value))} />
    </>
  );
}
```

​		这个例子目前的行为是修改`step`会重启定时器 - 因为它是依赖项之一。在大多数场景下，这正是你所需要的。清除上一次的effect然后重新运行新的effect并没有任何错。除非我们有很好的理由，我们不应该改变这个默认行为。

​		不过，假如我们不想在`step`改变后重启定时器，我们该如何从effect中移除对`step`的依赖呢？

​	**当你想更新一个状态，并且这个状态更新依赖于另一个状态的值时，你可能需要用`useReducer`去替换它们。**

​		当你写类似`setSomething(something => ...)`这种代码的时候，也许就是考虑使用reducer的契机。reducer可以让你**把组件内发生了什么(actions)和状态如何响应并更新分开表述。**

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
const { count, step } = state;

useEffect(() => {
  const id = setInterval(() => {
    dispatch({ type: 'tick' }); // Instead of setCount(c => c + step);
  }, 1000);
  return () => clearInterval(id);
}, [dispatch]);


```

```jsx
const initialState = {
  count: 0,
  step: 1,
};

function reducer(state, action) {
  const { count, step } = state;
  if (action.type === 'tick') {
    return { count: count + step, step };
  } else if (action.type === 'step') {
    return { count, step: action.step };
  } else {
    throw new Error();
  }
}
```

你可能会问：“这怎么就更好了？”答案是**React会保证`dispatch`在组件的声明周期内保持不变。所以上面例子中不再需要重新订阅定时器。**

相比于直接在effect里面读取状态，它dispatch了一个*action*来描述发生了什么。这使得我们的effect和`step`状态解耦。我们的effect不再关心怎么更新状态，它只负责告诉我们发生了什么。更新的逻辑全都交由reducer去统一处理

##### useReducer是Hooks的作弊模式

​		我们已经学习到如何移除effect的依赖，不管状态更新是依赖上一个状态还是依赖另一个状态。**但假如我们需要依赖\*props\*去计算下一个状态呢？**举个例子，也许我们的API是`<Counter step={1} />`。确定的是，在这种情况下，我们没法避免依赖`props.step` 。是吗？

实际上， 我们可以避免！我们可以把*reducer*函数放到组件内去读取props：

```jsx
function Counter({ step }) {
  const [count, dispatch] = useReducer(reducer, 0);

  function reducer(state, action) {
    if (action.type === 'tick') {
      return state + step;
    } else {
      throw new Error();
    }
  }

  useEffect(() => {
    const id = setInterval(() => {
      dispatch({ type: 'tick' });
    }, 1000);
    return () => clearInterval(id);
  }, [dispatch]);

  return <h1>{count}</h1>;
}
```

这种模式会使一些优化失效，所以你应该避免滥用它，不过如果你需要你完全可以在reducer里面访问props。

**即使是在这个例子中，React也保证`dispatch`在每次渲染中都是一样的。** 所以你可以在依赖中去掉它。它不会引起effect不必要的重复执行。

你可能会疑惑：这怎么可能？在之前渲染中调用的reducer怎么“知道”新的props？答案是当你`dispatch`的时候，React只是记住了action - 它会在下一次渲染中再次调用reducer。在那个时候，新的props就可以被访问到，而且reducer调用也不是在effect里。

**这就是为什么我倾向认为`useReducer`是Hooks的“作弊模式”。它可以把更新逻辑和描述发生了什么分开。结果是，这可以帮助我移除不必需的依赖，避免不必要的effect调用。**