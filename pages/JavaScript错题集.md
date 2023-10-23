- {{cards [[JavaScript]]}}
- 8.输出是什么？#card #JavaScript
  collapsed:: true
  ```
  class Chameleon {
  static colorChange(newColor) {
    this.newColor = newColor
    return this.newColor
  }
  - constructor({ newColor = 'green' } = {}) {
    this.newColor = newColor
  }
  }
  - const freddie = new Chameleon({ newColor: 'purple' })
  freddie.colorChange('orange')
  ```
  A: orange
  B: purple
  C: green
  D: TypeError
	- **答案: D**
	- colorChange 是一个静态方法。静态方法被设计为只能被创建它们的构造器使用（也就是 Chameleon），并且不能传递给实例。因为 freddie 是一个实例，静态方法不能被实例使用，因此抛出了 TypeError 错误。
- [[$red]]==17. 输出是什么？==#card #JavaScript
  card-last-interval:: -1
  card-repeats:: 1
  card-ease-factor:: 2.5
  card-next-schedule:: 2023-10-20T16:00:00.000Z
  card-last-reviewed:: 2023-10-20T06:50:43.105Z
  card-last-score:: 1
  collapsed:: true
  ```
  function getPersonInfo(one, two, three) {
    console.log(one)
    console.log(two)
    console.log(three)
  }
  
  const person = 'Lydia'
  const age = 21
  
  getPersonInfo`${person} is ${age} years old`
  ```
  A: "Lydia" 21 ["", " is ", " years old"]
  B: ["", " is ", " years old"] "Lydia" 21
  C: "Lydia" ["", " is ", " years old"] 21
	- **答案: B**
	- 如果使用标记模板字面量，第一个参数的值总是包含字符串的数组。其余的参数获取的是传递的表达式的值！
- collapsed:: true
  25. 输出是什么？#card #JavaScript
  ```
  const obj = { a: 'one', b: 'two', a: 'three' }
  console.log(obj)
  ```
  A: { a: "one", b: "two" }
  B: { b: "two", a: "three" }
  C: { a: "three", b: "two" }
  D: SyntaxError
	- **答案: C**
	- 如果你有两个名称相同的键，则键会被替换掉。它仍然位于第一个键出现的位置，但是值是最后出现那个键的值。
- collapsed:: true
  28. 输出是什么？#card #JavaScript
  ```
  String.prototype.giveLydiaPizza = () => {
    return 'Just give Lydia pizza already!'
  }
  
  const name = 'Lydia'
  
  name.giveLydiaPizza()
  ```
  A: "Just give Lydia pizza already!"
  B: TypeError: not a function
  C: SyntaxError
  D: undefined
	- **答案: A**
	- String 是内置的构造函数，我们可以向它添加属性。我只是在它的原型中添加了一个方法。基本类型字符串被自动转换为字符串对象，由字符串原型函数生成。因此，所有 string(string 对象) 都可以访问该方法
- 29. 输出是什么？#card #JavaScript
  ```
  const a = {}
  const b = { key: 'b' }
  const c = { key: 'c' }
  
  a[b] = 123
  a[c] = 456
  
  console.log(a[b])
  ```
  A: 123
  B: 456
  C: undefined
  D: ReferenceError
	- **答案: B**
	- 对象的键被自动转换为字符串。我们试图将一个对象 b 设置为对象 a 的键，且相应的值为 123。
	- 然而，当字符串化一个对象时，它会变成 "[object Object]"。因此这里说的是，a["[object Object]"] = 123。然后，我们再一次做了同样的事情，c 是另外一个对象，这里也有隐式字符串化，于是，a["[object Object]"] = 456。
	- 然后，我们打印 a[b]，也就是 a["[object Object]"]。之前刚设置为 456，因此返回的是 456。
- [[$red]]==31. 当点击按钮时，event.target 是什么？== #card #JavaScript
  collapsed:: true
  ```
  <div onclick="console.log('first div')">
    <div onclick="console.log('second div')">
      <button onclick="console.log('button')">
        Click!
      </button>
    </div>
  </div>
  ```
  A: Outer div
  B: Inner div
  C: button
  D: 一个包含所有嵌套元素的数组。
	- **答案: C**
	- 导致事件的最深嵌套的元素是事件的 target。你可以通过 event.stopPropagation 来停止冒泡。
- collapsed:: true
  33. 输出是什么？#card #JavaScript
  ```
  const person = { name: 'Lydia' }
  
  function sayHi(age) {
    console.log(`${this.name} is ${age}`)
  }
  
  sayHi.call(person, 21)
  sayHi.bind(person, 21)
  ```
  A: undefined is 21  Lydia is 21
  B: function  function
  C: Lydia is 21  Lydia is 21
  D: Lydia is 21  function
	- **答案: D**
	- 使用这两种方法，我们都可以传递我们希望 this 关键字引用的对象。但是，.call 是立即执行的。
	- .bind 返回函数的副本，但带有绑定上下文！它不是立即执行的。
