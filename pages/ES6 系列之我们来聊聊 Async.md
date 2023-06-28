- > https://github.com/mqyqingfeng/Blog/issues/100
-
- ## async
	- ES2017 标准引入了 async 函数，使得异步操作变得更加方便。
	- [[#blue]]==在异步处理上，async 函数就是 Generator 函数的语法糖==。
	- 其实 async 函数的实现原理，就是将 Generator 函数和自动执行器，包装在一个函数里。
	- ```
	  async function fn(args) {
	    // ...
	  }
	  
	  // 等同于
	  
	  function fn(args) {
	    return spawn(function* () {
	      // ...
	    });
	  }
	  ```
- ## async 与 Promise
	- ### 1. 代码更加简洁
	  background-color:: pink
		- ```
		  /**
		   * 示例一
		   */
		  function fetch() {
		    return (
		      fetchData()
		      .then(() => {
		        return "done"
		      });
		    )
		  }
		  
		  async function fetch() {
		    await fetchData()
		    return "done"
		  };
		  ```
	- ### 2. 错误处理
	  background-color:: pink
		- ```
		  function fetch() {
		    try {
		      fetchData()
		        .then(result => {
		          const data = JSON.parse(result)
		        })
		        .catch((err) => {
		          console.log(err)
		        })
		    } catch (err) {
		      console.log(err)
		    }
		  }
		  ```
		- 在这段代码中，try/catch 能捕获 fetchData() 中的一些 Promise 构造错误，但是不能捕获 JSON.parse 抛出的异常，如果要处理 JSON.parse 抛出的异常，需要添加 catch 函数重复一遍异常处理的逻辑。
		- async/await 的出现使得 try/catch 就可以捕获同步和异步的错误。
		- ```
		  async function fetch() {
		    try {
		      const data = JSON.parse(await fetchData())
		    } catch (err) {
		      console.log(err)
		    }
		  };
		  ```
	- ### 3. 调试
	  background-color:: pink
		-
-