- > https://github.com/type-challenges/type-challenges/blob/main/questions/00043-easy-exclude/README.zh-CN.md
-
- ## 实现 Exclude
  background-color:: red
	- 实现内置的 `Exclude<T, U>` 类型，但不能直接使用它本身。
	- > 从联合类型 `T` 中排除 `U` 中的类型，来构造一个新的类型。
	- 例如：
	- `type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'`
- ## 解答
  background-color:: blue
	-