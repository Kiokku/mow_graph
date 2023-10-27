- > https://github.com/mqyqingfeng/Blog/issues/92
-
- ## WeakMap特性
	- ### 1. WeakMap 只接受**对象**作为键名
	  background-color:: pink
		- ```
		  const map = new WeakMap();
		  map.set(1, 2);
		  // TypeError: Invalid value used as weak map key
		  map.set(null, 2);
		  // TypeError: Invalid value used as weak map key
		  ```
	- ### 2. WeakMap 的键名所引用的对象是弱引用
		- > 在计算机程序设计中，弱引用与强引用相对，是指**不能确保其引用的对象不会被垃圾回收器回收的引用**。 一个对象若只被弱引用所引用，则被认为是不可访问（或弱可访问）的，并因此可能在任何时刻被回收。
		- ```
		  let map = new Map();
		  let key = new Array(5 * 1024 * 1024); //Obj
		  
		  // 建立了 map 对 key 所引用对象的强引用
		  map.set(key, 1);
		  // key = null 不会导致 key 的原引用对象被回收
		  key = null;
		  ```
		- 当设置 `key = null` 时，只是去掉了 key 对 Obj 的强引用，并没有去除 arr 对 Obj 的强引用，所以 Obj 还是不会被回收掉。
		- ```
		  const wm = new WeakMap();
		  let key = new Array(5 * 1024 * 1024);
		  wm.set(key, 1);
		  key = null;
		  ```
		- 当我们设置 `wm.set(key, 1)` 时，其实建立了 wm 对 key 所引用的对象的弱引用，但因为 `let key = new Array(5 * 1024 * 1024)` 建立了 key 对所引用对象的强引用，被引用的对象并不会被回收，但是当我们设置 `key = null` 的时候，就只有 wm 对所引用对象的弱引用，下次垃圾回收机制执行的时候，该引用对象就会被回收掉。
	- [[#red]]==WeakMap只有四个方法可用：get()、set()、has()、delete()==
- ## 应用
	- ### 1. 在 DOM 对象上保存相关数据
	  background-color:: pink
		- ```
		  let wm = new WeakMap(), element = document.querySelector(".element");
		  wm.set(element, "data");
		  
		  let value = wm.get(elemet);
		  console.log(value); // data
		  
		  element.parentNode.removeChild(element);
		  element = null;
		  ```
	- ### 2. 数据缓存
	  background-color:: pink
		- 当我们需要**关联对象和数据**，比如在不修改原有对象的情况下储存某些属性或者根据对象储存一些计算的值等，而又不想管理这些数据的死活时非常适合考虑使用 WeakMap。数据缓存就是一个非常好的例子：
		- ```
		  const cache = new WeakMap();
		  function countOwnKeys(obj) {
		      if (cache.has(obj)) {
		          console.log('Cached');
		          return cache.get(obj);
		      } else {
		          console.log('Computed');
		          const count = Object.keys(obj).length;
		          cache.set(obj, count);
		          return count;
		      }
		  }
		  ```
	- ### 3. 私有属性
	  background-color:: pink
		- WeakMap 也可以被用于实现私有变量，不过在 ES6 中实现私有变量的方式有很多种，这只是其中一种：
		- ```
		  const privateData = new WeakMap();
		  
		  class Person {
		      constructor(name, age) {
		          privateData.set(this, { name: name, age: age });
		      }
		  
		      getName() {
		          return privateData.get(this).name;
		      }
		  
		      getAge() {
		          return privateData.get(this).age;
		      }
		  }
		  
		  export default Person;
		  ```
	- ### 4. 防止内存泄漏
	  background-color:: pink
		- 由于 WeakMap 只弱引用键名，当对应的键名不再被引用时，WeakMap 会自动进行垃圾回收，并清除对应的键值对。