- ## Unshift
  background-color:: red
	- 实现类型版本的 `Array.unshift`。
	- 例如：
	- ```
	  type Result = Unshift<[1, 2], 0> // [0, 1, 2,]
	  ```
- ## 解答
  background-color:: blue
	- ```
	  type Unshift<T extends any[], U> = [U, ...T]
	  ```