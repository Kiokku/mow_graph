- > https://missing-semester-cn.github.io/2020/shell-tools/
-
- ## Shell 脚本
	- 本节中，我们会专注于 `bash` 脚本，因为它最流行，应用更为广泛。
	- **变量赋值**：`foo=bar`
		- 需要注意的是，`foo = bar` （[[#red]]==使用空格隔开==）是不能正确工作的，因为解释器会调用程序`foo` 并将 `=` 和 `bar`作为参数。
	- **访问变量中存储的数值**：`$foo`
	- **字符串**：字符串通过`'` 和 `"`分隔符来定义，但是它们的含义并不相同。以`'`定义的字符串为[[#blue]]==原义字符串==，其中的变量不会被转义，而 `"`定义的字符串会[[#green]]==将变量值进行替换==。
		- ```
		  foo=bar
		  echo "$foo"
		  # 打印 bar
		  echo '$foo'
		  # 打印 $foo
		  ```
	- **控制流关键字**：`if`, `case`, `while` 和 `for`
	- **函数**：bash使用了很多特殊的变量来表示参数、错误代码和相关变量。下面是列举来其中一些变量，更完整的列表可以参考 [这里](https://www.tldp.org/LDP/abs/html/special-chars.html)。
		- `$0` - 脚本名
		- `$1` 到 `$9` - 脚本的参数。 `$1` 是第一个参数，依此类推。
		- `$@` - 所有参数
		- `$#` - 参数个数
		- `$?` - 前一个命令的返回值`error code`
		- `$$` - 当前脚本的进程识别码
		- `!!` - 完整的上一条命令，包括参数。[[#blue]]==常见应用：当你因为权限不足执行命令失败时，可以使用== `sudo !!`[[#blue]]==再尝试一次==。
		- `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式 shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值。
		- ```
		  mcd () {
		      mkdir -p "$1"
		      cd "$1"
		  }
		  ```
	- **返回输出值**：`STDOUT`
	- **返回错误及错误**：`STDERR`，返回值0表示正常执行，其他所有非0的返回值都表示有错误发生。
	- **短路[运算符](https://en.wikipedia.org/wiki/Short-circuit_evaluation)（short-circuiting）**：退出码可以搭配 `&&`（与操作符）和 `||`（或操作符）使用，用来进行条件判断，决定是否执行其他程序，同一行的多个命令可以用 ; 分隔。程序 `true` 的返回码永远是`0`，`false` 的返回码永远是`1`。
		- ```
		  alse || echo "Oops, fail"
		  # Oops, fail
		  
		  true || echo "Will not be printed"
		  #
		  
		  true && echo "Things went well"
		  # Things went well
		  
		  false && echo "Will not be printed"
		  #
		  
		  false ; echo "This will always run"
		  # This will always run
		  ```
		-
		-
		-
		-