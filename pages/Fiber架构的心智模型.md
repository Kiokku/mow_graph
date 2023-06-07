- > https://react.iamkasong.com/process/fiber-mental.html
-
- ## 什么是代数效应
	- `代数效应`是`函数式编程`中的一个概念，用于将`副作用`从`函数`调用中分离。
	- 假设我们有一个函数`getTotalPicNum`，传入2个`用户名称`后，分别查找该用户在平台保存的图片数量，最后将图片数量相加后返回。
	- ```
	  function getTotalPicNum(user1, user2) {
	    const picNum1 = getPicNum(user1);
	    const picNum2 = getPicNum(user2);
	  
	    return picNum1 + picNum2;
	  }
	  ```
	- 在`getTotalPicNum`中，我们不关注`getPicNum`的实现，只在乎“获取到两个数字后将他们相加的结果返回”这一过程。
	- 接下来我们来实现`getPicNum`。
	- "用户在平台保存的图片数量"是保存在服务器中的。所以，为了获取该值，我们需要发起[[#blue]]==异步请求==。
	- ```
	  async function getTotalPicNum(user1, user2) {
	    const picNum1 = await getPicNum(user1);
	    const picNum2 = await getPicNum(user2);
	  
	    return picNum1 + picNum2;
	  }
	  ```
	- 但是，`async await`是有`传染性`的 —— 当一个函数变为`async`后，这意味着调用他的函数也需要是`async`，这破坏了`getTotalPicNum`的同步特性。
	- 我们虚构一个类似`try...catch`的语法 —— `try...handle`与两个操作符`perform`、`resume`。
	- ```
	  function getPicNum(name) {
	    const picNum = perform name;
	    return picNum;
	  }
	  
	  try {
	    getTotalPicNum('kaSong', 'xiaoMing');
	  } handle (who) {
	    switch (who) {
	      case 'kaSong':
	        resume with 230;
	      case 'xiaoMing':
	        resume with 122;
	      default:
	        resume with 0;
	    }
	  }
	  ```
	-