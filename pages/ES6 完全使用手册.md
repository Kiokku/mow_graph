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
		- ```
		  // 例子 6-2
		  
		  let map = new Map(arr);
		  
		  // 遍历 key 值
		  for (let key of map.keys()) {
		    console.log(key);
		  }
		  
		  // 遍历 value 值
		  for (let value of map.values()) {
		    console.log(value);
		  }
		  
		  // 遍历 key 和 value 值(一)
		  for (let item of map.entries()) {
		    console.log(item[0], item[1]);
		  }
		  
		  // 遍历 key 和 value 值(二)
		  for (let [key, value] of data) {
		    console.log(key)
		  }
		  ```
- ## 7. Promise
	- ### 1. 基本示例
	  background-color:: blue
		- ```
		  // 例子 7-1
		  
		  // bad
		  request(url, function(err, res, body) {
		      if (err) handleError(err);
		      fs.writeFile('1.txt', body, function(err) {
		          request(url2, function(err, res, body) {
		              if (err) handleError(err)
		          })
		      })
		  });
		  
		  // good
		  request(url)
		  .then(function(result) {
		      return writeFileAsynv('1.txt', result)
		  })
		  .then(function(result) {
		      return request(url2)
		  })
		  .catch(function(e){
		      handleError(e)
		  });
		  ```
	- ### 2. finally
	  background-color:: blue
		- ```
		  // 例子 7-2
		  
		  fetch('file.json')
		  .then(data => data.json())
		  .catch(error => console.error(error))
		  .finally(() => console.log('finished'));
		  ```
- ## 8. Async
	- ### 1. 代码更加简洁
	  background-color:: blue
		- ```
		  // 例子 8-1
		  
		  // good
		  function fetch() {
		    return (
		      fetchData()
		      .then(() => {
		        return "done"
		      });
		    )
		  }
		  
		  // better
		  async function fetch() {
		    await fetchData()
		    return "done"
		  };
		  ```
	- ### 2. 错误处理
	  background-color:: blue
		- ```
		  // 例子 8-4
		  
		  // good
		  function fetch() {
		    try {
		      fetchData()
		        .then(result => {
		          const data = JSON.parse(result)
		        })
		        .catch((err) => {
		          console.log(err)
		        })
		    } catch (err) {
		      console.log(err)
		    }
		  }
		  
		  // better
		  async function fetch() {
		    try {
		      const data = JSON.parse(await fetchData())
		    } catch (err) {
		      console.log(err)
		    }
		  };
		  ```
	- ### 3. "async 地狱"
	  background-color:: blue
		- ```
		  // 例子 8-5
		  
		  // bad
		  (async () => {
		    const getList = await getList();
		    const getAnotherList = await getAnotherList();
		  })();
		  
		  // good
		  (async () => {
		    const listPromise = getList();
		    const anotherListPromise = getAnotherList();
		    await listPromise;
		    await anotherListPromise;
		  })();
		  
		  // good
		  (async () => {
		    Promise.all([getList(), getAnotherList()]).then(...);
		  })();
		  ```
- ## 9. Class
	- 构造函数尽可能使用 Class 的形式
	- ```
	  // 例子 9-1
	  
	  class Foo {
	    static bar () {
	      this.baz();
	    }
	    static baz () {
	      console.log('hello');
	    }
	    baz () {
	      console.log('world');
	    }
	  }
	  
	  Foo.bar(); // hello
	  ```
	- ```
	  // 例子 9-2
	  
	  class Shape {
	    constructor(width, height) {
	      this._width = width;
	      this._height = height;
	    }
	    get area() {
	      return this._width * this._height;
	    }
	  }
	  
	  const square = new Shape(10, 10);
	  console.log(square.area);    // 100
	  console.log(square._width);  // 10
	  ```
- ## 10.Decorator
	- ### 1. log
	  background-color:: blue
	- ### 2. autobind
	  background-color:: blue
	- ### 3. debounce
	  background-color:: blue
	- ### 4. React 与 Redux
	  background-color:: blue
	- 实现可以参考 [[ES6 系列之我们来聊聊装饰器]]
- ## 11. 函数
	- ### 1. 默认值
	  background-color:: blue
		- ```
		  // 例子 11-1
		  
		  // bad
		  function test(quantity) {
		    const q = quantity || 1;
		  }
		  
		  // good
		  function test(quantity = 1) {
		    ...
		  }
		  
		  ```
