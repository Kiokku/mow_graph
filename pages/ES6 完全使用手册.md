- > https://github.com/mqyqingfeng/Blog/issues/111
-
- ## 1. let 和 const
	- 在我们开发的时候，可能认为应该默认使用 let 而不是 var，这种情况下，对于需要写保护的变量要使用 const。
	- 然而另一种做法日益普及：[[#green]]==默认使用 const，只有当确实需要改变变量的值的时候才使用 let==。这是因为大部分的变量的值在初始化后不应再改变，而预料之外的变量的修改是很多 bug 的源头。
	- ```
	  // 例子 1-1
	  
	  // bad
	  var foo = 'bar';
	  
	  // good
	  let foo = 'bar';
	  
	  // better
	  const foo = 'bar';
	  ```
- ## 2. 模板字符串
	- ### 1. 模板字符串
	  background-color:: blue
		- 需要拼接字符串的时候尽量改成使用模板字符串:
		- ```
		  // 例子 2-1
		  
		  // bad
		  const foo = 'this is a' + example;
		  
		  // good
		  const foo = `this is a ${example}`;
		  ```
	- ### 2. 标签模板
	  background-color:: blue
		- 可以借助标签模板优化书写方式:
		- ```
		  // 例子 2-2
		  
		  let url = oneLine `
		      www.taobao.com/example/index.html
		      ?foo=${foo}
		      &bar=${bar}
		  `;
		  
		  console.log(url); // www.taobao.com/example/index.html?foo=foo&bar=bar
		  ```
- ## 3. 箭头函数
	- 优先使用箭头函数，不过以下几种情况**避免使用**：
	- ### 1. 使用箭头函数定义对象的方法
	  background-color:: red
		- ```
		  // 例子 3-1
		  
		  // bad
		  let foo = {
		    value: 1,
		    getValue: () => console.log(this.value)
		  }
		  
		  foo.getValue();  // undefined
		  ```
	- ### 2. 定义原型方法
	  background-color:: red
		- ```
		  // 例子 3-2
		  
		  // bad
		  function Foo() {
		    this.value = 1
		  }
		  
		  Foo.prototype.getValue = () => console.log(this.value)
		  
		  let foo = new Foo()
		  foo.getValue();  // undefined
		  ```
	- ### 3. 作为事件的回调函数
	  background-color:: red
		- ```
		  // 例子 3-3
		  
		  // bad
		  const button = document.getElementById('myButton');
		  button.addEventListener('click', () => {
		      console.log(this === window); // => true
		      this.innerHTML = 'Clicked button';
		  });
		  ```
- ## 4. Symbol
	- ### 1. 唯一值
	  background-color:: blue
		- ```
		  // 例子 4-1
		  
		  
		  // bad
		  // 1. 创建的属性会被 for-in 或 Object.keys() 枚举出来
		  // 2. 一些库可能在将来会使用同样的方式，这会与你的代码发生冲突
		  if (element.isMoving) {
		    smoothAnimations(element);
		  }
		  element.isMoving = true;
		  
		  // good
		  if (element.__$jorendorff_animation_library$PLEASE_DO_NOT_USE_THIS_PROPERTY$isMoving__) {
		    smoothAnimations(element);
		  }
		  element.__$jorendorff_animation_library$PLEASE_DO_NOT_USE_THIS_PROPERTY$isMoving__ = true;
		  
		  // better
		  var isMoving = Symbol("isMoving");
		  
		  ...
		  
		  if (element[isMoving]) {
		    smoothAnimations(element);
		  }
		  element[isMoving] = true;
		  ```
	- ### 2. 魔术字符串
	  background-color:: blue
		- 魔术字符串指的是在代码之中多次出现、与代码形成**强耦合**的某一个具体的字符串或者数值。
		- 魔术字符串不利于修改和维护，风格良好的代码，应该尽量消除魔术字符串，改由含义清晰的变量代替。
		- ```
		  // 例子 4-1
		  
		  // bad
		  const TYPE_AUDIO = 'AUDIO'
		  const TYPE_VIDEO = 'VIDEO'
		  const TYPE_IMAGE = 'IMAGE'
		  
		  // good
		  const TYPE_AUDIO = Symbol()
		  const TYPE_VIDEO = Symbol()
		  const TYPE_IMAGE = Symbol()
		  
		  function handleFileResource(resource) {
		    switch(resource.type) {
		      case TYPE_AUDIO:
		        playAudio(resource)
		        break
		      case TYPE_VIDEO:
		        playVideo(resource)
		        break
		      case TYPE_IMAGE:
		        previewImage(resource)
		        break
		      default:
		        throw new Error('Unknown type of resource')
		    }
		  }
		  ```
	- ### 3. 私有变量
	  background-color:: blue
		- [[ES6 系列之私有变量的实现]]
- ## 5. Set 和 Map
	- ### 1. 数组去重
	  background-color:: blue
		- ```
		  // 例子 5-1
		  
		  [...new Set(array)]
		  ```
	- ### 2. 条件语句的优化
	  background-color:: blue
		- ```
		  // 例子 5-2
		  // 根据颜色找出对应的水果
		  
		  // bad
		  function test(color) {
		    switch (color) {
		      case 'red':
		        return ['apple', 'strawberry'];
		      case 'yellow':
		        return ['banana', 'pineapple'];
		      case 'purple':
		        return ['grape', 'plum'];
		      default:
		        return [];
		    }
		  }
		  
		  test('yellow'); // ['banana', 'pineapple']
		  ```
		- ```
		  // good
		  const fruitColor = {
		    red: ['apple', 'strawberry'],
		    yellow: ['banana', 'pineapple'],
		    purple: ['grape', 'plum']
		  };
		  
		  function test(color) {
		    return fruitColor[color] || [];
		  }
		  ```
		- ```
		  // better
		  const fruitColor = new Map()
		    .set('red', ['apple', 'strawberry'])
		    .set('yellow', ['banana', 'pineapple'])
		    .set('purple', ['grape', 'plum']);
		  
		  function test(color) {
		    return fruitColor.get(color) || [];
		  }
		  ```
- ## 6. for of
	- ### 1. 遍历范围
	  background-color:: blue
		- for...of 循环可以使用的范围包括：
			- 1. 数组
			  2. Set
			  3. Map
			  4. 类数组对象，如 arguments 对象、DOM NodeList 对象
			  5. Generator 对象
			  6. 字符串
	- ### 2. 优势
	  background-color:: blue
		- ES2015 引入了 for..of 循环，它结合了 forEach 的简洁性和中断循环的能力：
		- ```
		  // 例子 6-1
		  
		  for (const v of ['a', 'b', 'c']) {
		    console.log(v);
		  }
		  // a b c
		  
		  for (const [i, v] of ['a', 'b', 'c'].entries()) {
		    console.log(i, v);
		  }
		  // 0 "a"
		  // 1 "b"
		  // 2 "c"
		  ```
	- ### 3. 遍历 Map
	  background-color:: blue
		-