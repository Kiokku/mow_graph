- > https://github.com/type-challenges/type-challenges/blob/main/questions/00003-medium-omit/README.zh-CN.md
-
- ## 实现 Omit
  background-color:: red
	- 不使用 `Omit` 实现 TypeScript 的 `Omit<T, K>` 泛型。
	- `Omit` 会创建一个省略 `K` 中字段的 `T` 对象。
	- 例如：
	- ```
	  interface Todo {
	    title: string
	    description: string
	    completed: boolean
	  }
	  
	  type TodoPreview = MyOmit<Todo, 'description' | 'title'>
	  
	  const todo: TodoPreview = {
	    completed: false,
	  }
	  ```
- ## 解答
  background-color:: blue
	- 实现 `Omit<T, K>`，作用恰好与 `Pick<T, K>` 相反
	- 比较容易尝试的方案是：
		- ```
		  type MyOmit<T, K extends keyof T> = {
		    [P in keyof T]: P extends K ? never : T[P]
		  }
		  ```
		- [[#red]]==其实仍然包含了 `description`、`title` 这两个 Key，只是这两个 Key 类型为 `never`，不符合要求。==
	- 所以只要 `P in keyof T` 写出来了，后面怎么写都无法将这个 Key 抹去，我们应该从 Key 下手：
		- ```
		  type MyOmit<T, K extends keyof T> = {
		    [P in (keyof T extends K ? never : keyof T)]: T[P]
		  }
		  ```
		- 但这样写仍然不对，我们思路正确，即把 `keyof T` 中归属于 `K` 的排除，但[[#red]]==因为前后 `keyof T` 并没有关联==，所以需要借助 `Exclude` 告诉 TS，前后 `keyof T` 是同一个指代（实现过 `Exclude`）：
		- ```
		  // 本题答案
		  type MyOmit<T, K extends keyof T> = {
		    [P in Exclude<keyof T, K>]: T[P]
		  }
		  
		  type Exclude<T, U> = T extends U ? never : T
		  ```
	- [[#green]]==当然既然都用上了 `Exclude`，我们不如再结合 `Pick`，写出更优雅的 `Omit` 实现：==
		- ```
		  // 本题优雅答案
		  type MyOmit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>
		  ```