- ## 12. 拓展运算符
	- ### 1. arguments 转数组
	  background-color:: blue
		- ```
		  // 例子 12-1
		  
		  // bad
		  function sortNumbers() {
		    return Array.prototype.slice.call(arguments).sort();
		  }
		  
		  // good
		  const sortNumbers = (...numbers) => numbers.sort();
		  ```
	- ### 2. 调用参数
	  background-color:: blue
		- ```
		  // 例子 12-2
		  
		  // bad
		  Math.max.apply(null, [14, 3, 77])
		  
		  // good
		  Math.max(...[14, 3, 77])
		  // 等同于
		  Math.max(14, 3, 77);
		  ```
	- ### 3. 构建对象
	  background-color:: blue
		- 剔除部分属性，将剩下的属性构建一个新的对象
		- ```
		  // 例子 12-3
		  let [a, b, ...arr] = [1, 2, 3, 4, 5];
		  
		  const { a, b, ...others } = { a: 1, b: 2, c: 3, d: 4, e: 5 };
		  ```
		- 有条件的构建对象
		- ```
		  // 例子 12-4
		  
		  // bad
		  function pick(data) {
		    const { id, name, age} = data
		  
		    const res = { guid: id }
		  
		    if (name) {
		      res.name = name
		    }
		    else if (age) {
		      res.age = age
		    }
		  
		    return res
		  }
		  
		  // good
		  function pick({id, name, age}) {
		    return {
		      guid: id,
		      ...(name && {name}),
		      ...(age && {age})
		    }
		  }
		  ```
		- 合并对象
		- ```
		  // 例子 12-5
		  
		  let obj1 = { a: 1, b: 2,c: 3 }
		  let obj2 = { b: 4, c: 5, d: 6}
		  let merged = {...obj1, ...obj2};
		  ```
	- ### 4. React
	  background-color:: blue
		- ```
		  // 例子 12-6
		  
		  const parmas =  {value1: 1, value2: 2, value3: 3}
		  
		  <Test {...parmas} />
		  ```
- ## 13. 双冒号运算符
	- ```
	  // 例子 13-1
	  
	  foo::bar;
	  // 等同于
	  bar.bind(foo);
	  
	  foo::bar(...arguments);
	  // 等同于
	  bar.apply(foo, arguments);
	  ```
	- 如果双冒号左边为空，右边是一个对象的方法，则等于将该方法绑定在该对象上面。
	- ```
	  // 例子 13-2
	  
	  var method = obj::obj.foo;
	  // 等同于
	  var method = ::obj.foo;
	  
	  let log = ::console.log;
	  // 等同于
	  var log = console.log.bind(console);
	  ```
- ## 14. 解构赋值
	- ### 1. 对象的基本解构
	  background-color:: blue
		- ```
		  // 例子 14-1
		  
		  componentWillReceiveProps(newProps) {
		  	this.setState({
		  		active: newProps.active
		  	})
		  }
		  
		  componentWillReceiveProps({active}) {
		  	this.setState({active})
		  }
		  ```
	- ### 2. 对象深度解构
	  background-color:: blue
		- ```
		  // 例子 14-4
		  
		  // bad
		  function test(fruit) {
		    if (fruit && fruit.name)  {
		      console.log (fruit.name);
		    } else {
		      console.log('unknown');
		    }
		  }
		  
		  // good
		  function test({name} = {}) {
		    console.log (name || 'unknown');
		  }
		  ```
		- ```
		  // 例子 14-5
		  
		  let obj = {
		      a: {
		        b: {
		          c: 1
		        }
		      }
		  };
		  
		  const {a: {b: {c = ''} = ''} = ''} = obj;
		  ```
	- ### 3. 数组解构
	  background-color:: blue
		- ```
		  // 例子 14-6
		  
		  // bad
		  const splitLocale = locale.split("-");
		  const language = splitLocale[0];
		  const country = splitLocale[1];
		  
		  // good
		  const [language, country] = locale.split('-');
		  ```
	- ### 4. 变量重命名
	  background-color:: blue
		- ```
		  // 例子 14-8
		  
		  let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
		  console.log(baz); // "aaa"
		  ```
	- ### 5. 仅获取部分属性
	  background-color:: blue
		- ```
		  // 例子 14-9
		  
		  function test(input) {
		    return [left, right, top, bottom];
		  }
		  const [left, __, top] = test(input);
		  
		  function test(input) {
		    return { left, right, top, bottom };
		  }
		  const { left, right } = test(input);
		  ```
