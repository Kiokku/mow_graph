- > https://github.com/mqyqingfeng/Blog/issues/52
-
- ## 前言
	- v8 是 Chrome 的 JavaScript 引擎，其中关于数组的排序完全采用了 JavaScript 实现。
	- 排序采用的算法跟数组的长度有关，当数组长度小于等于 10 时，采用插入排序，大于 10 的时候，采用快速排序。(当然了，这种说法并不严谨)。
	- 我们先来看看插入排序和快速排序。
- ## 插入排序
	- [[插入排序]]
- ## 快速排序
	- [[快速排序]]
- ## v8 基准选择
	- v8 选择基准的原理是从头和尾之外再选择一个元素，然后三个值排序取中间值。