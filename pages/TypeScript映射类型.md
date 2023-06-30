- > https://ts.yayujs.com/handbook/MappedTypes.html
-
- ## 映射类型（Mapped Types）
	- 有的时候，一个类型需要基于另外一个类型，但是你又不想拷贝一份，这个时候可以考虑使用映射类型。
	- 映射类型建立在索引签名的语法上，我们先回顾下索引签名：
	- ```
	  // 当你需要提前声明属性的类型时
	  type OnlyBoolsAndHorses = {
	    [key: string]: boolean | Horse;
	  };
	   
	  const conforms: OnlyBoolsAndHorses = {
	    del: true,
	    rodney: false,
	  };
	  ```
	- 而映射类型，就是使用了 `PropertyKeys` 联合类型的泛型，其中 `PropertyKeys` 多是通过 `keyof` 创建，然后循环遍历键名创建一个类型：
	- ```
	  type OptionsFlags<Type> = {
	    [Property in keyof Type]: boolean;
	  };
	  ```
	-