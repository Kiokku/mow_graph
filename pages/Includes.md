- > https://github.com/type-challenges/type-challenges/blob/main/questions/00898-easy-includes/README.zh-CN.md
-
- ## Includes
  background-color:: red
	- 在类型系统里实现 JavaScript 的 `Array.includes` 方法，这个类型接受两个参数，返回的类型要么是 `true` 要么是 `false`。
	- 例如：
	- ```
	  type isPillarMen = Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Dio'> // expected to be `false`
	  ```
- ## 解答
  background-color:: blue
	- 由于之前的经验，很容易做下面的联想：
	- ```
	  // 如果题目要求是这样
	  type isPillarMen = Includes<'Kars' | 'Esidisi' | 'Wamuu' | 'Santana', 'Dio'>
	  // 那我就能用 extends 轻松解决了
	  type Includes<T, K> = K extends T ? true : false
	  ```
	- 可惜第一个输入是数组类型，[[#red]]==`extends` 可不支持判定 “数组包含” 逻辑==，此时要了解一个新知识点，即 TS 判断中的 `[number]` 下标。不仅这道题，以后很多困难题都需要它作为基础知识。
	- [[#green]]==`[number]` 下标表示任意一项，而 `extends T[number]` 就可以实现数组包含的判定，==因此下面的解法是有效的：
	- ```
	  type Includes<T extends any[], K> = K extends T[number] ? true : false
	  ```
	- 但翻答案后发现这并不是标准答案，还真找到一个反例：
	- ```
	  type Includes<T extends any[], K> = K extends T[number] ? true : false
	  type isPillarMen = Includes<[boolean], false> // true
	  ```
	- 原因很简单，`true`、`false` 都继承自 `boolean`，所以 `extends` 判断的界限太宽了，题目要求的是**精确值匹配**，故上面的答案理论上是错的。
	- [[#blue]]==标准答案是每次判断数组第一项，并递归（讲真觉得这不是 easy 题），分别有两个难点。==
		- 第一如何写 Equal 函数？比较流行的方案是这个：
		- ```
		  type isEqual<X, Y> =
		    (<T>() => T extends X ? 1 : 2) extends
		    (<T>() => T extends Y ? 1 : 2)
		    	? true
		      : false
		  ```
		- 有了 `Equal` 后就简单了，我们用解构 + `infer` + 递归的方式做就可以了：
		- ```
		  type Includes<T includes any[], K> =
		  	isEqual<T[0], K> extends true
		  		? true
		          : Value extends [Value[0], ...infer rest]
		          	? Includes<rest, K>
		              : false;
		  ```
-