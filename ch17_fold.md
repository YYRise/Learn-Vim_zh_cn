# 第17章 折叠

在阅读文件时，经常会有一些不相关的文本会妨碍您理解。使用 Vim 折叠可以隐藏这些不必要的信息。

本章中，您将学习如何使用不同的折叠方法。

## 手动折叠

想象您正在折叠一张纸来覆盖一些文本，实际的文本不会消失，它仍在那儿。Vim 折叠的工作方式与此相同，它_折叠_一段文本，在显示时会隐藏起来，但实际上并不会真的删除它。

折叠操作符是`z`。折叠纸张时，它看起来也像字母 "z"。

假设有如下文本：

```text
Fold me
Hold me
```

输入 `zfj`。Vim 将这两行折叠成一行，同时会看到类似消息：

```text
+-- 2 lines: Fold me -----
```

上面的命令分解如下：

* `zf` 是折叠操作符。
* `j` 是用于折叠操作符的动作。

您可以使用 `zo` 打开/展开已折叠文本，使用 `zc` 关闭/收缩文本。

Vim 折叠遵循语法规则。您可以在折叠运算符后，加上一个动作或文本对象。例如，使用 `zfap` 可以折叠外部段落；使用 `zfG` 可以折叠至文件末尾；使用 `zfa{` 可以折叠 `{` 和 `}` 之间的文本。

您可以在可视模式下进行折叠。高亮您想要折叠的区域后 \(`v`, `V`, 或 `Ctrl-v`\)，再输入 `zf` 即可。

一个没有命令行模式版本的 Vim 操作符是不完整的。在命令行模式下，使用 `:fold` 命令可以执行一次折叠。若要折叠当前行及紧随其后的第二行，可以运行：

```text
:,+1fold
```

`,+1` 是要折叠的范围。如果不传递范围参数，默认当前行。`+1` 是代表下一行的范围指示器。运行 `:5,10fold` 可以折叠第5至10行。运行 `:,$fold` 可以折叠当前行至文件末尾。

还有许多其他折叠和展开的命令。我发现他们实在太多，以至于在刚起步时很难记住。最有用的一些命令是：

* `zR` 展开所有折叠。
* `zM` 收缩所有折叠。
* `za` 切换折叠状态。

`zR` 和 `zM` 可用于任意行上，但 `za` 仅能用于已折叠/未折叠的行上。输入 `:h fold-commands` 可查阅更多有关折叠的指令。

## 不同的折叠方法

以上部分涵盖了 Vim 手动折叠的内容。实际上，Vim 有六种不同的折叠方法：

1. 手动折叠
2. 缩进折叠
3. 表达式折叠
4. 语法折叠
5. 差异折叠
6. 标志折叠

运行 `:set foldmethod?` 可查看您当前正在使用哪一种折叠方式。默认情况下，Vim 使用手动方式。

在本章的剩余部分，您将学习其他五种折叠方法。让我们从缩进折叠开始。

## 缩进折叠

要使用缩进折叠，需要将 `'foldmethod'` 选项更改为缩进：

```text
:set foldmethod=indent
```

假设有如下文本：

```text
One
  Two
  Two again
```

运行 `:set foldmethod=indent` 后将看到：

```text
One
+-- 2 lines: Two -----
```

使用缩进折叠后，Vim 将会查看每行的开头有多少空格，并将它与 `'shiftwidth'` 选项进行比较，以此来决定该行可折叠性。`'shiftwidth'` 返回每次缩进所需的空格数。如果运行：

```text
:set shiftwidth?
```

Vim 的默认 `'shiftwidth'` 值为2。对于上面的文本而言，"Two" 和 "Two again" 的开头都有两个空格。当 Vim 看到了空格数_且_`'shiftwidth'`值为2时，Vim 认为该行的缩进折叠级别为1。

假设这次文本开头只有一个空格：

```text
One
 Two
 Two again
```

运行 `:set foldmethod=indent` 后，Vim 不再折叠已缩进的行了，因为这些行没有足够的空格。然而，当您改变 `'shiftwidth'` 的值为1后：

```text
:set shiftwidth=1
```

