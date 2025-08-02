---

title:  程序分析：方法与技术概览
icon:   search
author: 776styjsu
index:  true
order: 1
---

<Catalog />

## 1. 引言与动机
假设你写了一个程序。你能知道关于它的什么信息呢？一个程序分析工具可以回答诸如以下的问题：

- *我的程序是否实现了其规范？或者说，我的程序是正确的吗？*
- *给定的程序是做什么的？*
- *代码补全时，IDE 应该建议什么方法？*
- *一个方法的最佳名称是什么？*
- *哪些代码行最可能有缺陷？*
- *当更改一行代码时，哪些其他行可能也需要更改？*

在软件开发和维护过程中，程序分析对于保障安全、确保正确性、提升性能以及降低成本至关重要。

## 2. 研究问题
通过学习程序分析领域的研究技术和思想，可以部分回答以下问题：

- *关于一个程序，我能知道什么？*
- *我如何才能知道它？*

## 3. 基本概念

程序分析（Program analysis）是计算机科学的一个领域，旨在自动化地获取关于程序的知识，例如程序的正确性、健壮性、安全性和活跃性等特征。
程序分析可以分为静态分析和动态分析。静态分析检查程序的代码，但它不运行程序（例：类型检查和编译）。动态分析观察程序的执行情况（例：测试和性能分析）。

关于程序的大多数问题，既可以通过静态分析也可以通过动态分析来回答——有时甚至可以使用相同的算法。静态分析和动态分析有不同的权衡取舍。关于两者的权衡取舍与内在关联，该文献对此进行了简要讨论（[Static and dynamic analysis: synergy and duality](https://homes.cs.washington.edu/~mernst/pubs/staticdynamic-woda2003.pdf)）。

## 4. 分析方法
一些程序分析的主题包括：
- **抽象解释**（Abstract interpretation），也称为**数据流分析**（dataflow analysis）或**符号执行**（symbolic execution），是经典的静态分析。
- **类型系统**（Type systems），其任务包括类型检查（type checking）、类型推断（type inference）以及将类型应用于非标准问题，例如别名分析（aliasing）。
- **验证**（Verification），其提供关于程序所有可能执行的证明，在关键任务场景中是必需的。
- **模型检测**（Model checking），即对某种可能性空间的穷尽式探索（exhaustive exploration）；这些可能性可以通过显式（explicitly） 或符号化（symbolically） 的方式跟踪记录，一个关键挑战是抽象化（abstraction）。
- **测试**（Testing），其任务包括测试生成（输入和预言/测试预期结果（oracles））、测试用例选择（selection）和测试用例优先级排序（prioritization）。
- 其他动态分析，例如**性能分析**（profiling）。
- **调试**（Debugging）。
- **重构**（Refactoring）。
- **分析后端**（Analysis back ends） 与 **决策过程**（Decision procedures）：有时解决程序分析任务的最佳方法是将其转换为其他形式，并利用现有的**求解器**（solver）。

---
（本文主要基于[Note on Program Analysis](https://homes.cs.washington.edu/~mernst/pubs/program-analysis-book.pdf)翻译而来，内容有少量调整）