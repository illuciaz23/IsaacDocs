---
tags:
  - 教程
---

# [教程] 良好代码练习

### 什么是微观优化？我应该优化我的MOD吗？ {: .subHeader}

#### 定义

作为程序员，我们经常关心程序的运行速度如何。

当新手程序员开始考虑“性能”时，他们可能会对代码进行糟糕的调整，希望它能加快速度。这些被称为[微观优化（或过早优化）](https://wiki.c2.com/?PrematureOptimization)。例如，新手可能会从一些整齐地组织成单独函数的代码开始，如下所示：


```lua
local function main()
  -- 使用foo做点什么
  foo()

  -- 使用bar做点什么
  bar()
end

local function foo()
  -- TODO
end

local function bar()
  -- TODO
end
```

在上面的代码中，我们有两个小函数，命名良好（理论上），易于阅读和理解。但是新手可能会想把代码转换成这样：

```lua
local function main()
  -- 使用foo做点什么
  -- TODO

  -- 使用bar做点什么
  -- TODO
end
```

这里的想法是，既然我们去掉了两个函数调用，那么程序在理论上应该会加快速度。（因为在后台，函数在被调用时所做的是将值放在堆栈上，然后在完成后将它们从堆栈中弹出。）

但实际上，编译器通常可以优化代码以自动执行这种加速（而无需程序员实际修改其源代码）。因此，在这种情况下，新手程序员获得了运行效率完全一样的字节码，代价是源代码变得更糟。太差劲了！

此外，即使编译器没有自动优化函数调用，调用函数的简单操作也可以在几纳秒内完成。你永远无法有意义地衡量程序的运行时性能中，使用一些额外的函数调用所产生的区别。所以还是和以前一样：新手程序员正在使他们的源代码变得更糟，而没有可衡量的好处。

微观优化是许多新手陷入的陷阱。花在执行微观优化上的时间应该花在测量代码中的实际瓶颈上，然后对这些瓶颈进行优化。或者修复真正的bug！或者添加真正的功能！

这就是为什么程序员有这样一句格言：“过早的优化是万恶之源。”，出自[唐纳德·克努特](https://en.wikipedia.org/wiki/Donald_Knuth)，是有史以来最著名的计算机科学家之一。在他的论文“[Structured Programming with go to Statements](https://pic.plover.com/knuth-GOTO.pdf)”中，他写了这样一句著名的话：

> 程序员浪费了大量的时间去考虑或担心程序中非关键部分的速度，而当考虑到调试和维护时，这些对效率的尝试实际上会产生强烈的负面影响。我们应该忘记这种微小的效率，比如说因为过早优化而浪费的大约97%的时间。然而，我们不应该放弃那关键的 3% 的机会

#### 测量性能

在上面的例子中，新手认为删除函数调用会加快程序的速度。但这些假设可以是关于任何类型的代码，而不仅仅是函数调用。您可能“知道”以某种方式进行编码会比以另一种方式更快。

但在实际生活中的程序中，要预测哪种代码形式实际上会影响程序的性能是**极其困难**的。有时，你可以做出一个你认为会加快程序的变更，但却会让程序变慢！有时，你可以做出一个你认为会让程序变慢的变更，但它却加快了速度！编译器一直在引擎盖下做各种摸不着头脑的事情。

这就是为什么当我们谈论优化时，最需要讨论的是**测量性能**。测量一段代码的运行时间的行为叫做性能分析。（也可以称为基准测试。）

记住来自于C2 wiki的**[优化的三条准则](https://wiki.c2.com/?RulesOfOptimization)**：

1. 别这么做。
2. 现在先别这么做。
3. 优化前先做性能分析。

这3条规则背后的理念是，在实际生活中的程序中，你几乎从不需要优化。但如果你真的这么做了，你**必须**在之前和之后都测量性能。根据你的衡量标准，它会告诉你代码更改是否值得让代码变得更长、更复杂或更难理解。有时候，这是值得的，但往往不会。

#### 代码清晰度

那么，如果你一般不应该关心性能，你应该关心什么呢？答案是代码清晰度。

首先也是最重要的一点，代码的目标是使其简洁易读。（即使你正在编写的代码永远不会被其他人阅读，你仍然应该让它整洁易读，以供未来的你阅读，因为你可能需要在几个月或几年后阅读这些代码，并且必须弄清楚它能做什么来修复一些错误。）

您可能会认为将“代码清晰度”列为比拥有“能跑的代码”更重要这个做法很蠢。这当然有争议。但考虑一下：

- 不能跑但易于理解的代码可以被修改，让它能跑。
- 能跑但无法破译的代码将很难修改。这意味着我们可能无法修复任何错误或添加新功能。

[吉多·范罗苏姆](https://en.wikipedia.org/wiki/Guido_van_Rossum)，编程语言[Python](https://www.python.org/)的创建者，他的关键见解之一就是[代码读起来比写起来频繁得多](https://www.python.org/dev/peps/pep-0008/)。Python被设计为简洁、干净和可读的语言，它有标准的做事方式，并建议每个人都遵循[PEP-8编码标准](https://www.python.org/dev/peps/pep-0008/)。现在，Python是[世界上最流行的编程语言](https://pypl.github.io/PYPL.html)。代码的可读性并不是Python崛起的*唯一*原因，但它仍是重要的原因之一。

### 什么是单行职责(SLR)？ {: .subHeader}

在编写代码时，要努力使其看起来美观，便于他人阅读，尤其是当你向他人展示或寻求帮助时。在这种情况下，遵循“单行职责”规则是一个好主意，这意味着**一行代码**应该只做**一件事**。阅读[此博客](https://midu.dev/single-line-responsability-haz-una-cosa-por-linea/)了解更多细节，你会明白为什么SLR伟大。