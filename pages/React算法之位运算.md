## 概念
	- 位运算直接处理每一个比特位(bit), 是非常底层的运算, [[#green]]==优势是速度快==, [[#red]]==劣势就是不直观且只支持整数运算.==
- ## 特性
	- |位运算|用法|描述|
	  |--|--|--|
	  |按位与(&)|a & b|对于每一个比特位,两个操作数都为 1 时, 结果为 1, 否则为 0|
	  |按位或(  )|a  b|对于每一个比特位,两个操作数都为 0 时, 结果为 0, 否则为 1|
	  |按位异或(^)|a ^ b|对于每一个比特位,两个操作数相同时, 结果为 0, 否则为 1|
	  |按位非(~)|~ a|反转操作数的比特位, 即 0 变成 1, 1 变成 0|
	  |左移(<<)|a << b|将 a 的二进制形式向左移 b (< 32) 比特位, 右边用 0 填充|
	  |有符号右移(>>)|a >> b|将 a 的二进制形式向右移 b (< 32) 比特位, 丢弃被移除的位, 左侧以最高位来填充|
	  |无符号右移(>>>)|a >>> b|将 a 的二进制形式向右移 b (< 32) 比特位, 丢弃被移除的位, 并用 0 在左侧填充|
	- [`ES5`规范中](https://www.ecma-international.org/ecma-262/5.1/#sec-11.10):
		- 位运算只能在[[#blue]]==整型==变量之间进行运算
		  logseq.order-list-type:: number
		- JavaScript 中的`Number`类型在底层都是以浮点数(参考 IEEE754 标准)进行存储.
		  logseq.order-list-type:: number
		- JavaScript 中所有的按位操作符的操作数都会被[转成补码（two's complement）](https://www.ecma-international.org/ecma-262/5.1/#sec-9.5)形式的`有符号32位整数`.
		  logseq.order-list-type:: number
	- 所以在 JavaScript 中使用位运算时, 有 2 种情况会造成[[#red]]==结果异常==：
		- 操作数为浮点型(虽然底层都是浮点型, 此处理解为显示性的浮点型)
		  logseq.order-list-type:: number
			- `转换流程: 浮点数 -> 整数(丢弃小数位) -> 位运算`
		- 操作数的大小超过`Int32`范围(`-2^31 ~ 2^31-1`). 超过范围的二进制位会被截断, 取`低位32bit`.
		  logseq.order-list-type:: number
			- ```
			  Before: 11100110111110100000000000000110000000000001
			  After:              10100000000000000110000000000001
			  ```
	- [[#green]]==隐式转换：==
		- `'str' >>> 0; *//  ===> Number('str') >>> 0  ===> NaN >>> 0 = 0*`
- ## 基本使用
	- ### 枚举属性
	  background-color:: pink
		- 通过位移的方式, 定义一些枚举常量
		- ```
		  const A = 1 << 0; // 0b00000001
		  const B = 1 << 1; // 0b00000010
		  const C = 1 << 2; // 0b00000100
		  ```
	- ### 位掩码
	  background-color:: pink
		- 通过位移定义的一组枚举常量, 可以利用位掩码的特性, 快速操作这些枚举产量(增加, 删除, 比较).
		- 属性增加`|`
		  logseq.order-list-type:: number
			- `ABC = A | B | C`
			  logseq.order-list-type:: number
		- 属性删除`& ~`
		  logseq.order-list-type:: number
			- `AB = ABC & ~C`
			  logseq.order-list-type:: number
		- 属性比较
		  logseq.order-list-type:: number
			- AB 当中包含 B: `AB & B === B`
			  logseq.order-list-type:: number
			- AB 当中不包含 C: `AB & C === 0`
			  logseq.order-list-type:: number
			- A 和 B 相等: `A === B`
			  logseq.order-list-type:: number
		- ```
		  const A = 1 << 0; // 0b00000001
		  const B = 1 << 1; // 0b00000010
		  const C = 1 << 2; // 0b00000100
		  
		  // 增加属性
		  const ABC = A | B | C; // 0b00000111
		  // 删除属性
		  const AB = ABC & ~C; // 0b00000011
		  
		  // 属性比较
		  // 1. AB当中包含B
		  console.log((AB & B) === B); // true
		  // 2. AB当中不包含C
		  console.log((AB & C) === 0); // true
		  // 3. A和B相等
		  console.log(A === B); // false
		  ```
- ## React 当中的使用场景
	- ### 优先级管理 lanes
	  background-color:: green
		- lanes 是`17.x`版本中开始引入的重要概念, 代替了`16.x`版本中的`expirationTime`, 作为`fiber`对象的一个属性(位于`react-reconciler`包), 主要控制 fiber 树在构造过程中的优先级。
		- #### 变量定义
			- 源码[ReactFiberLane.js](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberLane.js#L74-L103)中的定义
			- ```
			  //类型定义
			  export opaque type Lanes = number;
			  export opaque type Lane = number;
			  
			  // 变量定义
			  export const NoLanes: Lanes = /*                        */ 0b0000000000000000000000000000000;
			  export const NoLane: Lane = /*                          */ 0b0000000000000000000000000000000;
			  
			  export const SyncLane: Lane = /*                        */ 0b0000000000000000000000000000001;
			  export const SyncBatchedLane: Lane = /*                 */ 0b0000000000000000000000000000010;
			  
			  export const InputDiscreteHydrationLane: Lane = /*      */ 0b0000000000000000000000000000100;
			  const InputDiscreteLanes: Lanes = /*                    */ 0b0000000000000000000000000011000;
			  
			  const InputContinuousHydrationLane: Lane = /*           */ 0b0000000000000000000000000100000;
			  const InputContinuousLanes: Lanes = /*                  */ 0b0000000000000000000000011000000;
			  // ...
			  // ...
			  
			  const NonIdleLanes = /*                                 */ 0b0000111111111111111111111111111;
			  
			  export const IdleHydrationLane: Lane = /*               */ 0b0001000000000000000000000000000;
			  const IdleLanes: Lanes = /*                             */ 0b0110000000000000000000000000000;
			  
			  export const OffscreenLane: Lane = /*                   */ 0b1000000000000000000000000000000;
			  ```
		- #### [方法定义](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberLane.js#L121-L194)
			- ```
			  function getHighestPriorityLanes(lanes: Lanes | Lane): Lanes {
			    // 判断 lanes中是否包含 SyncLane
			    if ((SyncLane & lanes) !== NoLanes) {
			      return_highestLanePriority = SyncLanePriority;
			      return SyncLane;
			    }
			    // 判断 lanes中是否包含 SyncBatchedLane
			    if ((SyncBatchedLane & lanes) !== NoLanes) {
			      return_highestLanePriority = SyncBatchedLanePriority;
			      return SyncBatchedLane;
			    }
			    // ...
			    // ... 省略其他代码
			    return lanes;
			  }
			  ```
			- 在方法定义中, 也是通过位掩码的特性来判断二进制形式变量之间的关系. 除了常规的位掩码操作外, 特别说明其中 2 个技巧性强的函数:
				- `getHighestPriorityLane`: 分离出最[[#green]]==高优先级==
				  logseq.order-list-type:: number
					- ```
					  function getHighestPriorityLane(lanes: Lanes) {
					    return lanes & -lanes;
					  }
					  ```
					- 通过`lanes & -lanes`可以分离出所有比特位中最右边的 1, 具体来讲:
						- 假设 `lanes(InputDiscreteLanes) = 0b0000000000000000000000000011000`
						- `-lanes = 0b1111111111111111111111111101000` (全部取反后 + 1)
						- 所以 `lanes & -lanes = 0b0000000000000000000000000001000`
						- 相比最初的 InputDiscreteLanes, 分离出来了`最右边的1`
						- 通过 lanes 的定义, 数字越小的优先级越高, 所以此方法可以获取`最高优先级的lane`
				- `getLowestPriorityLane`: 分离出[[#red]]==最低优先级==
				  logseq.order-list-type:: number
					- ```
					  function getLowestPriorityLane(lanes: Lanes): Lane {
					    // This finds the most significant non-zero bit.
					    const index = 31 - clz32(lanes);
					    return index < 0 ? NoLanes : 1 << index;
					  }
					  ```
					- `clz32(lanes)`返回一个数字在转换成 32 无符号整形数字的二进制形式后, 前导 0 的个数
						- 假设 `lanes(InputDiscreteLanes) = 0b0000000000000000000000000011000`
						- 那么 `clz32(lanes) = 27`
						- `index = 31 - clz32(lanes) = 4`
						- 最后 `1 << index = 0b0000000000000000000000000010000`
						- 相比最初的 InputDiscreteLanes, 分离出来了`最左边的1`
						- 通过 lanes 的定义, 数字越小的优先级越高, 所以此方法可以获取最低优先级的 lane
	- ### 执行上下文 ExecutionContext
	  background-color:: green
		- `ExecutionContext`定义于`react-reconciler`包中, 代表`reconciler`在运行时的上下文状态。
		- [变量定义](https://github.com/facebook/react/blob/v17.0.2/packages/react-reconciler/src/ReactFiberWorkLoop.old.js#L247-L256):
			- ```
			  export const NoContext = /*             */ 0b0000000;
			  const BatchedContext = /*               */ 0b0000001;
			  const EventContext = /*                 */ 0b0000010;
			  const DiscreteEventContext = /*         */ 0b0000100;
			  const LegacyUnbatchedContext = /*       */ 0b0001000;
			  const RenderContext = /*                */ 0b0010000;
			  const CommitContext = /*                */ 0b0100000;
			  export const RetryAfterError = /*       */ 0b1000000;
			  
			  // ...
			  
			  // Describes where we are in the React execution stack
			  let executionContext: ExecutionContext = NoContext;
			  ```
		- 使用：
			- ```
			  // scheduleUpdateOnFiber函数中包含了好多关于executionContext的判断(都是使用位运算)
			  export function scheduleUpdateOnFiber(
			    fiber: Fiber,
			    lane: Lane,
			    eventTime: number,
			  ) {
			    if (root === workInProgressRoot) {
			      // 判断: executionContext 不包含 RenderContext
			      if (
			        deferRenderPhaseUpdateToNextBatch ||
			        (executionContext & RenderContext) === NoContext
			      ) {
			        // ...
			      }
			    }
			    if (lane === SyncLane) {
			      if (
			        // 判断: executionContext 包含 LegacyUnbatchedContext
			        (executionContext & LegacyUnbatchedContext) !== NoContext &&
			        // 判断: executionContext 不包含 RenderContext或CommitContext
			        (executionContext & (RenderContext | CommitContext)) === NoContext
			      ) {
			        // ...
			      }
			    }
			    // ...
			  }
			  ```
			-