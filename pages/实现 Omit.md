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
	- 其实仍然包含了 `description`、`title` 这两个 Key，只是这两个 Key 类型为 `never`，不符合要求。
	-