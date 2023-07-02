- {{cards [[JavaScript]]}}
- 8.输出是什么？#card #JavaScript
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
- 17. 输出是什么？#card #JavaScript
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
-
-