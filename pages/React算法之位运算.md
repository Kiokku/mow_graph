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
			-