- ## 15. 增强的对象字面量
	- ```
	  // 例子 15-1
	  
	  // bad
	  const something = 'y'
	  const x = {
	    something: something
	  }
	  
	  // good
	  const something = 'y'
	  const x = {
	    something
	  };
	  ```
	- 动态属性
	- ```
	  // 例子 15-2
	  
	  const x = {
	    ['a' + '_' + 'b']: 'z'
	  }
	  
	  console.log(x.a_b); // z
	  ```
- ## 16. 数组的拓展方法
	- ### 1. keys
	  background-color:: blue
		- ```
		  // 例子 16-1
		  
		  var arr = ["a", , "c"];
		  
		  var sparseKeys = Object.keys(arr);
		  console.log(sparseKeys); // ['0', '2']
		  
		  var denseKeys = [...arr.keys()];
		  console.log(denseKeys);  // [0, 1, 2]
		  ```
	- ### 2. entries
	  background-color:: blue
	- ### 3. values
	  background-color:: blue
	- ### 4. includes
	  background-color:: blue
	- ### 5. find
	  background-color:: blue
	- ### 6. findIndex
	  background-color:: blue
- ## 17.   [optional-chaining](https://github.com/tc39/proposal-optional-chaining)
	- ```
	  // 例子 17-1
	  
	  const obj = {
	    foo: {
	      bar: {
	        baz: 42,
	      },
	    },
	  };
	  
	  const baz = obj?.foo?.bar?.baz; // 42
	  ```
- ## 18. logical-assignment-operators
	- ```
	  // 例子 18-1
	  
	  a ||= b;
	  
	  obj.a.b ||= c;
	  
	  a &&= b;
	  
	  obj.a.b &&= c;
	  ```
	- Babel 编译为：
	- ```
	  var _obj$a, _obj$a2;
	  
	  a || (a = b);
	  
	  (_obj$a = obj.a).b || (_obj$a.b = c);
	  
	  a && (a = b);
	  
	  (_obj$a2 = obj.a).b && (_obj$a2.b = c);
	  ```
	- 需要 [@babel/plugin-proposal-logical-assignment-operators](https://babeljs.io/docs/en/babel-plugin-proposal-logical-assignment-operators) 插件支持
- ## 19. nullish-coalescing-operator
	- ```
	  a ?? b
	  
	  // 相当于
	  
	  (a !== null && a !== void 0) ? a : b
	  ```
- ## 20. pipeline-operator
	- ```
	  const double = (n) => n * 2;
	  const increment = (n) => n + 1;
	  
	  // 没有用管道操作符
	  double(increment(double(5))); // 22
	  
	  // 用上管道操作符之后
	  5 |> double |> increment |> double; // 22
	  ```
- ## 参考
	- 1. [ES6 实践规范](https://juejin.im/post/5934ff6d2f301e005861422f)
	  2. [babel 7 简单升级指南](https://juejin.im/post/5b87cab1e51d4538ac05dc54)
	  3. [不得不知的 ES6 小技巧](https://mp.weixin.qq.com/s/NAbvosbJ4utOgFaM-6wO4Q?)
	  4. [深入解析 ES6：Symbol](http://bubkoo.com/2015/07/24/es6-in-depth-symbols/)
	  5. [什么时候你不能使用箭头函数？](https://zhuanlan.zhihu.com/p/26540168)
	  6. [一些使 JavaScript 更加简洁的小技巧](https://www.css88.com/archives/9868)
	  7. [几分钟内提升技能的 8 个 JavaScript 方法](https://www.css88.com/archives/9916)
	  8. [[译] 如何使用 JavaScript ES6 有条件地构造对象](https://juejin.im/post/5bb47db76fb9a05d071953ea)
	  9. [5 个技巧让你更好的编写 JavaScript(ES6) 中条件语句](https://www.css88.com/archives/9865)
	  10. [ES6 带来的重大特性 – JavaScript 完全手册（2018版）](https://www.css88.com/archives/9958)