- 42. **setInterval** 方法的返回值是什么？#card #JavaScript
  collapsed:: true
  ```
  setInterval(() => console.log('Hi'), 1000)
  ```
  A: 一个唯一的 id
  B: 该方法指定的毫秒数
  C: 传递的函数
  D: undefined
	- **答案: A**
	- setInterval 返回一个唯一的 id。此 id 可被用于 clearInterval 函数来取消定时。
- collapsed:: true
  44. 输出是什么？#card #JavaScript
  ```
  function* generator(i) {
    yield i;
    yield i * 2;
  }
  
  const gen = generator(10);
  
  console.log(gen.next().value);
  console.log(gen.next().value);
  ```
  A: [0, 10], [10, 20]
  B: 20, 20
  C: 10, 20
  D: 0, 10 and 10, 20
	- **答案: C**
	- 一般的函数在执行之后是不能中途停下的。但是，生成器函数却可以中途 “停下”，之后可以再从停下的地方继续。当生成器遇到yield关键字的时候，会生成yield后面的值。注意，生成器在这种情况下不 **返回** (**return** ) 值，而是 **生成** (**yield**) 值。
	- 首先，我们用10作为参数i来初始化生成器函数。然后使用next()方法一步步执行生成器。第一次执行生成器的时候，i的值为10，遇到第一个yield关键字，它要生成i的值。此时，生成器 “暂停”，生成了10。
	- 然后，我们再执行next()方法。生成器会从刚才暂停的地方继续，这个时候i还是10。于是我们走到了第二个yield关键字处，这时候需要生成的值是i*2，i为10，那么此时生成的值便是20。所以这道题的最终结果是10,20。
- collapsed:: true
  53. 输出是什么？#card #JavaScript
  ```
  function Car() {
    this.make = "Lamborghini";
    return { make: "Maserati" };
  }
  
  const myCar = new Car();
  console.log(myCar.make);
  ```
  A: "Lamborghini"
  B: "Maserati"
  C: ReferenceError
  D: TypeError
	- **答案: B**
	- 返回属性的时候，属性的值等于 **返回的** 值，而不是构造函数中设定的值。我们返回了字符串 "Maserati"，所以 myCar.make等于"Maserati".
- [[$red]]==55. 输出是什么？==#card #JavaScript
  collapsed:: true
  ```
  class Dog {
    constructor(name) {
      this.name = name;
    }
  }
  
  Dog.prototype.bark = function() {
    console.log(`Woof I am ${this.name}`);
  };
  
  const pet = new Dog("Mara");
  
  pet.bark();
  
  delete Dog.prototype.bark;
  
  pet.bark();
  ```
  A: "Woof I am Mara", TypeError
  B: "Woof I am Mara","Woof I am Mara"
  C: "Woof I am Mara", undefined
  D: TypeError, TypeError
	- **答案: A**
	- 我们可以用delete关键字删除对象的属性，对原型也是适用的。删除了原型的属性后，该属性在原型链上就不可用了。在本例中，函数bark在执行了delete Dog.prototype.bark后不可用，然而后面的代码还在调用它。
	- 当我们尝试调用一个不存在的函数时TypeError异常会被抛出。在本例中就是 TypeError: pet.bark is not a function，因为pet.bark是undefined.
- collapsed:: true
  56. 输出是什么？#card #JavaScript
  ```
  const set = new Set([1, 1, 2, 3, 4]);
  
  console.log(set);
  ```
  A: [1, 1, 2, 3, 4]
  B: [1, 2, 3, 4]
  C: {1, 1, 2, 3, 4}
  D: {1, 2, 3, 4}
	- **答案: D**
	- Set对象是独一无二的值的集合：也就是说同一个值在其中仅出现一次。
	- 我们传入了数组[1, 1, 2, 3, 4]，他有一个重复值1.以为一个集合里不能有两个重复的值，其中一个就被移除了。所以结果是 {1, 2, 3, 4}.
- collapsed:: true
  58. 输出是什么？#card #JavaScript
  ```
  const name = "Lydia";
  age = 21;
  
  console.log(delete name);
  console.log(delete age);
  ```
  A: false, true
  B: "Lydia", 21
  C: true, true
  D: undefined, undefined
	- **答案: A**
	- delete操作符返回一个布尔值： true指删除成功，否则返回false. 但是通过 var, const 或 let 关键字声明的变量无法用 delete 操作符来删除。
	- name变量由const关键字声明，所以删除不成功：返回 false. 而我们设定age等于21时，我们实际上添加了一个名为age的属性给全局对象。对象中的属性是可以删除的，全局对象也是如此，所以delete age返回true.
- collapsed:: true
  61. 输出是什么？#card #JavaScript
  ```
  const person = { name: "Lydia" };
  
  Object.defineProperty(person, "age", { value: 21 });
  
  console.log(person);
  console.log(Object.keys(person));
  ```
  A: { name: "Lydia", age: 21 }, ["name", "age"]
  B: { name: "Lydia", age: 21 }, ["name"]
  C: { name: "Lydia"}, ["name", "age"]
  D: { name: "Lydia"}, ["age"]
	- **答案: B**
	- 通过defineProperty方法，我们可以给对象添加一个新属性，或者修改已经存在的属性。而我们使用defineProperty方法给对象添加了一个属性之后，属性默认为 **不可枚举 (not enumerable)**. Object.keys方法仅返回对象中 **可枚举 (enumerable)** 的属性，因此只剩下了"name".
	- 用defineProperty方法添加的属性默认不可变。你可以通过writable, configurable 和 enumerable属性来改变这一行为。这样，defineProperty方法可以让您更好地控制要添加到对象的属性。
