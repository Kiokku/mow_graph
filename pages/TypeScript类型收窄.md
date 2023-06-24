- > https://ts.yayujs.com/handbook/Narrowing.html
-
- ## 类型收窄（Narrowing）
	- TypeScript 的类型检查器会考虑到比如 `if/else` 、三元运算符、循环、真值检查等情况下的类型分析，而这个**将类型推导为更精确类型的过程，我们称之为收窄 (narrowing)**。
- ## typeof 类型保护（type guards）
	- 在 TypeScript 中，检查 `typeof` 返回的值就是一种**类型保护**。TypeScript 知道 `typeof` 不同值的结果，看下面这个例子：
	- ```
	  function printAll(strs: string | string[] | null) {
	    if (typeof strs === "object") {
	      for (const s of strs) {
	  		  // Object is possibly 'null'.
	        console.log(s);
	      }
	    } else if (typeof strs === "string") {
	      console.log(strs);
	    } else {
	      // do nothing
	    }
	  }
	  ```
	- 在这个 `printAll` 函数中，我们尝试判断 `strs` 是否是一个对象，原本的目的是判断它是否是一个数组类型，但是[[#red]]==在 JavaScript 中，`typeof null` 也会返回 `object`==。而这是 JavaScript 一个不幸的历史事故。
	- 熟练的用户自然不会感到惊讶，但也并不是所有人都如此熟练。不过幸运的是，[[#green]]==TypeScript 会让我们知道 `strs` 被收窄为 `strings[] | null` ，而不仅仅是 `string[]`。==
- ## 真值收窄（Truthiness narrowing）
	-