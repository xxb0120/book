# ArchGuard Book

架构治理指南与实践

- [ ] 什么是架构
  - [ ] 架构的定义
- [ ] 架构的模型
  - [ ] 架构的风格
  - [ ] 架构的模式
  - [ ] 架构的规范
  - [ ] xxxx
- [ ] 什么是架构治理
  - [ ] 架构治理的定义
  - [ ] 架构治理的模型
- [ ] 什么是架构治理系统
  - [ ] 架构分析
  - [ ] 架构可视化

ArchGuard 相关

- [ ] ArchGuard 插件化
  - [ ] 插件化

# 架构建模：如何用代码描述软件架构


在架构治理平台 ArchGuard 中，为了实现对架构的治理，我们需要**代码** + **模型**描述所要处理的内容和数据。所以，在 ArchGuard 中，我们有了[代码的模型](https://www.phodal.com/blog/modeling-for-code/)、依赖的模型、变更的模型等，剩下的两个核心的部分就是**架构的模型**、**架构的治理模型**，其它的还有诸如[构建的模型](https://www.phodal.com/blog/abstract-build/)等，会在后续的过程中持续引入到系统中。

PS：本文里的架构展开是基于自动化分析需求的，模型也是基于这个动机出发的。

## 架构是什么？？

对单个语言的代码建模并不难，对于一个语言有特别的概念，如 package、class、field、function 等等。在有了明确概念的基础之下，结合我们的业务上的需求，就能构建一个太差不差的模型。在采用 DDD 这一类建模方式的时候，产生共识，提炼知识，形成概念等，便能构建出模型的雏形。

### 起点：架构是重要的元素

然而，对于架构来说，业内没有统一的定义。于是乎，诸如 Martin Fowler 喜欢引用 GoF（《设计模式》作者们） 之一的 Ralph Johnson 对于架构的描述：

> 架构是那些重要的东西……，无论它具体是什么。

同样的 Grady Booch （UML 的发明者之一）也是惟类似的方式来概括架构的：

> 软件架构是系统设计过程中的重要设计决定的集合，可以通过变更成本来衡量每个设计决定的重要维度。

所以呢，这让我们感觉说了等于没说，我们得去定义什么是**重要的东西**。而重要的东西，在不同人、不同场景之下，它是存在差异的。哪怕是同一个类型的软件，在不同的公司、不同的利益相关者的背景之下，重要的东西也尽相同。

### 原则：可是到底哪些是重要的？

于是乎，我再尝试去引用最新的架构相关的书籍，诸如于我编写这篇文章时，参考的书《软件架构：架构模式、特征及实践指南》一书中 Neal Ford 对于架构的定义：

> 软件架构中包含系统的结构、系统必须支持的架构特征、架构决策以及设计原则。**系统的结构**是指实现该系统的一种或多种架构风格（如微服务、分层和微内核等）。**架构特征**定义了系统的成功标准。**架构决策**定义了一组关于如何构建系统的规则。**设计原则**是关于如何构建系统的非必须遵循的指导原则。

从模型构筑的层面来说，书中的定义也提供了一个灵活性。诸如于在架构特征的定义里，关注的是各类能力（ability），如互操作性、可适用性、可测试性等等。

在现有的 ArchGuard 这个业务场景之下，我们难以自动化地识别出各类的特征。因为从实践的层面上来说，这些能力并不一定实现了，它是目标架构，可能还只存在于架构蓝图之上的。在这个层面上来说，这里定义的架构，偏向于是**设计层面**的定义。

从另外一方面来说，架构决策则是在架构治理的过程中，我们所关注的核心。可以在后续针对于这一系列的原则的规则，构建出一个描述架构特征的 DSL。

### 重要的元素：组件、边界与通信

接着，让我们再回到 Bob 大叔（Robert C. Martin）的《架构整洁之道》书中的定义：

> 软件系统的质量是由它的构建者所决定的，软件架构这项工作的实质就是**规划如何将系统切分成组件，并安排好组件之间的关系，以及组件之间互相通信的方式**。

再从 Clean Architecture 模式来说，Bob 大叔一直在强调的是：顶层抽象策略与底层实现要实现解耦。诸如于如何划定合理的边界？如何组合相关的策略与层次？从这个模式上来说，我们得到了一个越来越清晰的定义。

然而，我们还遇到一个更难的问题是，**如何定义一个组件是什么**？\*\*还有关系是什么？\*\*在书里的序言， Kevlin Henney（《面向模式的软件架构》卷4、卷 5的作者之一）给了一个更精确的描述词：**组织结构**（structure），从宏观到微观的构筑过程，其中的构件包含了组件、类、函数、模块、层级、服务等。

对于大型软件来说，其组织结构方式异常复杂，它像极了一个国家的层级关系，一级部门、二级部门等等。而部门之间又有复杂的关系，正是层级关系 + 层级的构件构建成了这个复杂的系统。（PS：而了让系统能良好的运行，即其中的组件（螺丝钉）按规则执行，则需要一个督察组织。）

### 层次结构：组件和关系

软件架构已经有了几十年的历史，我们已经用 ”模式“ 这一词对过去的架构进行了一系列的总结。二十年前，人们初步总结了《面向模式的软件架构》（POSA）。在这里，就引述 POSA 1 的第 6 章里，有一个完整的层级关系介绍：

> **软件架构**描述了软件系统的子系统和组件以及它们之间的关系。通常使用不同的视图来说明子系统和组件，以展示软件系统的功能特征和非功能特征。
>
> **组件**是被封装起来的软件系统的一部分，包含一个接口。组件是用于打造系统的构件。在编程语言层面，组件可能由模块、类、对象或一组相关的函数表示。
>
> **关系**描述了组件之间的联系，可能是静态的，也可能是动态的。静态关系会在源代码中直接显示出来，它们指出了架构中组件的布局；动态关系指出了组件之间 的临时关系和动态交互，可能不容易通过源代码的静态结构看出来。
>
> **视图**呈现软件架构的某个方面，展示软件系统的某些具体特征。
>
> ……

在今天来看，从模式上来说，软件架构本身并没有发生太大的变化。只是呢，一些定义发生了变化，诸如于组件和接口。在微服务架构风格流行的今天，一个微服务也可以视为一个组件，它包含了一系列的接口，对外提供了复用的能力。而用来描述它们的关系的元素，则不再是过去的函数调用，变为了远程调用、事件触发。

现在，我们有了一详尽的定义，在建模上，可能还欠缺一些元素，诸如于，如何分析出**组件间的关系**。

### 第 3 种架构视图：展示工程关注点

在 ArchGuard 中，我们使用了 C4 架构可视化模型作为一种参考视图。这种实现的方式主要是从分析和可视化的层面来考虑的。除了 C4 之外，另外一种主流的方式是 4 + 1 视图。顺带一提，在 4 + 1 的论文《Architectural Blueprints—The “4+1” View Model of Software Architecture》，同样也有一个描述架构的表示公式：`Software architecture = {Elements, Forms, Rationale/Constraints}`。

从通识的角度来看，采用 4 + 1 视图是一个比较理想的方式。只是，由于存在大量的 PaaS、IaaS 等 xx 即服务设计的不合理性，使得这些记录基础设计相关信息的代码，并没有与代码库一起存放，使得在辩识上存在一定的难度。

因此，从实现的层面来说，在这里，我们要引用的是《面向模式的软件架构》中，提到的《Software Architecture in Industrial Applications》（也可以参考《实用软件体系结构》一书）架构视图：

* 概念视图：描述了整个系统需求向整个体系结构的转化。
* 模块视图：描述了如何将系统划分成模块并将模块组织成层。
* 执行视图：描述了系统的动态元素以及它们之间的交互。
* 代码视图：描述了源代码的组织结构。

在这个视图的定义里，它更能清晰的划分开几个不同层面的考虑因素。采用作者们在最早的论文里提到的示例：

| 软件架构 | 使用示例 | 影响因素的例子 |
|----|----|----|
| 代码架构 | 配置管理, 系统构建、OEM 定价 | 编程语言，开发工具和环境，扩展子系统 |
| 模块架构 | 模块接口管控、变更影响分析、接口约束一致性检查、配置管理 | 使能软件技术、组织结构、设计原则 |
| 执行架构 | 性能和可调度性分析，系统的静态和动态配置，将系统移植到不同的执行环境 | 硬件架构、运行时环境性能标准、通信机制 |
| 概念架构 | 使用特定领域的组件和连接器进行设计、性能评估、安全性和可靠性分析、了解系统的静态和动态可配置性 | 应用领域、抽象软件范式、设计方法 |

从表格的右边里，我们就可以直接对应到系统所需要的每个层面的设计因素，诸如于编程语言等元素放在代码架构上。换句话来说，在微服务、单体架构下，都能找到自己合适的位置。

### 概念的最后：描述模型的类型系统

最后，为了保证本文在概念的完整性，我们还需要一种方式来描述这个系统种的模型和一系列的概念，从形式上来说，它是一个类型系统。诸如于，我们在 UML中所表示的 （PlantUML 表示方式）：

```javascript
class Architecture {
   Component[] components
   System[]    subSystems
   Relation[]  relations
   ArchStyle   archStyle
   Rule[]      archRules
   ...
}
```

一个用来描述类型的系统，就是一个类型系统，和编程语言里的类型是等同的。它可以用来解释一系列的概念，以及概念之间如何连接。

顺带一题，如果我们把编程语言看作是一个系统，那么我们就会发现其在设计的有趣之处。类型系统与结构体（或者类）可以用于构建系统中的概念，一个个的表达式则是用于构建概念之间的关系。

## 建模

对于概念来说，哪怕是有了如此多丰富的展开，要做一个小结也并非一件容易的事情。更何况于模型而言，它是数值上的标准化，一种接近于通用的模型形态。

### 设计：第一个版本的架构模型

所以，我的第一个尝试是从 《面向模式的软件架构》的定义下手：

```javascript
class SystemArchitecture(
    val archStyle: ArchitectureStyle,
    val subSystem: List<SubSystem>,
    var components: List<ArchComponent>,
    val connections: List<ArchConnection>
)
```

示例：对于一个系统来说，它存在一个主的架构模式，如微服务架构。而在每个微服务相当于是一个子系统或者说组件。当然了，一个子系统可以包含多个微服务。而对于个组件来说，它包含了输入和输出，以及一系列的计算逻辑。所以，它的一个模型可能是这样的：

```javascript
class ArchComponent(
    val name: String,
    val type: ArchComponentType,
    val inbounds: List<String>,
    val outbound: List<String>,
    val components: List<ArchComponent>
)
```

而麻烦的一点在于，组件是动态的，组件之间是存在关系的，组件内部也存在关系。所以，我们还需要考虑如何表示组件间的关系？

```javascript
class Connection(
    val connectors: String,
    val source: String,
    val target: String,
    var connectionType: ConnectionType,
    val connectorStyles: ConnectorStyle,
)
```

所以，如果以宽泛的定义来看，组件其实是树形结构的。同样的，对于架构来说，这种 Connection 也是存在的。根据上面的一系列形态展开，我们就能得到一个初始版本的架构的架构模型。

更多模型相关内容，详见 ArchGuard Scanner 中的初始版本 [Architecture.kt](https://github.com/archguard/scanner/blob/master/analyser_architecture/src/main/kotlin/org/archguard/architecture/core/WorkspaceArchitecture.kt) 。


### 实现思路：生成架构模型

ArchGuard 中的模型是通过代码来生成的。在这种业务场景之下，在构建模型的时候，需要平衡设计与实现。由此，基于代码分析的视图方式更适用我们。从代码和依赖中，我们可以：

* 基于目录结构分析出分层关系，进而得到代码的结构视图。
* 分析依赖管理工具，进而得到一个模块的视图。
* 分析依赖的软件，如 Spring、Dubbo 等，进而得到一个执行的视图。
* 分析软件中的模型，进而得到概念视图。

结合之下，我们就能构建出一个更完整的架构模型。

### 实现：放弃第一个版本的模型

设计挺美好的，但是当我开始写第一行代码的时候，发现不对劲了：我们有了一个分析的模型，但是输入呢？

仅从项目的分析来说，我们拿到的只是一个代码仓库的代码。一个代码仓库可能是一个模块，一个系统，又或者是一个服务。所以，我们的输入是什么？基于已有的分析情况，如项目依赖（依赖管理工具）、源码结构、语言等？我们缺少构建一个项目所需要的上下文。

我们从源码所能分析到的内容如下所示：

```kotlin
data class PotentialExecArch(
    var protocols: List<String> = listOf(),
    var appTypes: List<String> = listOf(),
    var connectorTypes: List<ConnectorType> = listOf(),
    var coreStacks: List<String> = listOf(),
    var concepts: List<CodeDataStruct> = listOf()
)
```

对应的一些分析细节：

1. 协议信息。解析源码和 Gradle、Maven、NPM 中的依赖信息，如含 Dubbo 就视为带 RPC；如带 `java.io.File` 就认为带 FileIO
2. 应用类型。解析依赖信息，如包含 `clikt` 就视为 Kotlin 的 CLI 应用。
3. 连接类型。同上
4. 核心技术栈。根据依赖类型分析。
5. 概念模型。需要先分析潜在的分层架构模型，根据分层架构模型，就能得到概念集。

当然了， 这部分代码还没写完，如果有兴趣，也欢迎来加入我们。

### 多种架构模型

于是，在 ArchGuard 的背景下，“架构” 的架构模型有多种形态的:

- 扫描形态下的架构模型（对应到 ArchGuard Scanner）。即，从各个项目的源码中得到的架构模型，从源码、依赖、数据库等中计算出来。
- 分析形态下的架构模型（对应到 ArchGuard Backend）。基于分析形态下的架构模型，构建出包含架构相关知识的架构模型。
- 展示形态下的架构模型（对应到 ArchGuard Frontend）。结合可视化的知识，展示出对应的架构模型。

每一个模型都是在上一步的基础上添加了领域知识，那么什么是领域知识呢？如何做好架构？

## 小结：挑战

模型，一个开始，它需要不断地演进才能适用需要。而有了一个凑合能用的模型，只是这一系列工作的开始，我们还会遇到一系列的挑战。

### 描绘目标架构

系统中的架构反应的只是现状，如何去描述未来的架构，并将两者进行匹配，又是一个非常有意思的话题。

### 衡量变化性

我们还将面临的另外一个问题是，软件架构并非是不变的。

软件只要一直在开发，就会以细微地方式变化着。从宏观的层面来说，尽可能架构师都在努力地不去大范围地变动结构。而我们需要面对于这些挑战，诸如于基础设施变化，而这种变化带来的是临时性的中间状态。

如何去表示这种临时性的中间状态，就变得更加有意思。

在这里只开始了迈出了第一步，如果大家有兴趣，欢迎来 ArchGuard 为技术建模：[https://github.com/archguard/archguard/discussions/67](https://github.com/archguard/archguard/discussions/67)

## 参考资料

* 《整洁架构之道 》
* 《架构之美》
* 《软件架构：架构模式、特征及实践指南》
* 《面向模式的软件架构 卷 1：模式系统》
* 《实用软件体系结构》
* 《Software Architecture in Industrial Applications》
* 《Architectural Blueprints—The “4+1” View Model of Software Architecture》




## License

This code is distributed under the Creative Commons Attribution-Noncommercial-No Derivative Works 3.0  License. See `LICENSE` in this directory.