- collapsed:: true
  66. 使用哪个构造函数可以成功继承**Dog**类？#card #JavaScript
  ```
  class Dog {
    constructor(name) {
      this.name = name;
    }
  };
  
  class Labrador extends Dog {
    // 1 
    constructor(name, size) {
      this.size = size;
    }
    // 2
    constructor(name, size) {
      super(name);
      this.size = size;
    }
    // 3
    constructor(size) {
      super(name);
      this.size = size;
    }
    // 4 
    constructor(name, size) {
      this.name = name;
      this.size = size;
    }
  
  };
  ```
  A: 1
  B: 2
  C: 3
  D: 4
	- **答案: B**
	- 在子类中，在调用super之前不能访问到this关键字。 如果这样做，它将抛出一个ReferenceError：1 和 4 将引发一个引用错误。
	- 使用super关键字，需要用给定的参数来调用父类的构造函数。 父类的构造函数接收name参数，因此我们需要将name传递给super。
	- Labrador类接收两个参数，name参数是由于它继承了Dog，size作为Labrador类的额外属性，它们都需要传递给Labrador的构造函数，因此使用构造函数 2 正确完成。
- collapsed:: true
  67. 输出什么？#card #JavaScript
  ```
  // index.js
  console.log('running index.js');
  import { sum } from './sum.js';
  console.log(sum(1, 2));
  
  // sum.js
  console.log('running sum.js');
  export const sum = (a, b) => a + b;
  ```
  A: running index.js, running sum.js, 3
  B: running sum.js, running index.js, 3
  C: running sum.js, 3, running index.js
  D: running index.js, undefined, running sum.js
	- **答案: B**
	- import命令是编译阶段执行的，在代码运行之前。因此这意味着被导入的模块会先运行，而导入模块的文件会后执行。
	- 这是 CommonJS 中require（）和import之间的区别。使用require()，您可以在运行代码时根据需要加载依赖项。 如果我们使用require而不是import，running index.js，running sum.js，3会被依次打印。
- collapsed:: true
  82. 输出什么？#card #JavaScript
  ```
  var status = "😎"
  
  setTimeout(() => {
    const status = "😍"
  
    const data = {
      status: "🥑",
      getStatus() {
        return this.status
      }
    }
  
    console.log(data.getStatus())
    console.log(data.getStatus.call(this))
  }, 0)
  ```
  A: "🥑" and "😍"
  B: "🥑" and "😎"
  C: "😍" and "😎"
  D: "😎" and "😎"
	- **答案: B**
	- this关键字的指向取决于使用它的位置。 在**函数**中，比如getStatus，this指向的是调用它的对象，上述例子中data对象调用了getStatus，因此this指向的就是data对象。 当我们打印this.status时，data对象的status属性被打印，即"🥑"。
	- 使用call方法，可以更改this指向的对象。data.getStatus.call(this)是将this的指向由data对象更改为全局对象。在全局对象上，有一个名为status的变量，其值为”😎“。 因此打印this.status时，会打印“😎”。
- 92. 输出什么？#card #JavaScript
  ```
  function giveLydiaPizza() {
    return "Here is pizza!"
  }
  
  const giveLydiaChocolate = () => "Here's chocolate... now go hit the gym already."
  
  console.log(giveLydiaPizza.prototype)
  console.log(giveLydiaChocolate.prototype)
  ```
  A: { constructor: ...} { constructor: ...}
  B: {} { constructor: ...}
  C: { constructor: ...} {}
  D: { constructor: ...} undefined
	- **答案: D**
	- 常规函数，例如giveLydiaPizza函数，有一个prototype属性，它是一个带有constructor属性的对象（原型对象）。 然而，箭头函数，例如giveLydiaChocolate函数，没有这个prototype属性。 尝试使用giveLydiaChocolate.prototype访问prototype属性时会返回undefined。
- 95. 输出什么？#card #JavaScript
  ```
  function nums(a, b) {
    if
    (a > b)
    console.log('a is bigger')
    else 
    console.log('b is bigger')
    return 
    a + b
  }
  
  console.log(nums(4, 2))
  console.log(nums(1, 2))
  ```
  A: a is bigger, 6 and b is bigger, 3
  B: a is bigger, undefined and b is bigger, undefined
  C: undefined and undefined
  D: SyntaxError
	- **答案: B**
	- 在 JavaScript 中，我们不必显式地编写分号 (;)，但是 JavaScript 引擎仍然在语句之后自动添加分号。这称为**自动分号插入**。例如，一个语句可以是变量，或者像throw、return、break这样的关键字。
	- 在这里，我们在新的一行上写了一个return语句和另一个值a + b 。然而，由于它是一个新行，引擎并不知道它实际上是我们想要返回的值。相反，它会在return后面自动添加分号。你可以这样看：
	- ```
	  return;
	    a + b
	  ```
	- 这意味着永远不会到达a + b，因为函数在return关键字之后停止运行。如果没有返回值，就像这里，函数返回undefined。注意，在if/else语句之后没有自动插入！
