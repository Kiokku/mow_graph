- > https://github.com/type-challenges/type-challenges/blob/main/questions/03057-easy-push/README.zh-CN.md
-
- ## Push
  background-color:: red
	- 在类型系统里实现通用的 `Array.push` 。
	- 例如：
	- ```
	  type Result = Push<[1, 2], '3'> // [1, 2, '3']
	  ```
- ## 解答
  background-color:: blue
	- ```
	  type Push<T extensa any[], U> = [...T, U]
	  ```