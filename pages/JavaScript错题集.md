- {{cards [[JavaScript]]}}
- 8.输出是什么？#card #JavaScript
	- ```
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
	- A: orange
	  B: purple
	  C: green
	  D: TypeError
-
-