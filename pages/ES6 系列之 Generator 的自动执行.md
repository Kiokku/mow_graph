- > https://github.com/mqyqingfeng/Blog/issues/99
-
- ## 单个异步任务
	- ```
	  var fetch = require('node-fetch');
	  
	  function* gen(){
	      var url = 'https://api.github.com/users/github';
	      var result = yield fetch(url);
	      console.log(result.bio);
	  }
	  ```
	- 为了获得最终的执行结果，你需要这样做：
	- ```
	  var g = gen();
	  var result = g.next();
	  
	  result.value.then(function(data){
	      return data.json();
	  }).then(function(data){
	      g.next(data);
	  });
	  ```
	- > 首先执行 Generator 函数，获取遍历器对象。
	- > 然后使用 next 方法，执行异步任务的第一阶段，即 fetch(url)。
	  > 注意，由于 fetch(url) 会返回一个 Promise 对象，所以 result 的值为：
	  > `{ value: Promise { <pending> }, done: false }`
	- > 最后我们为这个 Promise 对象添加一个 then 方法，先将其返回的数据格式化(`data.json()`)，再调用 g.next，将获得的数据传进去，由此可以执行异步任务的第二阶段，代码执行完毕。
- ## 多个异步任务
	-
	-