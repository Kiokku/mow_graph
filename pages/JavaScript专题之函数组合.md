- >https://github.com/mqyqingfeng/Blog/issues/45
-
- > 🌟 需求
  >
  > 我们需要写一个函数，输入 'kevin'，返回 'HELLO, KEVIN'。
-
- ## 尝试
	- ```
	  var toUpperCase = function(x) { return x.toUpperCase(); };
	  var hello = function(x) { return 'HELLO, ' + x; };
	  
	  var greet = function(x){
	      return hello(toUpperCase(x));
	  };
	  
	  greet('kevin');
	  ```
	- 还好我们只有两个步骤，首先小写转大写，然后拼接字符串。如果有更多的操作，greet 函数里就需要更多的嵌套，类似于 `fn3(fn2(fn1(fn0(x))))`。
- ## 优化
	- 试想我们写个 compose 函数：
	- ```
	  var compose = function(f,g) {
	      return function(x) {
	          return f(g(x));
	      };
	  };
	  
	  var greet = compose(hello, toUpperCase);
	  greet('kevin');
	  ```
	- 利用 compose 将两个函数组合成一个函数，让[[#blue]]==代码从右向左运行==，而不是由内而外运行，可读性大大提升。这便是**函数组合**。
	- `compose(d, c, b, a)`
- ## compose
	- underscore 的 compose 函数的实现：
	- ```
	  function compose() {
	      var args = arguments;
	      var start = args.length - 1;
	      return function() {
	          var i = start;
	          var result = args[start].apply(this, arguments);
	          while (i--) result = args[i].call(this, result);
	          return result;
	      };
	  };
	  ```
- ## pointfree
	- > [pointfree参考](https://www.ruanyifeng.com/blog/2017/03/pointfree.html)
	  >
	  >**不使用所要处理的值，只合成运算过程。**中文可以译作"无值"风格。
	- pointfree 指的是函数无须提及将要操作的数据是什么样的。依然是以最初的需求为例：
	- ```
	  // 需求：输入 'kevin'，返回 'HELLO, KEVIN'。
	  
	  // 非 pointfree，因为提到了数据：name
	  var greet = function(name) {
	      return ('hello ' + name).toUpperCase();
	  }
	  
	  // pointfree
	  // 先定义基本运算，这些可以封装起来复用
	  var toUpperCase = function(x) { return x.toUpperCase(); };
	  var hello = function(x) { return 'HELLO, ' + x; };
	  
	  var greet = compose(hello, toUpperCase);
	  greet('kevin');
	  ```
	- 我们再举个稍微复杂一点的例子，为了方便书写，我们需要借助在[[JavaScript专题之函数柯里化]]中写到的 [curry](logseq://graph/mow_graph?block-id=6499b665-8c15-47cb-9c47-99390f3239b0) 函数：
	- ```
	  // 需求：输入 'kevin daisy kelly'，返回 'K.D.K'
	  
	  // 非 pointfree，因为提到了数据：name
	  var initials = function (name) {
	      return name.split(' ').map(compose(toUpperCase, head)).join('. ');
	  };
	  
	  // pointfree
	  // 先定义基本运算
	  var split = curry(function(separator, str) { return str.split(separator) })
	  var head = function(str) { return str.slice(0, 1) }
	  var toUpperCase = function(str) { return str.toUpperCase() }
	  var join = curry(function(separator, arr) { return arr.join(separator) })
	  var map = curry(function(fn, arr) { return arr.map(fn) })
	  
	  var initials = compose(join('.'), map(compose(toUpperCase, head)), split(' '));
	  
	  initials("kevin daisy kelly");
	  ```
	- 也许你会想，这种写法好麻烦呐，我们还需要定义那么多的基础函数……可是如果有工具库已经帮你写好了呢？比如 [ramda.js](http://ramda.cn/docs/)：
	- ```
	  // 使用 ramda.js
	  var initials = R.compose(R.join('.'), R.map(R.compose(R.toUpper, R.head)), R.split(' '));
	  ```
	- >Pointfree 的本质就是[[#blue]]==使用一些通用的函数，组合出各种复杂运算。==上层运算不要直接操作数据，而是通过底层函数去处理。即不使用所要处理的值，只合成运算过程。
	  >
	  > Pointfree 模式能够帮助我们减少不必要的命名，让代码保持简洁和通用，更符合语义，更容易复用，测试也变得轻而易举。
- ## 实战
  id:: 6499c79d-5687-4265-8139-d0c2d9d00a29
	- 这个例子来自于 [Favoring Curry](http://fr.umio.us/favoring-curry/)，[[#green]]==建议阅读==，有助于加深对柯里化的理解：
	- 假设我们从服务器获取这样的数据：
	- ```
	  var data = {
	      result: "SUCCESS",
	      tasks: [
	          {id: 104, complete: false,            priority: "high",
	                    dueDate: "2013-11-29",      username: "Scott",
	                    title: "Do something",      created: "9/22/2013"},
	          {id: 105, complete: false,            priority: "medium",
	                    dueDate: "2013-11-22",      username: "Lena",
	                    title: "Do something else", created: "9/22/2013"},
	          {id: 107, complete: true,             priority: "high",
	                    dueDate: "2013-11-22",      username: "Mike",
	                    title: "Fix the foo",       created: "9/22/2013"},
	          {id: 108, complete: false,            priority: "low",
	                    dueDate: "2013-11-15",      username: "Punam",
	                    title: "Adjust the bar",    created: "9/25/2013"},
	          {id: 110, complete: false,            priority: "medium",
	                    dueDate: "2013-11-15",      username: "Scott",
	                    title: "Rename everything", created: "10/2/2013"},
	          {id: 112, complete: true,             priority: "high",
	                    dueDate: "2013-11-27",      username: "Lena",
	                    title: "Alter all quuxes",  created: "10/5/2013"}
	      ]
	  };
	  ```
	- 我们需要写一个名为 getIncompleteTaskSummaries 的函数，接收一个 username 作为参数，从服务器获取数据，然后筛选出这个用户的未完成的任务的 ids、priorities、titles、和 dueDate 数据，并且按照日期升序排序。
	- 以 Scott 为例，最终筛选出的数据为：
	- ```
	  [
	      {id: 110, title: "Rename everything", 
	          dueDate: "2013-11-15", priority: "medium"},
	      {id: 104, title: "Do something", 
	          dueDate: "2013-11-29", priority: "high"}
	  ]
	  ```
	- ### 普通的方式为：
	  background-color:: pink
		- ```
		  // 第一版 过程式编程
		  var fetchData = function() {
		      // 模拟
		      return Promise.resolve(data)
		  };
		  
		  var getIncompleteTaskSummaries = function(membername) {
		       return fetchData()
		           .then(function(data) {
		               return data.tasks;
		           })
		           .then(function(tasks) {
		               return tasks.filter(function(task) {
		                   return task.username == membername
		               })
		           })
		           .then(function(tasks) {
		               return tasks.filter(function(task) {
		                   return !task.complete
		               })
		           })
		           .then(function(tasks) {
		               return tasks.map(function(task) {
		                   return {
		                       id: task.id,
		                       dueDate: task.dueDate,
		                       title: task.title,
		                       priority: task.priority
		                   }
		               })
		           })
		           .then(function(tasks) {
		               return tasks.sort(function(first, second) {
		                   var a = first.dueDate,
		                       b = second.dueDate;
		                   return a < b ? -1 : a > b ? 1 : 0;
		               });
		           })
		           .then(function(task) {
		               console.log(task)
		           })
		  };
		  
		  getIncompleteTaskSummaries('Scott')
		  ```
	- ### 使用 pointfree 模式：
	  background-color:: pink
		- ```
		  // 第二版 pointfree 改写
		  var fetchData = function() {
		      return Promise.resolve(data)
		  };
		  
		  // 编写基本函数
		  var prop = curry(function(name, obj) {
		      return obj[name];
		  });
		  
		  var propEq = curry(function(name, val, obj) {
		      return obj[name] === val;
		  });
		  
		  var filter = curry(function(fn, arr) {
		      return arr.filter(fn)
		  });
		  
		  var map = curry(function(fn, arr) {
		      return arr.map(fn)
		  });
		  
		  var pick = curry(function(args, obj){
		      var result = {};
		      for (var i = 0; i < args.length; i++) {
		          result[args[i]] = obj[args[i]]
		      }
		      return result;
		  });
		  
		  var sortBy = curry(function(fn, arr) {
		      return arr.sort(function(a, b){
		          var a = fn(a),
		              b = fn(b);
		          return a < b ? -1 : a > b ? 1 : 0;
		      })
		  });
		  
		  var getIncompleteTaskSummaries = function(membername) {
		      return fetchData()
		          .then(prop('tasks'))
		          .then(filter(propEq('username', membername)))
		          .then(filter(propEq('complete', false)))
		          .then(map(pick(['id', 'dueDate', 'title', 'priority'])))
		          .then(sortBy(prop('dueDate')))
		          .then(console.log)
		  };
		  
		  getIncompleteTaskSummaries('Scott')
		  ```
	- ### 直接使用 ramda.js:
	  background-color:: pink
		- ```
		  // 第三版 使用 ramda.js
		  var fetchData = function() {
		      return Promise.resolve(data)
		  };
		  
		  var getIncompleteTaskSummaries = function(membername) {
		      return fetchData()
		          .then(R.prop('tasks'))
		          .then(R.filter(R.propEq('username', membername)))
		          .then(R.filter(R.propEq('complete', false)))
		          .then(R.map(R.pick(['id', 'dueDate', 'title', 'priority'])))
		          .then(R.sortBy(R.prop('dueDate')))
		          .then(console.log)
		  };
		  
		  getIncompleteTaskSummaries('Scott')
		  ```
	- ### 使用 compose
	  background-color:: pink
		- ```
		  // 第四版 使用 compose
		  var fetchData = function() {
		      return Promise.resolve(data)
		  };
		  
		  var getIncompleteTaskSummaries = function(membername) {
		      return fetchData()
		          .then(R.compose(
		              console.log,
		              R.sortBy(R.prop('dueDate')),
		              R.map(R.pick(['id', 'dueDate', 'title', 'priority'])
		              ),
		              R.filter(R.propEq('complete', false)),
		              R.filter(R.propEq('username', membername)),
		              R.prop('tasks'),
		          ))
		  };
		  
		  getIncompleteTaskSummaries('Scott')
		  ```
		- compose 是从右到左依此执行，当然你也可以写一个从左到右的版本，但是从右向左执行更加能够反映数学上的含义。
		- ramda.js 提供了一个 R.pipe 函数，可以做的从左到右。
-