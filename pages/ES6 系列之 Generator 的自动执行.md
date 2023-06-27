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
	- 我们来看看执行多个异步任务的情况：
	- ```
	  var fetch = require('node-fetch');
	  
	  function* gen() {
	      var r1 = yield fetch('https://api.github.com/users/github');
	      var r2 = yield fetch('https://api.github.com/users/github/followers');
	      var r3 = yield fetch('https://api.github.com/users/github/repos');
	  
	      console.log([r1.bio, r2[0].login, r3[0].full_name].join('\n'));
	  }
	  ```
	- 为了获得最终的执行结果，你可能要写成：
	- ```
	  var g = gen();
	  var result1 = g.next();
	  
	  result1.value.then(function(data){
	      return data.json();
	  })
	  .then(function(data){
	      return g.next(data).value;
	  })
	  .then(function(data){
	      return data.json();
	  })
	  .then(function(data){
	      return g.next(data).value
	  })
	  .then(function(data){
	      return data.json();
	  })
	  .then(function(data){
	      g.next(data)
	  });
	  ```
	- 或者利用递归，我们可以这样写：
	- ```
	  function run(gen) {
	      var g = gen();
	  
	      function next(data) {
	          var result = g.next(data);
	  
	          if (result.done) return;
	  
	          result.value.then(function(data) {
	              return data.json();
	          }).then(function(data) {
	              next(data);
	          });
	  
	      }
	  
	      next();
	  }
	  
	  run(gen);
	  ```
- ## 启动器函数
	- 在 run 这个启动器函数中，我们在 then 函数中将数据格式化 `data.json()`，但在更广泛的情况下，比如 yield 直接跟一个 Promise，而非一个 fetch 函数返回的 Promise，因为没有 json 方法，代码就会报错。所以为了更具备通用性，连同这个例子和启动器，我们修改为：
	- ```
	  var fetch = require('node-fetch');
	  
	  function* gen() {
	      var r1 = yield fetch('https://api.github.com/users/github');
	      var json1 = yield r1.json();
	      var r2 = yield fetch('https://api.github.com/users/github/followers');
	      var json2 = yield r2.json();
	      var r3 = yield fetch('https://api.github.com/users/github/repos');
	      var json3 = yield r3.json();
	  
	      console.log([json1.bio, json2[0].login, json3[0].full_name].join('\n'));
	  }
	  
	  function run(gen) {
	      var g = gen();
	  
	      function next(data) {
	          var result = g.next(data);
	  
	          if (result.done) return;
	  
	          result.value.then(function(data) {
	              next(data);
	          });
	  
	      }
	  
	      next();
	  }
	  
	  run(gen);
	  ```
- ## 回调函数
	-