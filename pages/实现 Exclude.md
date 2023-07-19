- > https://github.com/type-challenges/type-challenges/blob/main/questions/00043-easy-exclude/README.zh-CN.md
-
- ## 实现 Exclude
  background-color:: red
	- 实现内置的 `Exclude<T, U>` 类型，但不能直接使用它本身。
	- > 从**联合类型** `T` 中排除 `U` 中的类型，来构造一个新的类型。
	- 例如：
	- `type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'`
- ## 解答
  background-color:: blue
	- 题眼：联合类型 `T`， [分发条件类型](logseq://graph/mow_graph?block-id=64a1826f-fe3a-4be9-ba0e-76235916dc53)
	- ```
	  // 本题答案
	  type Exclude<T, U> = T extends U ? never : T
	  ```
	- 实际运行效果：
	- ```
	  type C = Exclude<'a' | 'b', 'a' | 'c'> // 'b'
	  ```
	- 看上去有点不那么好理解，这是因为 TS 对联合类型的执行是分配律的，即：
	- ```
	  Exclude<'a' | 'b', 'a' | 'c'>
	  // 等价于
	  Exclude<'a', 'a' | 'c'> | Exclude<'b', 'a' | 'c'>
	  ```