- [[$red]]==97. 输出什么？==#card #JavaScript
  collapsed:: true
  ```
  const info = {
    [Symbol('a')]: 'b'
  }
  
  console.log(info)
  console.log(Object.keys(info))
  ```
  A: {Symbol('a'): 'b'} and ["{Symbol('a')"]
  B: {} and []
  C: { a: "b" } and ["a"]
  D: {Symbol('a'): 'b'} and []
	- **答案: D**
	- Symbol类型是不可枚举的。Object.keys方法返回对象上的所有可枚举的键属性。Symbol类型是不可见的，并返回一个空数组。 记录整个对象时，所有属性都是可见的，甚至是不可枚举的属性。
	- 这是Symbol的众多特性之一：除了表示完全唯一的值（防止对象意外名称冲突，例如当使用 2 个想要向同一对象添加属性的库时），您还可以隐藏这种方式对象的属性（尽管不完全。你仍然可以使用Object.getOwnPropertySymbols()方法访问 Symbol。
- collapsed:: true
  98. 输出什么？#card #JavaScript
  ```
  const getList = ([x, ...y]) => [x, y]
  const getUser = user => { name: user.name, age: user.age }
  
  const list = [1, 2, 3, 4]
  const user = { name: "Lydia", age: 21 }
  
  console.log(getList(list))
  console.log(getUser(user))
  ```
  A: [1, [2, 3, 4]] and SyntaxError
  B: [1, [2, 3, 4]] and { name: "Lydia", age: 21 }
  C: [1, 2, 3, 4] and { name: "Lydia", age: 21 }
  D: Error and { name: "Lydia", age: 21 }
	- **答案: A**
	- getList函数接收一个数组作为其参数。 在getList函数的括号之间，我们立即解构这个数组。 您可以将其视为：
	- [x, ...y] = [1, 2, 3, 4]
	- 使用剩余的参数... y，我们将所有剩余参数放在一个数组中。 在这种情况下，其余的参数是2，3和4。 y的值是一个数组，包含所有其余参数。 在这种情况下，x的值等于1，所以当我们打印[x，y]时，会打印[1，[2,3,4]]。
	- getUser函数接收一个对象。对于箭头函数，如果只返回一个值，我们不必编写花括号。但是，如果您想从一个箭头函数返回一个对象，您必须将它写在圆括号之间，否则两个花括号之间的所有内容都将被解释为一个块语句！在这种情况下，花括号之间的代码不是有效的 JavaScript 代码，因此会抛出 SyntaxError。
	- 以下函数将返回一个对象：
	- const getUser = user => ({ name: user.name, age: user.age })
- collapsed:: true
  103. 输出什么？#card #JavaScript
  ```
  const set = new Set()
  
  set.add(1)
  set.add("Lydia")
  set.add({ name: "Lydia" })
  
  for (let item of set) {
    console.log(item + 2)
  }
  ```
  A: 3, NaN, NaN
  B: 3, 7, NaN
  C: 3, Lydia2, [Object object]2
  D: "12", Lydia2, [Object object]2
	- **答案: C**
	- “+” 运算符不仅用于添加数值，还可以使用它来连接字符串。 每当 JavaScript 引擎发现一个或多个值不是数字时，就会将数字强制为字符串。
	- 第一个是数字 1。 1 + 2 返回数字 3。
	- 但是，第二个是字符串 “Lydia”。 “Lydia” 是一个字符串，2 是一个数字：2 被强制转换为字符串。 “Lydia” 和 “2” 被连接起来，产生字符串 “Lydia2”。
	- {name：“ Lydia”}是一个对象。 数字和对象都不是字符串，因此将二者都字符串化。 每当我们对常规对象进行字符串化时，它就会变成[Object object]。 与 “2” 串联的 “ [Object object]” 成为 “[Object object]2”。
- collapsed:: true
  108. 哪些方法修改了原数组？#card #JavaScript
  ```
  const emojis = ['✨', '🥑', '😍']
  
  emojis.map(x => x + '✨')
  emojis.filter(x => x !== '🥑')
  emojis.find(x => x !== '🥑')
  emojis.reduce((acc, cur) => acc + '✨')
  emojis.slice(1, 2, '✨') 
  emojis.splice(1, 2, '✨')
  ```
  A: All of them
  B: map reduce slice splice
  C: map slice splice
  D: splice
	- **答案: D**
	- 使用splice方法，我们通过删除，替换或添加元素来修改原始数组。 在这种情况下，我们从索引 1 中删除了 2 个元素（我们删除了'🥑'和'😍'），同时添加了✨emoji 表情。
	- map，filter和slice返回一个新数组，find返回一个元素，而reduce返回一个减小的值。
