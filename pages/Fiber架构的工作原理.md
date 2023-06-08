- > https://react.iamkasong.com/process/doubleBuffer.html
-
- ## 什么是“双缓存”
	- 当我们用`canvas`绘制动画，每一帧绘制前都会调用`ctx.clearRect`清除上一帧的画面。
	- 如果当前帧画面计算量比较大，导致清除上一帧画面到绘制当前帧画面之间有较长间隙，就会出现白屏。
	- 为了解决这个问题，我们可以在**内存中绘制当前帧动画**，绘制完毕后直接用当前帧替换上一帧画面，由于省去了两帧替换间的计算时间，不会出现从白屏到出现画面的闪烁情况。
	- 这种**在内存中构建并直接替换**的技术叫做[双缓存](https://baike.baidu.com/item/%E5%8F%8C%E7%BC%93%E5%86%B2)
	- `React`使用“双缓存”来完成`Fiber树`的构建与替换——对应着`DOM树`的创建与更新。
- ## 双缓存Fiber树
	- 在`React`中最多会同时存在两棵`Fiber树`。当前屏幕上显示内容对应的`Fiber树`称为`current Fiber树`，正在内存中构建的`Fiber树`称为`workInProgress Fiber树`。
	- `current Fiber树`中的`Fiber节点`被称为`current fiber`，`workInProgress Fiber树`中的`Fiber节点`被称为`workInProgress fiber`，他们通过`alternate`属性连接。
	- ```
	  currentFiber.alternate === workInProgressFiber;
	  workInProgressFiber.alternate === currentFiber;
	  ```
	-