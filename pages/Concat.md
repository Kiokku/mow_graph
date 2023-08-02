- > https://github.com/type-challenges/type-challenges/blob/main/questions/00533-easy-concat/README.zh-CN.md
-
- ## Concat
  background-color:: red
	- 在类型系统里实现 JavaScript 内置的 `Array.concat` 方法，这个类型接受两个参数，返回的新数组类型应该按照输入参数从左到右的顺序合并为一个新的数组。
	- 例如：
	- ```
	  type Result = Concat<[1], [2]> // expected to be [1, 2]
	  ```
- ## 解答
  background-color:: blue
	- ```
	  type Concat<P extends any[], Q extends any[]> = [...P, ...Q]
	  ```
	- TS 支持数组解构语法`...`