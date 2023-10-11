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
	-