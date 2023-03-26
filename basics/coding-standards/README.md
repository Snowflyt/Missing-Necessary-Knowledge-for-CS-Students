# 代码规范

> Quick & dirty. Dijkstra would not like it. — Edsger W. Dijkstra

在著名计算机科学家 Dijkstra 的一篇文章 EWD 1213 中，他在最后写下了下面这段话：

> 我的意思是，如果 10 年之后，当你正在以“快且脏”的方式做某件事时，你突然想象我正在看着你的肩膀，并对你自己说
>
> “Dijkstra 不会喜欢这个。”
>
> 我想，这对我来说已经足被称为“不朽”了。
>
> I mean, if 10 years from now, when you are doing something quick and dirty, you suddenly visualize that I am looking over your shoulders and say to yourself "Dijkstra would not have liked this.", well, that would be enough immortality for me.

Dijkstra 一生致力于以简洁与可证明的方式推动计算机科学的发展。他率先提出不应该在程序中使用 GOTO 语句，并提出了著名的 Dijkstra 最短路算法，同时也是操作系统中互斥、死锁、信号量等概念的提出者。

在编写任何代码时，我们都该如此——以结构化、简洁与易复用的方式编写代码，尽可能避免“快且脏”的代码。而“**代码规范（Coding Standards）**”，作为前人在数年或数十年过程中为写出这样的代码而总结出的经验，毫无疑问是我们应该尽力去遵守的。
