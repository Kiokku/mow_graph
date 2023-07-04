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
	-