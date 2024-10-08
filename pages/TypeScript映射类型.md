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
	- 在这个例子中，`OptionsFlags` 会遍历 `Type` 所有的属性，然后设置为布尔类型。
	- ```
	  type FeatureFlags = {
	    darkMode: () => void;
	    newUserProfile: () => void;
	  };
	   
	  type FeatureOptions = OptionsFlags<FeatureFlags>;
	  // type FeatureOptions = {
	  //    darkMode: boolean;
	  //    newUserProfile: boolean;
	  // }
	  
	  ```
- ## 映射修饰符（Mapping Modifiers）
	- 在使用映射类型时，有两个额外的修饰符可能会用到，一个是 [[#green]]==`readonly`，用于设置属性只读==，一个是 [[#green]]==`?` ，用于设置属性可选==。
	- 你可以通过前缀 `-` 或者 `+` 删除或者添加这些修饰符，如果没有写前缀，相当于使用了 `+` 前缀。
	- ```
	  // 删除属性中的只读属性
	  type CreateMutable<Type> = {
	    -readonly [Property in keyof Type]: Type[Property];
	  };
	   
	  type LockedAccount = {
	    readonly id: string;
	    readonly name: string;
	  };
	   
	  type UnlockedAccount = CreateMutable<LockedAccount>;
	  
	  // type UnlockedAccount = {
	  //    id: string;
	  //    name: string;
	  // }
	  
	  // 删除属性中的可选属性
	  type Concrete<Type> = {
	    [Property in keyof Type]-?: Type[Property];
	  };
	   
	  type MaybeUser = {
	    id: string;
	    name?: string;
	    age?: number;
	  };
	   
	  type User = Concrete<MaybeUser>;
	  // type User = {
	  //    id: string;
	  //    name: string;
	  //    age: number;
	  // }
	  ```
- ## 通过   `as`   实现键名重新映射（Key Remapping via   `as` ）
	- 在 TypeScript 4.1 及以后，你可以在映射类型中使用 `as` 语句实现键名重新映射：
	- ```
	  type MappedTypeWithNewProperties<Type> = {
	      [Properties in keyof Type as NewKeyType]: Type[Properties]
	  }
	  ```
	- 举个例子，你可以利用[[TypeScript模板字面量类型]]，基于之前的属性名创建一个新属性名：
	- ```
	  type Getters<Type> = {
	      [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property]
	  };
	   
	  interface Person {
	      name: string;
	      age: number;
	      location: string;
	  }
	   
	  type LazyPerson = Getters<Person>;
	  
	  // type LazyPerson = {
	  //    getName: () => string;
	  //    getAge: () => number;
	  //    getLocation: () => string;
	  // }
	  ```
	- 你也可以利用条件类型返回一个 `never` 从而过滤掉某些属性:
	- ```
	  // Remove the 'kind' property
	  type RemoveKindField<Type> = {
	      [Property in keyof Type as Exclude<Property, "kind">]: Type[Property]
	  };
	   
	  interface Circle {
	      kind: "circle";
	      radius: number;
	  }
	   
	  type KindlessCircle = RemoveKindField<Circle>;
	  
	  // type KindlessCircle = {
	  //    radius: number;
	  // }
	  ```
	- 你还可以遍历任何联合类型，不仅仅是 `string | number | symbol` 这种联合类型，可以是任何类型的联合：
	- ```
	  type EventConfig<Events extends { kind: string }> = {
	      [E in Events as E["kind"]]: (event: E) => void;
	  }
	   
	  type SquareEvent = { kind: "square", x: number, y: number };
	  type CircleEvent = { kind: "circle", radius: number };
	   
	  type Config = EventConfig<SquareEvent | CircleEvent>
	  // type Config = {
	  //    square: (event: SquareEvent) => void;
	  //    circle: (event: CircleEvent) => void;
	  // }
	  ```
- ## 深入探索（Further Exploration）
	- 映射类型也可以跟其他的功能搭配使用，举个例子，这是一个使用条件类型的映射类型，会根据对象是否有 `pii` 属性返回 `true` 或者 `false` :
	- ```
	  type ExtractPII<Type> = {
	    [Property in keyof Type]: Type[Property] extends { pii: true } ? true : false;
	  };
	   
	  type DBFields = {
	    id: { format: "incrementing" };
	    name: { type: string; pii: true };
	  };
	   
	  type ObjectsNeedingGDPRDeletion = ExtractPII<DBFields>;
	  // type ObjectsNeedingGDPRDeletion = {
	  //    id: false;
	  //    name: true;
	  // }
	  ```
	-