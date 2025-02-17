# 第4章 vim语法

## Vim 语法

刚接触Vim时很容易被Vim许多复杂的命令吓到，如果你看到一个Vim的用户使用`gUfV`或`1GdG`，你可能不能立刻想到这些命令是在做什么。这一章中，我将把Vim命令的结构拆分成一个简单的语法规则进行讲解。

这一章将是本书中最重要的一章，一旦你理解了Vim命令的语法结构，你将能够和Vim"说话"。注意，在这一章中当我讨论Vim语言时，我讨论并不是 Vimscript\(Vim自带的插件编写和自定义设置的语言\)，这里我讨论的是Vim中normal模式的下的命令的通用规则。

## 如何学习一门语言

我并不是一个英语为母语的人，当我13岁移民到美国时我学习的英语，我会通过做三件事情建立我的语言能力： 1. 学习语法规则 2. 扩展我的词汇量 3. 练习，练习，练习

同样的，为了说好Vim语言，你需要学习语法规则，增加词汇量，并且不断练习直到你可以把执行命令变成肌肉记忆。

## 语法规则

你只需要知道一个Vim语言的语法规则：

```text
verb + noun # 动词 + 名词
```

这就类似与在英语中的祈使句：

* Eat\(verb\) a donut\(noun\)
* Kick\(verb\) a ball\(noun\)
* Learn\(verb\) the Vim Editor\(noun\)

现在你需要的就是用Vim中基本的动词和名字来建立你的词汇表

## 词汇表

### 名词\(动作 Motion\)

我们这里将**动作**作为名词，**动作**用来在Vim中到处移动，他们也是Vim中的名词。下面列出了一些常见的**动作**的例子：

```text
h    左
j    下
k    上
l    右
w    向前移动到下一个单词的开头
}    跳转到下一个段落
$    跳转到当前行的末尾
```

在之后的章节你将学习更多的关于**动作**的内容，所以如果你不理解上面这些**动作**也不必担心。

### 动词\(操作符 Operator\)

根据`:h operator`，Vim共有16个**操作符**，然而根据我的经验，学习这3个**操作符**在80%的情况下就已经够用了

```text
y    yank(复制)
d    delete(删除)
c    change 删除文本，将删除的文本存到寄存器中，进入插入模式
```

现在你已经知道了基本的动词和名词，我们来用一下我们的语法规则。假设你有下面这段文本：

```text
const learn = "Vim";
```

* 复制当前位置到行尾的所有内容：`y$`
* 删除当前位置到下一个单词的开头：`dw`
* 修改当前位置到这个段落的结尾：`c}`

**动作**也接受数字作为参数\(这个部分我将在下个章节展开\)，如果你需要向上移动3行，你可以用`3k`代替按3次`k`

* 向左拷贝2个字符：`y2h`
* 删除后两个单词：`d2w`
* 修改后面两行：`c2j`

目前，你也许需要想很久才能完成一个简单的命令，不过我刚开始时也是这样，我也经历过类似的挣扎的阶段但是不久我的速度就快了起来，你也一样。

作为补充，行级的**操作符**在文本编辑中和其他的**操作符**一样，Vim允许你通过按两次命令执行行级的操作，例如`dd`，`yy`，`cc`来执行删除，复制或修改整个行。

我希望这些内容能够对你有用，但是到目前为止还没有结束，Vim有另一种类型的名词：文本对象\(text object\)

## 更多名词\(文本对象\)

想象一下你现在正在某个被括号包围的文本中例如`(hello Vim)`，你现在想要删掉括号中的所有内容，你会怎样快速的完成它？是否有一种方法能够把括号中内容作为整体删除呢？

答案是有的。文本通常是结构化的，特别是代码经常被放置在小括号、中括号、大括号、引号等当中。Vim提供了一种处理这种结构的文本对象的方法。

文本对象可以被**操作符**使用，这里有两类文本对象：

```text
i + object  内部文本对象
a + object  外部文本对象
```

**内部文本对象**选中的部分不包含包围文本对象的空白或括号等，**外部文本对象**则包括了包围内容的空白或括号等对象。外部对象总是比内部对象选中的内容更多，因此如果你的光标位于一对括号内部，例如`(hello Vim)`中：

* 删除括号内部的内容但保留括号：`di(`
* 删除括号以及内部的内容：`da(`

让我们看一些别的例子，假设你有这样一段Javascript的函数，你的光标停留在"Hello"上：

```text
const hello = function() {
    console.log("Hello Vim");
    return true;
}
```

* 删除整个"Hello Vim"：`di(`
* 删除整个函数\(被{}包含\)：`di{`
* 删除"Hello"这个词：`diw`

文本对象很强大因为你可以在一个位置指向不同的对象，能够删除一对括号、函数体或整个单词的文本对象中的内容。此外，当你看到`di(`，`di{`和`diw`时，你也可以很好的意识到他们表示的是什么。

让我们来看最后一个例子。假设你有这样一些html的标签的文本：