文本现在可以折叠了！现在，我们将 `'shiftwidth'` 以及文本开头的空格数都重新恢复为2后，另外添加一些内容：

```text
One
  Two
  Two again
    Three
    Three again
```

运行折叠命令 \(`zM`\) 后可以看到：

```text
One
+-- 4 lines: Two -----
```

展开已折叠的行 \(`zR`\)，接着移动光标至 "Three"，然后切换文本的折叠状态 \(`za`\)：

```text
One
  Two
  Two again
+-- 2 lines: Three -----
```

这是啥？叠中叠？

是的，您可以嵌套折叠。文本 "Two" 和 "Two again" 的折叠级别都为1，文本 "Three" 和 "Three again" 的折叠级别都为2。如果在一段可折叠文本中，具有另一段折叠级别更高的可折叠文本，则可以具有多个折叠层。

## 标志折叠

要使用标志折叠，请运行：

```text
:set foldmethod=marker
```

假设有如下文本：

```text
Hello

{{{
world
vim
}}}
```

输入 `zM` 后会看到：

```text
hello

+-- 4 lines: -----
```

Vim 将 `{{{` 和 `}}}` 视为折叠指示器，并折叠其中的内容。使用标志折叠时，Vim 会寻找由 `'foldmarker'` 选项定义的特殊标志，并标记折叠区域。要查看 Vim 使用的标志，请运行：

```text
:set foldmarker?
```

默认情况下，Vim 把 `{{{` 和 `}}}` 作为指示器。如果您想将指示器更改为其他诸如 "foo1" 或 "foo2" 的字符串，可以运行：

```text
:set foldmarker=foo1,foo2
```

假设有如下文本：

```text
hello

foo1
world
vim
foo2
```

现在，Vim 将使用 `foo1` 和 `foo2` 作为新折叠标志。注意，指示器必须是文本字符串，不能是正则表达式。

## 语法折叠

Vim 有一个能够自定义文本语法（高亮、字体粗细、颜色等）的语法系统。本章不会讨论语法系统的工作原理，但您可以使用它来指示要折叠的文本。要使用语法折叠，请运行：

```text
:set foldmethod=syntax
```

假设您有如下文本，并且想折叠方括号里的所有内容：

```text
[
  "one",
  "two",
  "three"
]
```

您需要设置正确的语法定义，来捕获方括号之间的字符：

```text
:syn region testFold start="\\[" end="\\]" transparent fold
```

您应该能看到：

```text
+-- 5 lines: [ -----
```

上面的命令分解如下：

* `:syn` 是语法命令。
* `region` 构造一个可以跨越几行的语法区域。查阅 `:h syntax.txt` 可以获得更多信息。 
* `start="\\[" end="\\]"` 定义区域的起始和结束。您需要转义 \(`\\`\) 方括号，因为它们是特殊字符。
* `transparent` 防止高亮。
* `fold` 当语法匹配到起始字符和结束字符时，增加折叠级别。

## 表达式折叠

表达式折叠允许您定义要匹配折叠的表达式。定义折叠表达式后，Vim 会计算每行的 `'foldexpr'` 值。这是必须配置的变量，它要返回适当的值。如果返回 0，则不折叠行。如果它返回 1，则该行的折叠级别为 1。如果它返回 2，则该线的折叠级别为 2。除了整数外还有其他的值，但我不打算介绍它们。如果你好奇，可以查阅`:h fold-expr`。

首先，更改折叠方法：

```text
:set foldmethod=expr
```

假设您有一份早餐食品列表，并且想要折叠所有以 "p" 开头的早餐项：

```text
donut
pancake
pop-tarts
protein bar
salmon
scrambled eggs
```

其次，更改 `foldexpr` 为捕获以 "p" 开头的表达式：

```text
:set foldexpr=getline(v:lnum)[0]==\\"p\\"
```

这表达式看起来有点吓人。我们来分解下：