- collapsed:: true
  110. 这个函数干了什么？#card #JavaScript
  ```
  JSON.parse()
  ```
  A: Parses JSON to a JavaScript value
  B: Parses a JavaScript object to JSON
  C: Parses any JavaScript value to JSON
  D: Parses JSON to a JavaScript object only
	- **答案: A**
	- 使用JSON.parse()方法，我们可以将 JSON 字符串解析为 JavaScript 值。
	- ```
	  // 将数字字符串化为有效的 JSON，然后将 JSON 字符串解析为 JavaScript 值：
	  const jsonNumber = JSON.stringify(4) // '4'
	  JSON.parse(jsonNumber) // 4
	  
	  // 将数组值字符串化为有效的 JSON，然后将 JSON 字符串解析为 JavaScript 值：
	  const jsonArray = JSON.stringify([1, 2, 3]) // '[1, 2, 3]'
	  JSON.parse(jsonArray) // [1, 2, 3]
	  
	  // 将对象字符串化为有效的 JSON，然后将 JSON 字符串解析为 JavaScript 值：
	  const jsonArray = JSON.stringify({ name: "Lydia" }) // '{"name":"Lydia"}'
	  JSON.parse(jsonArray) // { name: 'Lydia' }
	  ```
- [[$red]]==111. 输出什么？==#card #JavaScript
  collapsed:: true
  ```
  let name = 'Lydia'
  
  function getName() {
    console.log(name)
    let name = 'Sarah'
  }
  
  getName()
  ```
  A: Lydia
  B: Sarah
  C: undefined
  D: ReferenceError
	- **答案: D**
	- 每个函数都有其自己的执行上下文。 getName函数首先在其自身的上下文（范围）内查找，以查看其是否包含我们尝试访问的变量name。 上述情况，getName函数包含其自己的name变量：我们用let关键字和Sarah的值声明变量name。
	- 带有let关键字（和const）的变量被提升，但是与var不同，它不会被*** 初始化***。 在我们声明（初始化）它们之前，无法访问它们。 这称为 “暂时性死区”。 当我们尝试在声明变量之前访问变量时，JavaScript 会抛出ReferenceError: Cannot access 'name' before initialization。
	- 如果我们不在getName函数中声明name变量，则 javascript 引擎会查看原型链。会找到其外部作用域有一个名为name的变量，其值为Lydia。 在这种情况下，它将打印Lydia：
	- ```
	  let name = 'Lydia'
	  
	  function getName() {
	    console.log(name)
	  }
	  
	  getName() // Lydia
	  ```
- [[$red]]==112. 输出什么？==#card #JavaScript
  collapsed:: true
  ```
  function* generatorOne() {
    yield ['a', 'b', 'c'];
  }
  
  function* generatorTwo() {
    yield* ['a', 'b', 'c'];
  }
  
  const one = generatorOne()
  const two = generatorTwo()
  
  console.log(one.next().value)
  console.log(two.next().value)
  ```
  A: a and a
  B: a and undefined
  C: ['a', 'b', 'c'] and a
  D: a and ['a', 'b', 'c']
	- **答案: C**
	- 通过 yield 关键字，我们在 Generator 函数里执行yield表达式。通过 yield* 关键字，我们可以在一个Generator 函数里面执行（yield表达式）另一个 Generator 函数，或可遍历的对象 (如数组).
	- 在函数 generatorOne 中，我们通过 yield 关键字 yield 了一个完整的数组 ['a', 'b', 'c']。函数one通过next方法返回的对象的value 属性的值 (one.next().value) 等价于数组 ['a', 'b', 'c'].
	- ```
	  console.log(one.next().value) // ['a', 'b', 'c']
	  console.log(one.next().value) // undefined
	  ```
	- 在函数 generatorTwo 中，我们使用 yield* 关键字。就相当于函数two第一个yield的值，等价于在迭代器中第一个 yield 的值。数组['a', 'b', 'c']就是这个迭代器。第一个 yield 的值就是 a，所以我们第一次调用 two.next().value时，就返回a。
	- ```
	  console.log(two.next().value) // 'a'
	  console.log(two.next().value) // 'b'
	  console.log(two.next().value) // 'c'
	  console.log(two.next().value) // undefined
	  ```
- collapsed:: true
  114. 将会发生什么？#card #JavaScript
  ```
  let config = {
    alert: setInterval(() => {
      console.log('Alert!')
    }, 1000)
  }
  
  config = null
  ```
  A: setInterval 的回调不会被调用
  B: setInterval 的回调被调用一次
  C: setInterval 的回调仍然会被每秒钟调用
  D: 我们从没调用过 config.alert(), config 为 null
	- **答案: C**
	- 一般情况下当我们将对象赋值为 null，那些对象会被进行 **垃圾回收（garbage collected）** 因为已经没有对这些对象的引用了。然而，setInterval的参数是一个箭头函数（所以上下文绑定到对象 config 了），回调函数仍然保留着对 config的引用。只要存在引用，对象就不会被垃圾回收。因为没有被垃圾回收，setInterval 的回调每 1000ms (1s) 会被调用一次。
