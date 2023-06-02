---
title: "一次性整明白的 React Hook"
date: 2023-06-01
#description: <descriptive text here>

# weight: 1

# aliases: ["/first"]

tags: ["React Hooks"]
categories: ["React"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
image: "<image path/url>" # image path/url
alt: "<alt text>" # alt text
caption: "<text>" # display caption under cover
relative: false # when using page bundles set this to true
hidden: true # only hide on current single page
---

### useState

useState 可以说是我们日常最常用的 hooks 之一了，在实际使用过程中有一些简单的小技巧能帮助你提升性能 & 减少出 bug 的概率。

- 使用`惰性初始值`[官方文档](https://reactjs.org/docs/hooks-reference.html#lazy-initial-state%EF%BC%89)

  通常我们会使用以下的方式初始化 state

  ```javascript
  const [count, setCount] = useState(0);
  ```

  对于简单的初始值，这样做完全没有任何性能问题，但如果初始值是根据复杂计算得出来的，我们这样写就会产生性能问题。

  ```javascript
  const initialState = heavyCompute(() => {
    /* do some heavy compute here*/
  });
  const [state, useState] = useState(initialState);
  ```

  相信你已经发现这里的问题了对于 useState 的初始值，我们只需要计算一次，但是根据 React Function Component 的渲染逻辑，在每一次 render 的时候，都会重新调用该函数，因此 initialState 在每次 render 时都会被重新计算，哪怕它只在第一次渲染的时候被用到，这无疑会造成严重的性能问题，我们可以通过 useState 的惰性初始值来解决这个问题。

  ```javascript
  // 这样初始值就只会被计算一次
  const [state, setState] = useState(() =>
    heavyCompute(() => {
      /* do some heavy compute here*/
    })
  );
  ```

  `不需要把只需要计算一次的东西直接放在函数组件内部顶层的 block 中`

- 使用 函数式更新 [官方文档](https://zh-hans.legacy.reactjs.org/docs/hooks-reference.html#functional-updates)

  当我们想要更新 state 的时候，我们通常会这样调用 setState。

  ```javascript
  const [state, setState] = useState(0);
  setState(state + 1);
  ```

  看上去没有任何问题，我们看看另一个 case。

  ```javascript
  const Demo: FC = () => {
    const [state, setState] = useState(0);
    useEffect(() => {
      setTimeout(() => setState(state + 1), 3000);
    });

    return <div onClick={() => setState(state + 1)}>{state}</div>;
  };
  ```

  点击 div，我们可以看到计数器增加，那么三秒钟之后，计数器的值会是几呢
  `答案是1`
  这是一个非常反直觉的结果，原因在于第一次运行函数时，state 的值为 0，而 setTimeout 中的回调函数捕获了第一次运行 Demo 函数时 state 的值，也就是 0，所以 setState(state + 1)执行后 state 的值变成了 1，哪怕当前 state 值已经不是 0 了。
  让我们通过`函数式更新`修复这个问题。

  ```javascript
  const Demo: FC = () => {
    const [state, setState] = useState(0);
    useEffect(() => {
      setTimeout(() => setState(prev => prev + 1), 3000);
    });

    return <div onClick={() => setState(state + 1)}>{state}</div>;
  };
  ```

  `让我们再运行一次试试，发现三秒后，state 并没有变成 1，而是增加了 1。`

  直接在 setState 中依赖 state 计算新的 state 在异步执行的函数中会由于 js 闭包捕获得到预期外的结果，此时可以使用 setState(prev => prev + 1)。
