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
  ```
-
-