- 121. 输出什么？#card #JavaScript
  ```
  const config = {
      languages: [],
      set language(lang) {
          return this.languages.push(lang);
      }
  };
  
  console.log(config.language);
  ```
  A: function language(lang) { this.languages.push(lang }
  B: 0
  C: []
  D: undefined
	- **答案: D**
	- 方法 language 是一个 setter。Setters 并不保存一个实际值，它们的使命在于 **修改** 属性。当调用方法 setter， 返回 undefined。
- [[$red]]==124. 输出什么？==#card #JavaScript
  ```
  async function* range(start, end) {
      for (let i = start; i <= end; i++) {
          yield Promise.resolve(i);
      }
  }
  
  (async () => {
      const gen = range(1, 3);
      for await (const item of gen) {
          console.log(item);
      }
  })();
  ```
  A: Promise {1} Promise {2} Promise {3}
  B: Promise {} Promise {} Promise {}
  C: 1 2 3
  D: undefined undefined undefined
	- **答案: C**
	- 我们给 函数 range 传递： Promise{1}, Promise{2}, Promise{3}，Generator 函数 range 返回一个全是 async object promise 数组。我们将 async object 赋值给变量 gen，之后我们使用for await ... of 进行循环遍历。我们将返回的 Promise 实例赋值给 item： 第一个返回 Promise{1}， 第二个返回 Promise{2}，之后是 Promise{3}。因为我们正 **awaiting** item 的值，resolved 状态的 promsie，promise 数组的 resolved **值** 以此为： 1，2，3.
- 128. 输出什么？#card #JavaScript
  ```
  const name = "Lydia Hallie";
  const age = 21;
  
  console.log(Number.isNaN(name));
  console.log(Number.isNaN(age));
  
  console.log(isNaN(name));
  console.log(isNaN(age));
  ```
  A: true false true false
  B: true false false false
  C: false false true false
  D: false true false true
	- **答案: C**
	- 通过方法 Number.isNaN，你可以检测你传递的值是否为 **数字值** 并且是否等价于 NaN。name 不是一个数字值，因此 Number.isNaN(name) 返回 false。age 是一个数字值，但它不等价于 NaN，因此 Number.isNaN(age) 返回 false.
	- 通过方法 isNaN， 你可以检测你传递的值是否是一个 number（is not a number）。name 不是一个 number，因此 isNaN(name) 返回 true. age 是一个 number 因此 isNaN(age) 返回 false.
- [[$red]]==133. 输出什么？==#card #JavaScript
  ```
  const myPromise = Promise.resolve(Promise.resolve("Promise!"));
  
  function funcOne() {
      myPromise.then(res => res).then(res => console.log(res));
      setTimeout(() => console.log("Timeout!"), 0);
      console.log("Last line!");
  }
  
  async function funcTwo() {
      const res = await myPromise;
      console.log(await res);
      setTimeout(() => console.log("Timeout!"), 0);
      console.log("Last line!");
  }
  
  funcOne();
  funcTwo();
  ```
  A: Promise! Last line! Promise! Last line! Last line! Promise!
  B: Last line! Timeout! Promise! Last line! Timeout! Promise!
  C: Promise! Last line! Last line! Promise! Timeout! Timeout!
  D: Last line! Promise! Promise! Last line! Timeout! Timeout!
	- **答案: D**
	- 首先，我们调用 funcOne。在函数 funcOne 的第一行，我们调用myPromise promise **异步操作**。当 JS 引擎在忙于执行 promise，它继续执行函数 funcOne。下一行 **异步操作** setTimeout，其回调函数被 Web API 调用。 (详情请参考我关于 event loop 的文章.)
	- promise 和 timeout 都是异步操作，函数继续执行当 JS 引擎忙于执行 promise 和 处理 setTimeout 的回调。相当于 Last line! 首先被输出， 因为它不是异步操作。执行完 funcOne 的最后一行，promise 状态转变为 resolved，Promise! 被打印。然而，因为我们调用了 funcTwo()，调用栈不为空，setTimeout 的回调仍不能入栈。
	- 我们现在处于 funcTwo，先 **awaiting** myPromise。通过 await 关键字， 我们暂停了函数的执行直到 promise 状态变为 resolved (或 rejected)。然后，我们输出 res 的 awaited 值（因为 promise 本身返回一个 promise）。 接着输出 Promise!。
	- 下一行就是 **异步操作** setTimeout，其回调函数被 Web API 调用。
	- 我们执行到函数 funcTwo 的最后一行，输出 Last line!。现在，因为 funcTwo 出栈，调用栈为空。在事件队列中等待的回调函数（() => console.log("Timeout!") from funcOne, and () => console.log("Timeout!") from funcTwo）以此入栈。第一个回调输出 Timeout!，并出栈。然后，第二个回调输出 Timeout!，并出栈。得到结果 Last line! Promise! Promise! Last line! Timeout! Timeout!
- 135. 输出什么？#card #JavaScript
  ```
  const handler = {
      set: () => console.log("Added a new property!"),
      get: () => console.log("Accessed a property!")
  };
  
  const person = new Proxy({}, handler);
  
  person.name = "Lydia";
  person.name;
  ```
  A: Added a new property!
  B: Accessed a property!
  C: Added a new property! Accessed a property!
  D: 没有任何输出
	- **答案: C**
	- 使用 Proxy 对象，我们可以给一个对象添加自定义行为。在这个 case，我们传递一个包含以下属性的对象 handler : set and get。每当我们 **设置** 属性值时 set 被调用，每当我们 **获取** 时 get 被调用。
	- 第一个参数是一个空对象 {}，作为 person 的值。对于这个对象，自定义行为被定义在对象 handler。如果我们向对象 person 添加属性，set 将被调用。如果我们获取 person 的属性，get 将被调用。
	- 首先，我们向 proxy 对象 (person.name = "Lydia") 添加一个属性 name。set 被调用并输出 "Added a new property!"。
	- 然后，我们获取 proxy 对象的一个属性，对象 handler 的属性 get 被调用。输出 "Accessed a property!"。
- 139. 输出什么？#card #JavaScript
  ```
  class Counter {
    #number = 10
  
    increment() {
      this.#number++
    }
  
    getNum() {
      return this.#number
    }
  }
  
  const counter = new Counter()
  counter.increment()
  
  console.log(counter.#number)
  ```
  A: 10
  B: 11
  C: undefined
  D: SyntaxError
	- **答案: D**
	- 在 ES2020 中，通过 `#` 我们可以给 class 添加私有变量。在 class 的外部我们无法获取该值。当我们尝试输出 `counter.#number`，语法错误被抛出：我们无法在 `class Counter` 外部获取它！
- 142. 输出什么？#card #JavaScript
  ```
  class Bird {
      constructor() {
          console.log("I'm a bird. 🦢");
      }
  }
  
  class Flamingo extends Bird {
      constructor() {
          console.log("I'm pink. 🌸");
          super();
      }
  }
  
  const pet = new Flamingo();
  ```
  A: I'm pink. 🌸
  B: I'm pink. 🌸 I'm a bird. 🦢
  C: I'm a bird. 🦢 I'm pink. 🌸
  D: Nothing, we didn't call any method
	- **答案: B**
	- 我们创建了类 Flamingo 的实例 pet。当我们实例化这个实例，Flamingo 中的 constructor 被调用。首相，输出 "I'm pink. 🌸"，之后我们调用super()。super() 调用父类的构造函数，Bird。Bird 的构造函数被调用，并输出 "I'm a bird. 🦢"。
- [[$red]]==144. 我们需要向对象 person 添加什么，以致执行 [...person] 时获得形如 ["Lydia Hallie", 21] 的输出？==#card #JavaScript
  ```
  const person = {
    name: "Lydia Hallie",
    age: 21
  }
  
  [...person] // ["Lydia Hallie", 21]
  ```
  A: 不需要，对象默认就是可迭代的
  B: *[Symbol.iterator]() { for (let x in this) yield* this[x] }
  C: *[Symbol.iterator]() { yield* Object.values(this) }
  D: *[Symbol.iterator]() { for (let x in this) yield this }
	- **答案: C**
	- 对象默认并不是可迭代的。如果迭代规则被定义，则一个对象是可迭代的（An iterable is an iterable if the iterator protocol is present）。我们可以通过添加迭代器 symbol [Symbol.iterator] 来定义迭代规则，其返回一个 generator 对象，比如说构建一个 generator 函数 *[Symbol.iterator]() {}。如果我们想要返回数组 ["Lydia Hallie", 21]: yield* Object.values(this)，这个 generator 函数一定要 yield 对象 person 的Object.values。
- collapsed:: true
  150. 输出什么？#card #JavaScript
  ```
  const animals = {};
  let dog = { emoji: '🐶' }
  let cat = { emoji: '🐈' }
  
  animals[dog] = { ...dog, name: "Mara" }
  animals[cat] = { ...cat, name: "Sara" }
  
  console.log(animals[dog])
  ```
  A: { emoji: "🐶", name: "Mara" }
  B: { emoji: "🐈", name: "Sara" }
  C: undefined
  D: ReferenceError
	- **答案: B**
	- **对象的键会被转换为字符串。**
	- 因为 dog 的值是一个对象， animals[dog] 实际上意味着我们创建了一个叫做 "object Object" 的属性来代表新的对象。 animals["object Object"] 现在等于 { emoji: "🐶", name: "Mara"}。
	- cat 也是一个对象，animals[cat] 实际上意味着我们在用新的 cat 的属性覆盖 animals[``"``object Object``"``] 的值。
	- 打印 animals[dog]，实际上是animals["object Object"]，这是因为转化dog对象为一个字符串结果 "object Object" ，所以返回 { emoji: "🐈", name: "Sara" }。
- [[$red]]==关于块级作用域，以下代码输出多少？== #card #JavaScript
  collapsed:: true
  ```
  for (var i = 0; i < 5; i++) {
    setTimeout(() => console.log(i), 1000);
  }
  // 5 5 5 5 5
  
  for (let i = 0; i < 5; i++) {
    setTimeout(() => console.log(i), 1000 * i);
  }
  // 0 1 2 3 4
  
  for (var i = 0; i < 5; i++) {
    setTimeout(console.log, 1000 * i, i);
  }
  // 0 1 2 3 4
  ```
  为什么 setTimeout 格式不同,输出的结果也不同？例如：当 for 循环采用 var 声明变量，但 `setTimeout(console.log,1000*i,i)`与 `setTimeout(()=>console.log(i),0)`输出的结果不一样。
	- var 声明的变量是在函数作用域或者全局作用域的，在第一种方式中，由于 setTimeout 是异步执行，且它是从闭包中获取 i 变量，由于 i 是在函数/全局作用域中声明的，所以 5 次循环中 i 不断被赋值，最后 i 的值为 5，执行的结果为连续的 5 个 5。
	  logseq.order-list-type:: number
	- 在第二种方式中，通过给 setTimeout 的回调函数传参的方式，保存了每次循环中 i 的值，因此执行结果符合预期
	  logseq.order-list-type:: number
	- let 声明的变量是在块级作用域(花括号)中的，因此可以认为每次执行循环语句块中的 i 变量是互相独立的，所以执行结果也符合预期
	  logseq.order-list-type:: number
- [[$red]]==关于 Promise，判断以下代码的输出== #card #JavaScript
  collapsed:: true
  card-last-interval:: 4
  card-repeats:: 1
  card-ease-factor:: 2.36
  card-next-schedule:: 2023-10-24T06:50:01.468Z
  card-last-reviewed:: 2023-10-20T06:50:01.469Z
  card-last-score:: 3
  ```
  Promise.resolve()
    .then(() => {
      console.log(0);
      return Promise.resolve(4);
    })
    .then((res) => {
      console.log(res);
    });
   
  Promise.resolve()
    .then(() => {
      console.log(1);
    })
    .then(() => {
      console.log(2);
    })
    .then(() => {
      console.log(3);
    })
    .then(() => {
      console.log(5);
    })
    .then(() => {
      console.log(6);
    });
  ```
	- [[#green]]==答案：0 1 2 3 4 5 6==
	- [[#blue]]==解答：==
		- 顺序执行 0 和 1 没什么问题；
		- 问题变成了为什么 return Promise 为什么[[#green]]==产生了 2 次微任务==，导致4在2和3后打印？
			- 按照 PromiseA+的规范，此处应该是 2.3.2 标准：`If x is a promise` ,根据 A+准则的源码是以这么处理的：
			  logseq.order-list-type:: number
				- 遇到.then 就创建一 pending 状态的 Promise 保存起来。
				  logseq.order-list-type:: number
				- 如果是一个普通的 number 类型，则直接用 Promise.resolve(number)即可。 如果是 promise 类型的话，需要做一个状态同步操作，代码如下： 其中 x 是接收的已经 resolved 的 Promise，即(Promise.resolve(4))，而 this 指向我们刚创建的 pending 状态的 Promise
				  logseq.order-list-type:: number
				- ```
				  resolveWithPromise(x) {
				      x.then(
				        result => {
				          this.resolve(result); // promise将结果给resolve
				        },
				        reason => {
				          this.reject(reason);
				        }
				      );
				  ```
				- 也就是说，这 1 个 `micro task` 的作用就是**同步状态**。
			- v8 和 PromiseA+规范的差异：
			  logseq.order-list-type:: number
				- 与 promise/A+规范的不同之处在于，v8 并没有对`x is a promise` 的情况做处理，而是只有对`x is an object`的处理。所以多了一步 micro task：作用就是将 `resolveWithPromise => resolveWithThenableObject`
	- [[#blue]]==**总结：**如果 resolve()的括号内的结果是一个 promise 的话，会多执行两个`micro task`==
	- [在浏览器模拟事件循环](http://www.jsv9000.app/?code=UHJvbWlzZS5yZXNvbHZlKCkKICAudGhlbigoKSA9PiB7CiAgICBjb25zb2xlLmxvZygwKTsKICAgIHJldHVybiBQcm9taXNlLnJlc29sdmUoNCk7CiAgfSkKICAudGhlbigocmVzKSA9PiB7CiAgICBjb25zb2xlLmxvZyhyZXMpOwogIH0pOwogClByb21pc2UucmVzb2x2ZSgpCiAgLnRoZW4oKCkgPT4gewogICAgY29uc29sZS5sb2coMSk7CiAgfSkKICAudGhlbigoKSA9PiB7CiAgICBjb25zb2xlLmxvZygyKTsKICB9KQogIC50aGVuKCgpID0%2BIHsKICAgIGNvbnNvbGUubG9nKDMpOwogIH0pCiAgLnRoZW4oKCkgPT4gewogICAgY29uc29sZS5sb2coNSk7CiAgfSkKICAudGhlbigoKSA9PiB7CiAgICBjb25zb2xlLmxvZyg2KTsKICB9KTs%3D)