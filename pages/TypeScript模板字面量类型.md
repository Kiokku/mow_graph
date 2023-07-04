- > https://ts.yayujs.com/handbook/TemplateLiteralTypes.html
-
- ## 模板字面量类型（Template Literal Types）
	- 模板字面量类型以[字符串字面量类型](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#literal-types)为基础，可以通过联合类型扩展成多个字符串。
	- 它们跟 JavaScript 的模板字符串是相同的语法，但是只能用在类型操作中。当使用模板字面量类型时，它会替换模板中的变量，返回一个新的字符串字面量：
	- ```
	  type World = "world";
	   
	  type Greeting = `hello ${World}`;
	  // type Greeting = "hello world"
	  ```
	- 当模板中的变量是一个联合类型时，每一个可能的字符串字面量都会被表示：
	- ```
	  type EmailLocaleIDs = "welcome_email" | "email_heading";
	  type FooterLocaleIDs = "footer_title" | "footer_sendoff";
	   
	  type AllLocaleIDs = `${EmailLocaleIDs | FooterLocaleIDs}_id`;
	  // type AllLocaleIDs = "welcome_email_id" | "email_heading_id" | "footer_title_id" | "footer_sendoff_id"
	  ```
- ## 类型中的字符串联合类型（String Unions in Types）
	- 模板字面量最有用的地方在于你可以基于一个类型内部的信息，定义一个新的字符串，让我们举个例子：
		- 有这样一个函数 `makeWatchedObject`， 它会给传入的对象添加了一个 `on` 方法。在 JavaScript 中，它的调用看起来是这样：`makeWatchedObject(baseObject)`，我们假设这个传入对象为：
		- ```
		  const passedObject = {
		    firstName: "Saoirse",
		    lastName: "Ronan",
		    age: 26,
		  };
		  
		  ```
		- 这个 `on` 方法会被添加到这个传入对象上，该方法接受两个参数，`eventName` （ `string` 类型） 和 `callBack` （`function` 类型）：
		- ```
		  // 伪代码
		  const result = makeWatchedObject(baseObject);
		  result.on(eventName, callBack);
		  ```
		- 我们希望 `eventName` 是这种形式：`attributeInThePassedObject + "Changed"` ，举个例子，`passedObject` 有一个属性 `firstName`，对应产生的 `eventName` 为 `firstNameChanged`，同理，`lastName` 对应的是 `lastNameChanged`，`age` 对应的是 `ageChanged`。
		- 当这个 `callBack` 函数被调用的时候：
			- 1. 应该被传入与 `attributeInThePassedObject` 相同类型的值。比如 `passedObject` 中， `firstName` 的值的类型为 `string` , 对应 `firstNameChanged` 事件的回调函数，则接受传入一个 `string` 类型的值。`age` 的值的类型为 `number`，对应 `ageChanged` 事件的回调函数，则接受传入一个 `number` 类型的值。
			- 2. 返回值类型为 `void` 类型。
		- `on()` 方法的签名最一开始是这样的：`on(eventName: string, callBack: (newValue: any) => void)`。 使用这样的签名，我们是不能实现上面所说的这些约束的，这个时候就可以使用模板字面量：
		- ```
		  const person = makeWatchedObject({
		    firstName: "Saoirse",
		    lastName: "Ronan",
		    age: 26,
		  });
		   
		  // makeWatchedObject has added `on` to the anonymous Object
		  person.on("firstNameChanged", (newValue) => {
		    console.log(`firstName was changed to ${newValue}!`);
		  });
		  ```
		- 注意这个例子里，`on` 方法添加的事件名为 `"firstNameChanged"`， 而不仅仅是 `"firstName"`，而回调函数传入的值 `newValue` ，我们希望约束为 `string` 类型。我们先实现第一点。
		- ```
		  type PropEventSource<Type> = {
		      on(eventName: `${string & keyof Type}Changed`, callback: (newValue: any) => void): void;
		  };
		   
		  /// Create a "watched object" with an 'on' method
		  /// so that you can watch for changes to properties.
		  
		  declare function makeWatchedObject<Type>(obj: Type): Type & PropEventSource<Type>;
		  
		  ```
	- ### 模板字面量的推断（Inference with Template Literals）
	  background-color:: pink
		- 现在我们来实现第二点，回调函数传入的值的类型与对应的属性值的类型相同。我们现在只是简单的对 `callBack` 的参数使用 `any` 类型。实现这个约束的关键在于借助泛型函数：
			- 1. 捕获泛型函数第一个参数的字面量，生成一个字面量类型
			  2. 该字面量类型可以被对象属性构成的联合约束
			  3. 对象属性的类型可以通过索引访问获取
			  4. 应用此类型，确保回调函数的参数类型与对象属性的类型是同一个类型