- > https://github.com/mqyqingfeng/Blog/issues/109
-
- ## Decorator
	- 装饰器主要用于:
		- 1. 装饰类
		- 2. 装饰方法或属性
	- ### 装饰类
	  background-color:: pink
		- ```
		  @annotation
		  class MyClass { }
		  
		  function annotation(target) {
		     target.annotated = true;
		  }
		  ```
	- ### 装饰方法或属性
	  background-color:: pink
		- ```
		  class MyClass {
		    @readonly
		    method() { }
		  }
		  
		  function readonly(target, name, descriptor) {
		    descriptor.writable = false;
		    return descriptor;
		  }
		  ```
- ## Babel
	- ### 安装编译
	  background-color:: blue
		- [[#green]]==安装==：
			- `npm init`
			- `npm install --save-dev @babel/core @babel/cli`
			- `npm install --save-dev @babel/plugin-proposal-decorators @babel/plugin-proposal-class-properties`
		- [[#green]]==新建 .babelrc 文件：==
			- ```
			  {
			    "plugins": [
			      ["@babel/plugin-proposal-decorators", { "legacy": true }],
			      ["@babel/plugin-proposal-class-properties", {"loose": true}]
			    ]
			  }
			  ```
		- [[#green]]==编译指定的文件：==
			- `babel decorator.js --out-file decorator-compiled.js`
	- ### 装饰类的编译
	  background-color:: blue
		- 编译前：
		- ```
		  @annotation
		  class MyClass { }
		  
		  function annotation(target) {
		     target.annotated = true;
		  }
		  ```
		- 编译后：
		- ```
		  var _class;
		  
		  let MyClass = annotation(_class = class MyClass {}) || _class;
		  
		  function annotation(target) {
		    target.annotated = true;
		  }
		  ```
		- 我们可以看到对于类的装饰，其**原理**就是：
		- ```
		  @decorator
		  class A {}
		  
		  // 等同于
		  
		  class A {}
		  A = decorator(A) || A;
		  ```
	- ### 装饰方法的编译
	  background-color:: blue
		- 编译前：
		- ```
		  class MyClass {
		    @unenumerable
		    @readonly
		    method() { }
		  }
		  
		  function readonly(target, name, descriptor) {
		    descriptor.writable = false;
		    return descriptor;
		  }
		  
		  function unenumerable(target, name, descriptor) {
		    descriptor.enumerable = false;
		    return descriptor;
		  }
		  ```
		- 编译后：
		- ```
		  var _class;
		  
		  function _applyDecoratedDescriptor(target, property, decorators, descriptor, context ) {
		  	/**
		  	 * 第一部分
		  	 * 拷贝属性
		  	 */
		  	var desc = {};
		  	Object["ke" + "ys"](descriptor).forEach(function(key) {
		  		desc[key] = descriptor[key];
		  	});
		  	desc.enumerable = !!desc.enumerable;
		  	desc.configurable = !!desc.configurable;
		  
		  	if ("value" in desc || desc.initializer) {
		  		desc.writable = true;
		  	}
		  
		  	/**
		  	 * 第二部分
		  	 * 应用多个 decorators
		  	 */
		  	desc = decorators
		  		.slice()
		  		.reverse()
		  		.reduce(function(desc, decorator) {
		  			return decorator(target, property, desc) || desc;
		  		}, desc);
		  
		  	/**
		  	 * 第三部分
		  	 * 设置要 decorators 的属性
		  	 */
		  	if (context && desc.initializer !== void 0) {
		  		desc.value = desc.initializer ? desc.initializer.call(context) : void 0;
		  		desc.initializer = undefined;
		  	}
		  
		  	if (desc.initializer === void 0) {
		  		Object["define" + "Property"](target, property, desc);
		  		desc = null;
		  	}
		  
		  	return desc;
		  }
		  
		  let MyClass = ((_class = class MyClass {
		  	method() {}
		  }),
		  _applyDecoratedDescriptor(
		  	_class.prototype,
		  	"method",
		  	[readonly],
		  	Object.getOwnPropertyDescriptor(_class.prototype, "method"),
		  	_class.prototype
		  ),
		  _class);
		  
		  function readonly(target, name, descriptor) {
		  	descriptor.writable = false;
		  	return descriptor;
		  }
		  ```
- ## 应用
	- ### 1.log
	  background-color:: yellow
		- 为一个方法添加 log 函数，检查输入的参数：
		- ```
		  class Math {
		    @log
		    add(a, b) {
		      return a + b;
		    }
		  }
		  
		  function log(target, name, descriptor) {
		    var oldValue = descriptor.value;
		  
		    descriptor.value = function(...args) {
		      console.log(`Calling ${name} with`, args);
		      return oldValue.apply(this, args);
		    };
		  
		    return descriptor;
		  }
		  
		  const math = new Math();
		  
		  // Calling add with [2, 4]
		  math.add(2, 4);
		  ```
	- ### 2.autobind
	  background-color:: yellow
		- ```
		  class Person {
		    @autobind
		    getPerson() {
		    	return this;
		    }
		  }
		  
		  let person = new Person();
		  let { getPerson } = person;
		  
		  getPerson() === person;
		  // true
		  ```
	- ### 3.debounce
	  background-color:: yellow
		- 有的时候，我们需要对执行的方法进行防抖处理:
		- ```
		  class Toggle extends React.Component {
		  
		    @debounce(500, true)
		    handleClick() {
		      console.log('toggle')
		    }
		  
		    render() {
		      return (
		        <button onClick={this.handleClick}>
		          button
		        </button>
		      );
		    }
		  }
		  ```
		- 我们来实现一下：
		- ```
		  function _debounce(func, wait, immediate) {
		  
		      var timeout;
		  
		      return function () {
		          var context = this;
		          var args = arguments;
		  
		          if (timeout) clearTimeout(timeout);
		          if (immediate) {
		              var callNow = !timeout;
		              timeout = setTimeout(function(){
		                  timeout = null;
		              }, wait)
		              if (callNow) func.apply(context, args)
		          }
		          else {
		              timeout = setTimeout(function(){
		                  func.apply(context, args)
		              }, wait);
		          }
		      }
		  }
		  
		  function debounce(wait, immediate) {
		    return function handleDescriptor(target, key, descriptor) {
		      const callback = descriptor.value;
		  
		      if (typeof callback !== 'function') {
		        throw new SyntaxError('Only functions can be debounced');
		      }
		  
		      var fn = _debounce(callback, wait, immediate)
		  
		      return {
		        ...descriptor,
		        value() {
		          fn()
		        }
		      };
		    }
		  }
		  ```
	- ### 4.time
	  background-color:: yellow
		- 用于统计方法执行的时间:
		- ```
		  function time(prefix) {
		    let count = 0;
		    return function handleDescriptor(target, key, descriptor) {
		  
		      const fn = descriptor.value;
		  
		      if (prefix == null) {
		        prefix = `${target.constructor.name}.${key}`;
		      }
		  
		      if (typeof fn !== 'function') {
		        throw new SyntaxError(`@time can only be used on functions, not: ${fn}`);
		      }
		  
		      return {
		        ...descriptor,
		        value() {
		          const label = `${prefix}-${count}`;
		          count++;
		          console.time(label);
		  
		          try {
		            return fn.apply(this, arguments);
		          } finally {
		            console.timeEnd(label);
		          }
		        }
		      }
		    }
		  }
		  ```
	- ### 5.mixin
	  background-color:: yellow
		- 用于将对象的方法混入 Class 中：
		- ```
		  const SingerMixin = {
		    sing(sound) {
		      alert(sound);
		    }
		  };
		  
		  const FlyMixin = {
		    // All types of property descriptors are supported
		    get speed() {},
		    fly() {},
		    land() {}
		  };
		  
		  @mixin(SingerMixin, FlyMixin)
		  class Bird {
		    singMatingCall() {
		      this.sing('tweet tweet');
		    }
		  }
		  
		  var bird = new Bird();
		  bird.singMatingCall();
		  // alerts "tweet tweet"
		  ```
	- ### 6.redux
	  background-color:: yellow
		- 实际开发中，React 与 Redux 库结合使用时，常常需要写成下面这样。
		- ```
		  class MyReactComponent extends React.Component {}
		  
		  export default connect(mapStateToProps, mapDispatchToProps)(MyReactComponent);
		  ```
		- 有了装饰器，就可以改写上面的代码。
		- ```
		  @connect(mapStateToProps, mapDispatchToProps)
		  export default class MyReactComponent extends React.Component {};
		  ```