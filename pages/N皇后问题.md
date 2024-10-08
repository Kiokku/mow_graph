- > https://www.hello-algo.com/chapter_backtracking/n_queens_problem/
-
- > ❓ **Question**
  >
  > 根据国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。给定 $n$ 个皇后和一个 $n×n$ 大小的棋盘，寻找使得所有皇后之间无法相互攻击的摆放方案。
- 本题共有三个约束条件：**多个皇后不能在同一行、同一列和同一对角线**。值得注意的是，对角线分为主对角线 `\` 和副对角线 `/` 两种。
- 皇后的数量和棋盘的行数都为 $n$ ，因此我们容易得到第一个推论：**棋盘每行都允许且只允许放置一个皇后**。这意味着，我们可以采取逐行放置策略：从第一行开始，在每行放置一个皇后，直至最后一行结束。**此策略起到了剪枝的作用**，它避免了同一行出现多个皇后的所有搜索分支。
- ![image.png](../assets/image_1689151145453_0.png)
- 为了实现根据列约束剪枝，我们可以利用一个长度为 $n$ 的布尔型数组 `cols` 记录每一列是否有皇后。在每次决定放置前，我们通过 `cols` 将已有皇后的列剪枝，并在回溯中动态更新 `cols` 的状态。
- 那么，如何处理对角线约束呢？设棋盘中某个格子的行列索引为 `(row, col)` ，观察矩阵的某条主对角线，**我们发现该对角线上所有格子的行索引减列索引相等**，即 `row - col` 为恒定值。换句话说，若两个格子满足 `row1 - col1 == row2 - col2` ，则这两个格子一定处在一条主对角线上。
- 利用该性质，我们可以借助一个数组 `diag1` 来记录每条主对角线上是否有皇后。注意，$n$ 维方阵 `row - col` 的范围是 $[−n+1,n−1]$ ，因此共有 $2n−1$ 条主对角线。
- 同理，**次对角线上的所有格子的 `row + col` 是恒定值**。我们可以使用同样的方法，借助数组 `diag2` 来处理次对角线约束。
- ![image.png](../assets/image_1689153223246_0.png)
- ```
  /* 回溯算法：N 皇后 */
  function backtrack(row, n, state, res, cols, diags1, diags2) {
      // 当放置完所有行时，记录解
      if (row === n) {
          res.push(state.map((row) => row.slice()));
          return;
      }
      // 遍历所有列
      for (let col = 0; col < n; col++) {
          // 计算该格子对应的主对角线和副对角线
          const diag1 = row - col + n - 1;
          const diag2 = row + col;
          // 剪枝：不允许该格子所在列、主对角线、副对角线存在皇后
          if (!cols[col] && !diags1[diag1] && !diags2[diag2]) {
              // 尝试：将皇后放置在该格子
              state[row][col] = 'Q';
              cols[col] = diags1[diag1] = diags2[diag2] = true;
              // 放置下一行
              backtrack(row + 1, n, state, res, cols, diags1, diags2);
              // 回退：将该格子恢复为空位
              state[row][col] = '#';
              cols[col] = diags1[diag1] = diags2[diag2] = false;
          }
      }
  }
  
  /* 求解 N 皇后 */
  function nQueens(n) {
      // 初始化 n*n 大小的棋盘，其中 'Q' 代表皇后，'#' 代表空位
      const state = Array.from({ length: n }, () => Array(n).fill('#'));
      const cols = Array(n).fill(false); // 记录列是否有皇后
      const diags1 = Array(2 * n - 1).fill(false); // 记录主对角线是否有皇后
      const diags2 = Array(2 * n - 1).fill(false); // 记录副对角线是否有皇后
      const res = [];
  
      backtrack(0, n, state, res, cols, diags1, diags2);
      return res;
  }
  ```
-