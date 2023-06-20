- > https://github.com/mqyqingfeng/Blog/issues/92
-
- ## WeakMap特性
	- ### 1. WeakMap 只接受对象作为键名
	  background-color:: pink
		- ```
		  const map = new WeakMap();
		  map.set(1, 2);
		  // TypeError: Invalid value used as weak map key
		  map.set(null, 2);
		  // TypeError: Invalid value used as weak map key
		  ```
		-
-