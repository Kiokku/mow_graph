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
	- 在 TypeScript 中，只有对标识符（比如变量名）或者他们的属性使用 `typeof` 才是合法的。这可能会导致一些令人迷惑的问题：
	- ```
	  // Meant to use = ReturnType<typeof msgbox>
	  let shouldContinue: typeof msgbox("Are you sure you want to continue?");
	  // ',' expected.
	  ```
- ## 对对象使用   `typeof`
	- ```
	  const person = { name: "kevin", age: "18" }
	  type Kevin = typeof person;
	  
	  // type Kevin = {
	  // 		name: string;
	  // 		age: string;
	  // }
	  ```
- ## 对函数使用   `typeof`
	- ```
	  function identity<Type>(arg: Type): Type {
	    return arg;
	  }
	  
	  type result = typeof identity;
	  // type result = <Type>(arg: Type) => Type
	  ```
- ## 对 enum 使用   `typeof`
	- 在 TypeScript 中，enum 是一种新的数据类型，但在具体运行的时候，它会被编译成对象。
	- ```
	  enum UserResponse {
	    No = 0,
	    Yes = 1,
	  }
	  
	  ```
	- 对应编译的 JavaScript 代码为：
	- ```
	  var UserResponse;
	  (function (UserResponse) {
	      UserResponse[UserResponse["No"] = 0] = "No";
	      UserResponse[UserResponse["Yes"] = 1] = "Yes";
	  })(UserResponse || (UserResponse = {}));
	  ```
	- 而如果我们对 `UserResponse` 使用 `typeof`：
	- ```
	  type result = typeof UserResponse;
	  
	  // ok
	  const a: result = {
	        "No": 2,
	        "Yes": 3
	  }
	  
	  result 类型类似于：
	  
	  // {
	  //	"No": number,
	  //  "YES": number
	  // }
	  ```
	- 不过对一个 enum 类型只使用 `typeof` 一般没什么用，通常还会搭配 `keyof` 操作符用于获取属性名的联合字符串：
	- ```
	  type result = keyof typeof UserResponse;
	  // type result = "No" | "Yes"
	  ```