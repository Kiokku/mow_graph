- > https://github.com/type-challenges/type-challenges/blob/main/questions/03312-easy-parameters/README.zh-CN.md
-
- ## Parameters
  background-color:: red
	- 实现内置的 Parameters 类型，而不是直接使用它，可参考[TypeScript官方文档](https://www.typescriptlang.org/docs/handbook/utility-types.html#parameterstype)。
	- 例如：
	- ```
	  const foo = (arg1: string, arg2: number): void => {}
	  
	  type FunctionParamsType = MyParameters<typeof foo> // [arg1: string, arg2: number]
	  ```
- ## 解答
  background-color:: blue
	- `type Parameters<T> = T extends (...args: infer P) => any ? P : []`
	- `infer` 可以很方便从任何具体的位置取值