- > https://github.com/mqyqingfeng/Blog/issues/51
-
- ## Math.random
	- ```
	  var values = [1, 2, 3, 4, 5];
	  
	  values.sort(function(){
	      return Math.random() - 0.5;
	  });
	  
	  console.log(values)
	  ```
	- `Math.random() - 0.5` 随机得到一个正数、负数或是 0，[[#green]]==如果是正数则降序排列，如果是负数则升序排列，如果是 0 就不变，然后不断的升序或者降序，最终得到一个乱序的数组。==
	- 看似很美好的一个方案，实际上，效果却不尽如人意。不信我们写个 demo 测试一下：
	- ```
	  var times = [0, 0, 0, 0, 0];
	  
	  for (var i = 0; i < 100000; i++) {
	      
	      let arr = [1, 2, 3, 4, 5];
	      
	      arr.sort(() => Math.random() - 0.5);
	      
	      times[arr[4]-1]++;
	  
	  }
	  
	  console.log(times)
	  ```
	- 测试原理是：将 `[1, 2, 3, 4, 5]` 乱序 10 万次，计算乱序后的数组的最后一个元素是 1、2、3、4、5 的次数分别是多少。
	- 一次随机的结果为：`[30636, 30906, 20456, 11743, 6259]`
	- 我们会发现，最后一个元素为 5 的次数远远低于为 1 的次数，所以这个方案是有问题的。
- ## 插入排序
	- 如果要追究这个问题所在，就必须了解 sort 函数的原理，然而 ECMAScript 只规定了效果，没有规定实现的方式，所以不同浏览器实现的方式还不一样。
	- 以 v8 为例，v8 在处理 sort 方法时，[[#blue]]==当目标数组长度小于 10 时，使用插入排序==；[[#green]]==反之，使用快速排序和插入排序的混合排序==。
	- 本原因在于什么呢？其实就在于在**插入排序**的算法中，[[#red]]==当待排序元素跟有序元素进行比较时，一旦确定了位置，就不会再跟位置前面的有序元素进行比较，所以就乱序的不彻底。==
- ## Fisher–Yates
	- 为什么叫 Fisher–Yates 呢？ 因为这个算法是由 Ronald Fisher 和 Frank Yates 首次提出的。
	- ```
	  function shuffle(a) {
	      var j, x, i;
	      for (i = a.length; i; i--) {
	          j = Math.floor(Math.random() * i);
	          x = a[i - 1];
	          a[i - 1] = a[j];
	          a[j] = x;
	      }
	      return a;
	  }
	  ```
	- > 原理很简单，就是遍历数组元素，然后将当前元素与以后随机位置的元素进行交换，从代码中也可以看出，这样乱序的就会更加彻底。
	-