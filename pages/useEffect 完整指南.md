- > https://overreacted.io/zh-hans/a-complete-guide-to-useeffect/
- ## 每一次渲染都有它自己的 Props and State
  background-color:: pink
	- **当我们更新状态的时候，React会重新渲染组件。每一次渲染都能拿到独立的`state` 状态，这个状态值是函数中的一个常量。**
	- 这里关键的点在于任意一次渲染中的`state`常量都不会随着时间改变。渲染输出会变是因为我们的组件被一次次调用，而每一次调用引起的渲染中，它包含的`state`值独立于其他渲染。
- ## 每一次渲染都有它自己的事件处理函数
  background-color:: pink
	- ```
	  function Counter() {
	    const [count, setCount] = useState(0);
	  
	    function handleAlertClick() {
	      setTimeout(() => {
	        alert('You clicked on: ' + count);
	      }, 3000);
	    }
	  
	    return (
	      <div>
	        <p>You clicked {count} times</p>
	        <button onClick={() => setCount(count + 1)}>
	          Click me
	        </button>
	        <button onClick={handleAlertClick}>
	          Show alert
	        </button>
	      </div>
	    );
	  }
	  
	  // 0
	  // 1
	  // 2
	  ...
	  ```
	- 我们发现`count`在每一次函数调用中都是一个常量值。值得强调的是 — **我们的组件函数每次渲染都会被调用，但是每一次调用中`count`值都是常量，并且它被赋予了当前渲染中的状态值。**
	- ```
	  // During first render
	  function Counter() {
	  const count = 0; // Returned by useState()  // ...
	  function handleAlertClick() {
	    setTimeout(() => {
	      alert('You clicked on: ' + count);
	    }, 3000);
	  }
	  // ...
	  }
	  - // After a click, our function is called again
	  function Counter() {
	  const count = 1; // Returned by useState()  // ...
	  function handleAlertClick() {
	    setTimeout(() => {
	      alert('You clicked on: ' + count);
	    }, 3000);
	  }
	  // ...
	  }
	  - // After another click, our function is called again
	  function Counter() {
	  const count = 2; // Returned by useState()  // ...
	  function handleAlertClick() {
	    setTimeout(() => {
	      alert('You clicked on: ' + count);
	    }, 3000);
	  }
	  // ...
	  }
	  ```
	- 所以实际上，每一次渲染都有一个“新版本”的`handleAlertClick`。每一个版本的`handleAlertClick`“记住” 了它自己的 `count`。
	- **在任意一次渲染中，props和state是始终保持不变的。**如果props和state在不同的渲染中是相互独立的，那么使用到它们的任何值也是独立的（包括事件处理函数）。它们都“属于”一次特定的渲染。即便是事件处理中的异步函数调用“看到”的也是这次渲染中的`count`值。
