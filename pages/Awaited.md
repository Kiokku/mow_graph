- > https://github.com/type-challenges/type-challenges/blob/main/questions/00189-easy-awaited/README.zh-CN.md
-
- ## Awaited
  background-color:: red
	- 假如我们有一个 Promise 对象，这个 Promise 对象会返回一个类型。在 TS 中，我们用 Promise 中的 T 来描述这个 Promise 返回的类型。请你实现一个类型，可以获取这个类型。
	- 例如：`Promise<ExampleType>`，请你返回 ExampleType 类型。
	- ```
	  type ExampleType = Promise<string>
	  
	  type Result = MyAwaited<ExampleType> // string
	  ```
- ## 解答
  background-color:: blue
	- 首先 TS 永远不会执行代码，所以脑子里不要有 “await 得等一下才知道结果” 的念头。该题关键就是从 `Promise<T>` 中抽取类型 `T`，很适合用 `infer` 做：
	- ```
	  type MyAwaited<T> = T extends Promise<infer U> ? U : never
	  ```
	- 然而这个答案还不够标准，标准答案考虑了[[#blue]]==嵌套 `Promise` 的场景==：
	- ```
	  type MyAwaited<T extends Promise<unknow>> = T extends Promise<infer P> ?
	  	P extends Promise<unknow> ? MyAwaited<P> : P
	      : never
	  ```
	- 如果 `Promise<P>` 取到的 `P` 还形如 `Promise<unknown>`，就递归调用自己 `MyAwaited<P>`。