* `:set foldexpr` 设置 `'foldexpr'` 为自定义表达式。
* `getline()` 是 Vim 脚本的一个函数，它返回指定行的内容。如运行 `:echo getline(5)` 可以获取第5行的内容。
* `v:lnum` 是 Vim `'foldexpr'` 表达式的特殊变量。Vim 在扫描每一行时，都会将行号存储至 `v:lnum` 变量。
* `[0]` 处于 `getline(v:lnum)[0]` 语境时，代表每一行的第一个字符。Vim 在扫描某一行时，`getline(v:lnum)` 返回该行的内容，而 `getline(v:lnum)[0]` 则返回这一行的第一个字符。例如，我们早餐食品列表的第一行是 "donut"，则 `getline(v:lnum)[0]` 返回 "d"；列表的第二行是 "pancake"，则 `getline(v:lnum)[0]` 返回 "p"。
* `==\\"s\\"` 是等式表达式的后半部分，它检查刚才表达式的计算结果是否等于 "s"。如果是，则返回1，否则返回0。在 Vim 的世界里，1代表真，0代表假。所以，那些以 "s" 开头的行，表达式都会返回1。回想一下本节的开始，如果 `'foldexpr'` 的值为1，则折叠级别为1。

在运行这个表达式后，您将看到：

```text
donut
+-- 3 lines: pancake -----
salmon
scrambled eggs
```

## 差异折叠

Vim 可以对多个文件进行差异比较。

如果您有 `file1.txt`：

```text
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
```

以及 `file2.txt`：

```text
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
emacs is ok
```

运行 `vimdiff file1.txt file2.txt`：

```text
+-- 3 lines: vim is awesome -----
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
vim is awesome
[vim is awesome] / [emacs is ok]
```

Vim 会自动折叠一些相同的行。运行 `vimdiff` 命令时，Vim 会自动使用 `foldmethod=diff`。此时如果运行 `:set foldmethod?`，它将返回 `diff`。

## 持久化折叠

当关闭 Vim 会话后，您将失去所有的折叠信息。假设您有 `count.txt` 文件：

```text
one
two
three
four
five
```

手动从第三行开始往下折叠 \(`:3,$fold`\)：

```text
one
two
+-- 3 lines: three ---
```

当您退出 Vim 再重新打开 `count.txt` 后，这些折叠都不见了！

要在折叠后保留它们，可以运行：

```text
:mkview
```

当打开 `count.txt` 后，运行：

```text
:loadview
```

您的折叠信息都被保留下来了。然而，您需要手动运行 `mkview` 和 `loadview`。我知道，终有一日，我会忘记运行 `mkview` 就关闭文件了，接着便会丢失所有折叠信息。能不能自动实现这个呢？

当然能！要在关闭 `.txt` 文件时自动运行 `mkview`，以及在打开 `.txt` 文件后自动运行 `loadview`，将下列内容添加至您的 vimrc：

```text
autocmd BufWinLeave *.txt mkview
autocmd BufWinEnter *.txt silent loadview
```

在上一章您已经见过 `autocommand` 了，它用于在事件触发时执行一条命令。现在有两个事件可以用于实现操作：

* `BufWinLeave` 从窗口中删除缓冲时。
* `BufWinEnter` 在窗口中加载缓冲时。

现在，即使您在 `.txt` 文件内折叠内容后直接退出 Vim，下次再打开该文件时，您的折叠信息都能自动恢复。

默认情况下，在 Unix 系统的 `~/.vim/view` 内运行 `mkview` 时，Vim 都会保存折叠信息。您可以查阅 `:h 'viewdir'` 来了解更多信息。

## 聪明地学习折叠

当我刚开始使用 Vim 时， 我会跳过学习 Vim 折叠，因为我觉得它不太实用。然而，随着我码龄的增长，我越发觉得折叠功能大有用处。得当地使用折叠功能，文本结构可以更加清晰，犹如一本书籍的目录。

当您学习折叠时，请从手动折叠开始，因为它可以随学随用。然后逐渐学习不同的技巧来使用缩进和标志折叠。最后，学习如何使用语法和表达式折叠。您甚至可以使用后两个来编写您自己的 Vim 插件。

现在，您已经知道如何进行折叠了。让我们来学习下一个：使用 git 进行版本控制。

