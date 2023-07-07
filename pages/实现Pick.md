- > https://github.com/type-challenges/type-challenges/blob/main/questions/00004-easy-pick/README.zh-CN.md
-
- ## 实现 Pick
  background-color:: red
	- 不使用 `Pick<T, K>` ，实现 TS 内置的 `Pick<T, K>` 的功能。
	- **从类型 `T` 中选出符合 `K` 的属性，构造一个新的类型**。
	- 例如：
	- ```
	  interface Todo {
	    title: string
	    description: string
	    completed: boolean
	  }
	  
	  type TodoPreview = MyPick<Todo, 'title' | 'completed'>
	  
	  const todo: TodoPreview = {
	      title: 'Clean room',
	      completed: false,
	  }
	  ```
-
- ## 解答
  background-color:: blue
	- ```
	  type MyPick<T, K extends keyof T> = {
	    [key in K]: T[key]
	  }
	  ```
	- #### `keyof`: 取interface的键后保存为联合类型
	  background-color:: green
		- ```
		  interface userInfo {
		    name: string
		    age: number
		  }
		  type keyofValue = keyof userInfo
		  // keyofValue = "name" | "age"
		  ```
	- #### `in`: 取联合类型的值，主要用于数组和对象的构建
	  background-color:: green
		- 切记不要用于interface, 否则会报错
		- ```
		  type name = 'firstname' | 'lastname'
		  type TName = {
		    [key in name]: string
		  }
		  // TName = { firstname: string, lastname: string }
		  ```