```text
<div>
  <h1>Header1</h1>
  <p>Paragraph1</p>
  <p>Paragraph2</p>
</div>
```

如果你的光标位于"Header1"文本上：

* 删除"Header1"：`dit`
* 删除`<h1>Header1</h1>`：`dat`

如果你的光标在"div"文本上：

* 删除`h1`和所有`p`标签的行：`dit`
* 删除所有文本：`dat`
* 删除"div"：`di<`

下面列举的一些通常见到的文本对象：

```text
w     一个单词
p     一个段落
s     一个句子
(或)  一对()
{或}  一对{}
[或]  一对[]
<或>  一对<>
t     XML标签
"     一对""
'     一对''
`     一对``
```

你可以通过`:h text-objects`了解更多

## 结合性和语法

在学习Vim的语法之后，让我们来讨论一下Vim中的结合性以及为什么在文本编辑器中这是一个强大的功能。

结合性意味着你有很多可以组合起来完成更复杂命令的普通命令，就像你在编程中可以通过一些简单的抽象建立更复杂的抽象，在Vim中你可以通过简单的命令的组合执行更复杂的命令。Vim语法正是Vim中命令的可结合性的体现。

Vim的结合性最强大之处体现在它和外部程序结合时，Vim有一个**过滤操作符**`!`可以用外部程序过滤我们的文本。假设你有下面这段混乱的文本并且你想把它用tab格式化的更好看的一些：

```text
Id|Name|Cuteness
01|Puppy|Very
02|Kitten|Ok
03|Bunny|Ok
```

这件事情通过Vim命令不太容易完成，但是你可以通过终端提供的命令`column`很快的完成它，当你的光标位于"Id"上时，运行`!}column -t -s "|"`，你的文本就变得整齐了许多：

```text
Id  Name    Cuteness
01  Puppy   Very
02  Kitten  Ok
03  Bunny   Ok
```

让我们分解一下上面那条命令，动词是`!`\(**过滤操作符**\)，名词是`}`\(到下一个段落\)。**过滤操作符**`!`接受终端命令作为另一个参数，因此我把`column -t -s "|"`传给它。我不想详细描述`column`是如何工作的，但是总之它格式化了文本。

假设你不止想格式化你的文本，还想只展示`Ok`结尾的行，你知道`awk`命令可以做这件事情，那么你可以这样做：

```text
!}column -t -s "|" | awk 'NR > 1 && /Ok/{print $0}'
```

结果如下：

```text
02  Kitten  Ok
03  Bunny   Ok
```

666！管道竟然在Vim中也能起作用。

这就是Vim的结合性的强大之处。你知道的**操作符**，**动作**，终端命令越多，你组建复杂操作的能力成倍增长。

换句话说，假设你只知道四个**动作**：`w, $, }, G`和删除操作符\(`d`\)，你可以做8件事：按四种方式移动\(`w, $, }, G`\)和删除4种文本对象\(`dw, d$, d}, dG`\)。如果有一天你学习了小写变大写的**操作符**\(`gU`\)，你的Vim工具箱中多的不是1种工具，而是4种：`gUw, gU$, gU}, gUG`。现在你的Vim工具箱中就有12种工具了。如果你知道10个**动作**和5个**操作符**，那么你就有60种工具\(50个操作+10个移动\)。另外，行号动作\(`nG`\)给你了`n`种**动作**，其中`n`是你文件中的行数\(例如前往第5行，`5G`\)。搜索动作\(`/`\)实际上给你带来无限数量的**动作**因为你可以搜索任何内容。你知道多少终端命令，外部命令操作符\(`!`\)就给你了多少种过滤工具。使用Vim这种能够组合的工具，所有你知道的东西都可以被串起来完成更复杂的操作。你知道的越多，你就越强大。

这种具有结合性的行为也正符合Unix的哲学：_一个命令做好一件事_。**动作**只需要做一件事：前往X。**操作符**只需要做一件事：完成Y。通过结合一个**操作符**和一个**动作**，你就获得了YX：在X上完成Y。

甚至，**动作**和**操作符**都是可拓展的，你可以自己创造**动作**和**操作符**去丰富你的Vim工具箱，`Vim-textobj-user`有一系列自定义的文本对象。

另外，如果你不知道我刚才使用的`column`和`awk`命令也没有关系，重要的是Vim可以和终端命令很好的结合起来。

## 聪明地学习语法

你刚刚学完Vim唯一的语法规则：

```text
verb + noun
```

我学Vim中最大的"AHA moment"之一是当我刚学完大写命令\(`gU`\)时，想要把一个单词变成大写，我本能的运行了`gUiW`，它居然成功了，我光标所在的单词都大写了。我正是从那是开始理解Vim的。我希望你也会在不久之后有你自己的"AHA moment"，如果之前没有的话。

这一章的目标是向你展现Vim中的`verb+noun`模式，因此之后你就可以像学习一门新的语言一样渐进的学习Vim而不是死记每个命令的组合。

学习这种模式并且理解其中的含义，这是聪明的学习方式。

