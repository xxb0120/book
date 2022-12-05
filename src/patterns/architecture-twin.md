# 架构孪生 
> 架构孪生是一种旨在精确反映架构设计、实现与运行态等的虚拟模型，以数字化的形式对软件的架构、代码模型、分层、实现技术等的进行动态的呈现。

PS：我们本无意于创造一个新的概念，只是在探索如何更好的度量架构的过程中，发现借用于已有的成熟模式，可以让这件事情变得更简单。

治理架构时，我们（[ArchGuard](https://archguard.org/) 开发团队）一直推荐三态治理的方式，即设计态、实现态和运行态，以全面  + 数字化的方式来思考问题。在过去版本的 ArchGuard 中 ，我们一直致力于实现态的治理，通过对代码的静态分析，结合规则和 Linter 来识别和治理问题；而在 Feakin 中，探索的是如何构建设计架构 DSL 与实现的双向绑定，以此来进一步减少架构中的设计问题。那么，现在探索来到了运行态，我们又应该如何将它与现有的**设计态和运行态相结合**呢？所以，构建架构的数字化形式，便是我们所考虑的一种方案之一。

也因此，我们在这篇文章里分享一下，我们尚在验证、还不成熟的思考。

## 引子 1：生命周期中的反馈回路

我们做架构设计审查、代码度量等的目的是：掌握现有系统的设计与实现，**以有序引导架构演进的方向**。所以，我们围绕于生命周期的**设计态**、**实现态**与**运行态**，以持续架构设计的反馈回路，即由输出端的变化来调整输入端。所以，我们围绕于三态：

* 设计态：目标架构。通过 DSL（领域特定语言） + 架构工作台来构建 。
* 开发态：实现架构。关注于：可视化 + 自定义分析  + 架构治理。
* 运行态：运行架构。结合  APM 工具，构建完整的分析链。

我们应该预期这样一些反馈回路：

* 设计 DSL 与开发态的代码实现绑定。可以反应原来的设计 DSL 是否原理，代码实现是否能与原先设计一致？如果不能那么问题出在哪里？
* 设计态的服务蓝图与开发态的服务地图（ArchGuard 静态分析）、运行态的调用链路相绑定。可以反应与外部系统的设计和交互是否合理？是否符合当初的预期？
* ……

而在构建反馈回路的要点是：对于架构进行量化。

## 引子 2：架构能准确描述吗？

量化架构是一件困难的事，难度不在于代码和架构的模型本身。如果只从标准的架构定义来说，在先前的文章《[如何为代码建模？](https://www.phodal.com/blog/modeling-for-code/)》、《[为“架构”再建个模：如何用代码描述软件架构？](https://www.phodal.com/blog/architecture-model/)》介绍了代码的模型和架构的模型。但是，但是，我们都知道架构师是有无限的责任边界的，你要考虑所有不可能考虑到的底层问题，代码规范、数据库规范、异常处理机制等等。

既然如此，架构师的架构边界在哪里？如果我们的职责超脱了狭义的架构本身，那么我们描述架构的方式是否应该改变？

所以，我们需要寻找 “动态” （适用于不同组织）的架构模型，以及 “动态” 的治理模型。那么，答案是：

* 架构的数字孪生
* 架构适应度函数

足够的自定义模型的空间可以让问题变得简单起来。

## 架构孪生：数字化架构的虚拟模型

回到我们参考于数字孪生的架构孪生定义上：

> 架构孪生是一种旨在精确反映架构设计、实现与运行态等的虚拟模型，以数字化的形式对软件的架构、代码模型、分层、实现技术等的进行动态的呈现。还能允许在每个阶段模拟新代码和设计带来的变化，以持续优化架构。

由此，架构模型是可持续建设和运营中使用的架构孪生策略的关键组成部分。只是呢，为了实现这样的技术，我们需要面对几个挑战：

* 架构模型因子
* 生命周期跟踪与反馈
* 虚拟化演进路径

除此，还有一个非常有意思的问题依旧在考虑中：**模拟态**。如何针对于新的需求，动态模拟软件的架构演进，以发现潜在的架构瓶颈？

值得一提的是，模型的可视化是数字孪生的一个难题。而在架构侧，Codecity 是一个非常不错的可视化解决方案。

### 架构模型因子

如何描述架构模型呢？只需要寻找我们所需要的架构因子，便能构成我们的架构模型，再将其虚拟化。

在构建数字孪生时，我们会给研究对象（例如，风力涡轮机）配备与重要功能方面相关的各种传感器。同样的在构建架构孪生时，我们也需要一系列的 "传感器" 来测量研究对象的各种属性。对照之下，我们也可以定义三种形态的 "传感器" ：

* 设计时 - 架构描述性 “传感器”：描述性传感器用于描述架构的功能、特性和行为。
* 开发时 - 静态量化 “传感器”：静态量化传感器用于描述架构的实现技术。
* 运行时 - 探针（probe）：探针用于描述架构的运行时行为。

那么，接下来就是确定一一个详细的架构因子，并将他们组合在一起，构建出自己的适应度函数。在实现上，每一个架构因子可以对应于一个或者多个构建任务，这便是我们想构建  ArchGuard CodeDB 以支撑种类繁多的架构因子。

### 生命周期跟踪与反馈

如我们在引子 1 所描述，我们应该跟踪于生命周期中的架构因子，并根据已有的模式和规则为构建反馈回路。

* 可视化架构模型。类似于 Codecity
* 内建规则以提供反馈。
* 构建专家系统。

从孪生的角度来讲，难点是相似的：提供反馈与告警机制。

### 虚拟化演进路径

在这一部分，我们想的是解决一个问题：**如何体现你的架构未来会有问题？**

我们经常听别人说到：“你的架构不具备可伸缩性”，那么如何通过孪生来体现呢？既然，只有带有回路反馈的全生命跟踪，才是真正的全生命周期概念。那么，在拿到足够的数据时，我们是不是能模拟架构的未来演进。诸如于，根据历史的变化趋势，能否发现未来的瓶颈点？

譬如说，将模拟函数施加到系统之上，看出可视化模型的变化，进而构建基于现有系统的反馈，借此调整系统的设计。

## 其它

假设，我们将系统看作是一个个的生命体，那么理论上通过参数化，可以构建一个类 “康威生命游戏” ，便能监督系统的演进。对于 AI 写代码来说，我们只需要设定好输入和输出的，AI 应该帮我们写好程序。对于架构而言，我们只需要设定好架构，AI 理应生成对应的应用。

我们在构建 CodeDB，以承载更多的架构因子：<https://github.com/archguard/codedb-poc>，也欢迎一起探讨架构孪生的可能性。