- ## 每一次渲染都有它自己的Effects
  background-color:: pink
	- ```
	  function Counter() {
	    const [count, setCount] = useState(0);
	  
	    useEffect(() => {
	      document.title = `You clicked ${count} times`;
	    });
	  
	    return (
	      <div>
	        <p>You clicked {count} times</p>
	        <button onClick={() => setCount(count + 1)}>
	          Click me
	        </button>
	      </div>
	    );
	  }
	  ```
	- 我们已经知道`count`是某个特定渲染中的常量。事件处理函数“看到”的是属于它那次特定渲染中的`count`状态值。对于effects也同样如此：
		- **并不是`count`的值在“不变”的effect中发生了改变，而是*effect 函数本身*在每一次渲染中都不相同。**
		- 每一个effect版本“看到”的`count`值都来自于它属于的那次渲染。
	- **概念上，你可以想象effects是渲染结果的一部分。**
	- 严格地说，它们并不是（为了[允许Hook的组合](https://overreacted.io/why-do-hooks-rely-on-call-order/)并且不引入笨拙的语法或者运行时）。但是在我们构建的心智模型上，effect函数*属于*某个特定的渲染，就像事件处理函数一样。
- ## 逆潮而动
  background-color:: purple
	- > 到目前为止，我们可以明确地喊出下面重要的事实：**每一个**组件内的函数（包括事件处理函数，effects，定时器或者API调用等等）会捕获某次渲染中定义的props和state。
	- **在组件内什么时候去读取props或者state是无关紧要的。**因为它们不会改变。在单次渲染的范围内，props和state始终保持不变。
	- 当然，有时候你可能*想*在effect的回调函数里读取最新的值而不是捕获的值。最简单的实现方法是使用`refs`。
	- 需要注意的是当你想要从[[#green]]==*过去*==渲染中的函数里读取[[#green]]==*未来*==的props和state，你是在[[#red]]==逆潮而动==。虽然它并没有*错*（有时候可能也需要这样做），但它因为打破了默认范式会使代码显得不够“干净”。这是我们有意为之的，因为它能帮助突出哪些代码是脆弱的，是需要依赖时间次序的。
	- ```
	  function Example() {
	    const [count, setCount] = useState(0);
	    const latestCount = useRef(count);
	  
	    useEffect(() => {
	      // Set the mutable latest value
	      latestCount.current = count;
	      setTimeout(() => {
	        // Read the mutable latest value
	        console.log(`You clicked ${latestCount.current} times`);
	      }, 3000);
	    });
	    // ...
	  ```
- ## Effect中的清理
  background-color:: pink
	- 本质上，它的目的是消除副作用（effect)，比如取消订阅。
	- ```
	  useEffect(() => {
	      ChatAPI.subscribeToFriendStatus(props.id, handleStatusChange);
	      return () => {
	        ChatAPI.unsubscribeFromFriendStatus(props.id, handleStatusChange);
	      };
	  });
	  ```
	- 假设第一次渲染的时候`props`是`{id: 10}`，第二次渲染的时候是`{id: 20}`：
		- 1. **React 渲染`{id: 20}`的UI。**
		  2. 浏览器绘制。我们在屏幕上看到`{id: 20}`的UI。
		  3. **React 清除`{id: 10}`的effect。**
		  4. React 运行`{id: 20}`的effect。
	- React只会在[浏览器绘制](https://medium.com/@dan_abramov/this-benchmark-is-indeed-flawed-c3d6b5b6f97f)后运行effects。这使得你的应用更流畅因为大多数effects并不会阻塞屏幕的更新。[[#green]]==Effect的清除同样被延迟了。**上一次的effect会在重新渲染后被清除。**==
- ## 同步， 而非生命周期
  background-color:: pink
	- **React会根据我们当前的props和state同步到DOM。**“mount”和“update”之于渲染并没有什么区别。
	- 你应该以相同的方式去思考effects。**`useEffect`使你能够根据props和state*同步*React tree之外的东西。**
	- 不过话说回来，在*每一次*渲染后都去运行所有的effects可能并不高效。（并且在某些场景下，它可能会导致无限循环。）
	- 所以我们该怎么解决这个问题？
- ## 告诉React去比对你的Effects
  background-color:: pink
	- 如果想要避免effects不必要的重复调用，你可以提供给`useEffect`一个依赖数组参数(deps)：
		- ```
		  useEffect(() => {
		      document.title = 'Hello, ' + name;
		  }, [name]); // Our deps
		  ```
		- 如果当前渲染中的这些依赖项和上一次运行这个effect的时候值一样，因为没有什么需要同步React会自动跳过这次effect。
- ## 关于依赖项不要对React撒谎
  background-color:: purple
	- ```
	  function SearchResults() {
	    async function fetchData() {
	      // ...
	    }
	  
	    useEffect(() => {
	      fetchData();
	    }, []); // Is this okay? Not always -- and there's a better way to write it.
	  
	    // ...
	  }
	  ```
	- **只需要记住：**如果你设置了依赖项，**effect中用到的所有组件内的值都要包含在依赖中。**这包括props，state，函数 — 组件内的任何东西。
	- 有时候你是这样做了，但可能会引起一个问题。比如，你可能会遇到无限请求的问题，或者socket被频繁创建的问题。**解决问题的方法不是移除依赖项。**我们会很快了解具体的解决方案。
- ## 如果设置了错误的依赖会怎么样呢？
  background-color:: pink
	- 举个例子，我们来写一个每秒递增的计数器。在Class组件中，我们的直觉是：“开启一次定时器，清除也是一次”。这里有一个[例子](https://codesandbox.io/s/n5mjzjy9kl)说明怎么实现它。当我们理所当然地把它用`useEffect`的方式翻译，直觉上我们会设置依赖为`[]`。“我只想运行一次effect”，对吗？
	- ```
	  function Counter() {
	    const [count, setCount] = useState(0);
	  
	    useEffect(() => {
	      const id = setInterval(() => {
	        setCount(count + 1);
	      }, 1000);
	      return () => clearInterval(id);
	    }, []);
	  
	    return <h1>{count}</h1>;
	  }
	  ```
	- 然而，这个例子[只会递增一次](https://codesandbox.io/s/91n5z8jo7r)。
	- 定时器依然在运行，但取得的`count`的值一直是`1`：
		- 在第一次渲染中，`count`是`0`。因此，`setCount(count + 1)`在第一次渲染中等价于`setCount(0 + 1)`。**既然我们设置了`[]`依赖，effect不会再重新运行，它后面每一秒都会调用`setCount(0 + 1)`。
	- 类似于这样的问题是很难被想到的。因此，我鼓励你将诚实地告知effect依赖作为一条硬性规则，并且要列出所以依赖。（我们提供了一个[lint规则](https://github.com/facebook/react/issues/14920)如果你想在你的团队内做硬性规定。）
- ## 两种诚实告知依赖的方法
  background-color:: pink
	- **第一种策略是在依赖中包含所有effect中用到的组件内的值。**
		- ```
		  useEffect(() => {
		    const id = setInterval(() => {
		      setCount(count + 1);
		    }, 1000);
		    return () => clearInterval(id);
		  }, [count]);
		  ```
		- 这能[解决问题](https://codesandbox.io/s/0x0mnlyq8l)但是我们的定时器会在每一次`count`改变后清除和重新设定。这应该不是我们想要的结果。
	- **第二种策略是修改effect内部的代码以确保它包含的值只会在需要的时候发生变更。**我们不想告知错误的依赖 - 我们只是修改effect使得依赖更少。
- ## 让Effects自给自足
  background-color:: pink
	- 当我们想要根据前一个状态更新状态的时候，我们可以使用`setState`的[函数形式](https://reactjs.org/docs/hooks-reference.html#functional-updates)：
		- ```
		  useEffect(() => {
		      const id = setInterval(() => {
		        setCount(c => c + 1);
		      }, 1000);
		      return () => clearInterval(id);
		  }, []);
		  ```
	- 因为我们在effect中写了`setCount(count + 1)`所以`count`是一个必需的依赖。但是，我们真正想要的是把`count`转换为`count+1`，然后返回给React。可是React其实已经知道当前的`count`。**我们需要告知React的仅仅是去递增状态 - 不管它现在具体是什么值。**
- ## 解耦来自Actions的更新
  background-color:: pink
	- 我们来修改上面的例子让它包含两个状态：`count` 和 `step`。我们的定时器会每次在count上增加一个`step`值：
	- ```
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
	- 这个例子目前的行为是修改`step`会重启定时器 - 因为它是依赖项之一。在大多数场景下，这正是你所需要的。清除上一次的effect然后重新运行新的effect并没有任何错。除非我们有很好的理由，我们不应该改变这个默认行为。
	- 不过，假如我们不想在`step`改变后重启定时器，我们该如何从effect中移除对`step`的依赖呢？
	- **当你想更新一个状态，并且这个状态更新依赖于另一个状态的值时，你可能需要用`useReducer`去替换它们。**
	- 当你写类似`setSomething(something => ...)`这种代码的时候，也许就是考虑使用reducer的契机。reducer可以让你**把组件内发生了什么(actions)和状态如何响应并更新分开表述。**
	- ```
	  const [state, dispatch] = useReducer(reducer, initialState);
	  const { count, step } = state;
	  
	  useEffect(() => {
	    const id = setInterval(() => {
	      dispatch({ type: 'tick' }); // Instead of setCount(c => c + step);
	    }, 1000);
	    return () => clearInterval(id);
	  }, [dispatch]);
	  ```
	- > 你可以从依赖中去除`dispatch`, `setState`, 和`useRef`包裹的值因为React会确保它们是静态的。不过你设置了它们作为依赖也没什么问题。
	- 相比于直接在effect里面读取状态，它dispatch了一个*action*来描述发生了什么。这使得我们的effect和`step`状态解耦。我们的effect不再关心怎么更新状态，它只负责告诉我们发生了什么。更新的逻辑全都交由reducer去统一处理:
	- ```
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
- ## 把函数移到Effects里
  background-color:: pink
	- 感谢`eslint-plugin-react-hooks` 插件的`exhaustive-deps`lint规则，它会在你[编码的时候就分析effects](https://github.com/facebook/react/issues/14920)并且提供可能遗漏依赖的建议。换句话说，机器会告诉你组件中哪些数据流变更没有被正确地处理。
- ## 但我不能把这个函数放到Effect里
  background-color:: pink
	- 你可以把它包装成 [`useCallback` Hook](https://reactjs.org/docs/hooks-reference.html#usecallback):
	- ```
	  function SearchResults() {
	    // ✅ Preserves identity when its own deps are the same
	    const getFetchUrl = useCallback((query) => {
	      return 'https://hn.algolia.com/api/v1/search?query=' + query;
	    }, []);  // ✅ Callback deps are OK
	  
	    useEffect(() => {
	      const url = getFetchUrl('react');
	      // ... Fetch data and do something ...
	    }, [getFetchUrl]); // ✅ Effect deps are OK
	  
	    useEffect(() => {
	      const url = getFetchUrl('redux');
	      // ... Fetch data and do something ...
	    }, [getFetchUrl]); // ✅ Effect deps are OK
	  
	    // ...
	  }
	  ```
	- `useCallback`本质上是添加了一层依赖检查。它以另一种方式解决了问题 - **我们使函数本身只在需要的时候才改变，而不是去掉对函数的依赖。**
	- 这正是拥抱数据流和同步思维的结果。**对于通过属性从父组件传入的函数这个方法也适用。**
- ## 说说竞态
  background-color:: purple
	- 有问题的原因是请求结果返回的顺序不能保证一致。比如我先请求 `{id: 10}`，然后更新到`{id: 20}`，但`{id: 20}`的请求更先返回。请求更早但返回更晚的情况会错误地覆盖状态值。
	- 这被叫做竞态，这在混合了`async` / `await`（假设在等待结果返回）和自顶向下数据流的代码中非常典型（props和state可能会在async函数调用过程中发生改变）。
	- Effects并没有神奇地解决这个问题，尽管它会警告你如果你直接传了一个`async` 函数给effect。（我们会改善这个警告来更好地解释你可能会遇到的这些问题。）
	- 如果你使用的异步方式支持取消，那太棒了。你可以直接在清除函数中取消异步请求。
	- 或者，最简单的权宜之计是用一个布尔值来跟踪它：
	- ```
	  function Article({ id }) {
	    const [article, setArticle] = useState(null);
	  
	    useEffect(() => {
	      let didCancel = false;
	  
	      async function fetchData() {
	        const article = await API.fetchArticle(id);
	        if (!didCancel) {
	          setArticle(article);
	        }
	      }
	  
	      fetchData();
	  
	      return () => {
	        didCancel = true;
	      };
	    }, [id]);
	  
	    // ...
	  }
	  ```
	-