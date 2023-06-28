- > https://ts.yayujs.com/handbook/TypeofTypeOperator.html
-
- ## `typeof`   类型操作符（The   `typeof`   type operator）
	- TypeScript 添加的 `typeof` 方法可以在类型上下文（type context）中使用，用于获取一个变量或者属性的类型。
	- ```
	  let s = "hello";
	  let n: typeof s;
	  // let n: string
	  ```
- ## 限制（Limitations）
	- TypeScript 有意的限制了可以使用 `typeof` 的表达式的种类。
	-