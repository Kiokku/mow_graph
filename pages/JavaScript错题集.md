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
- collapsed:: true
  17. 输出是什么？#card #JavaScript
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
- 25. 输出是什么？#card #JavaScript
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
- 28. 输出是什么？#card #JavaScript
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
- 33. 输出是什么？#card #JavaScript
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
  ```
  setInterval(() => console.log('Hi'), 1000)
  ```
  A: 一个唯一的 id
  B: 该方法指定的毫秒数
  C: 传递的函数
  D: undefined
	- **答案: A**
	- setInterval 返回一个唯一的 id。此 id 可被用于 clearInterval 函数来取消定时。
- 44. 输出是什么？#card #JavaScript
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
- 53. 输出是什么？#card #JavaScript
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
- 56. 输出是什么？#card #JavaScript
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
- 58. 输出是什么？#card #JavaScript
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
- 61. 输出是什么？#card #JavaScript
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
- 66. 使用哪个构造函数可以成功继承**Dog**类？#card #JavaScript
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
- 67. 输出什么？#card #JavaScript
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
- 82. 输出什么？#card #JavaScript
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
- 98. 输出什么？
-
-