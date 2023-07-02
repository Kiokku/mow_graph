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