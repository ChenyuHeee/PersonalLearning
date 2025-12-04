# AIOpsLab：用于评估AI代理实现自主云的整体框架

## 专业中文翻译

> **原论文**: AIOpsLab: A Holistic Framework to Evaluate AI Agents for Enabling Autonomous Clouds
>
> **作者**: Yinfang Chen¹, Manish Shetty², Gagan Somashekar³, Minghua Ma³, Yogesh Simmhan⁴, Jonathan Mace³, Chetan Bansal³, Rujia Wang³, Saravan Rajmohan³
>
> **单位**: ¹伊利诺伊大学厄巴纳-香槟分校 (UIUC), ²加州大学伯克利分校, ³微软研究院, ⁴印度科学研究所
>
> **发表**: arXiv:2501.07606, 2025年1月

---

## 摘要 (Abstract)

**原文翻译：**

AI运维（AIOps，AI for IT Operations）旨在自动化复杂的运维任务，如故障定位和根因分析，以减少人工工作量并最小化对客户的影响。传统的DevOps工具和AIOps算法通常专注于解决孤立的运维任务，而大型语言模型（LLM，Large Language Model）和AI代理的最新进展正在通过实现端到端和多任务自动化来革新AIOps。本文展望了一个未来，在这个未来中，AI代理自主管理整个事件生命周期中的运维任务，从而实现自愈云系统——我们将这种范式称为**AgentOps（代理运维）**。

实现这一愿景需要一个全面的框架来指导这些代理的设计、开发和评估。为此，我们提出了**AIOpsLab**，这是一个不仅能够部署微服务云环境、注入故障、生成工作负载和导出遥测数据的框架，而且还能协调这些组件并提供与代理交互和评估的接口。我们讨论了这种整体框架的关键需求，并展示了AIOpsLab如何促进下一代AIOps代理的评估。通过在AIOpsLab创建的基准测试中评估最先进的LLM代理，我们提供了关于它们在处理云环境中复杂运维任务方面的能力和局限性的深入见解。

---

## 1 引言 (Introduction)

### 1.1 研究背景

IT应用和服务的快速演进导致企业越来越依赖于超大规模的云基础系统。这些系统通常是分布式的，采用微服务（Microservices）和无服务器计算（Serverless Computing）等架构，在实现可扩展性的同时也增加了复杂性并带来了新的运维挑战。在这样的云环境中，问题可能会级联成大规模中断。例如，亚马逊的一次宕机仅在一小时内就可能导致1亿美元的损失。

为了应对这种复杂基础设施中事件管理的挑战，业界正在朝着采用**AIOps（人工智能运维）**的方向发展，这是在**DevOps（开发与运维）**背景下进行的。AIOps的最终目标是创建**自主自愈云**，其中AI驱动的方法可以在最少人工干预的情况下检测、定位和缓解故障。尽管这一概念已经存在十多年，但AIOps和大型语言模型（LLM）代理的最新进展使这一愿景更加接近现实。

LLM代理集成了外部工具，能够动态地与环境交互，使它们能够自主管理整个事件生命周期，如图1所示。

### 1.2 AgentOps：新范式的提出

为了实现这种自主自愈云的愿景，我们提出了一个名为**AgentOps（代理运维，Agent for Operations）**的新范式。在这个范式中，代理方法不限于孤立的运维任务，而是能够无缝管理跨整个运维栈的多个跨层任务。AgentOps代表了一种演进，其中自主代理可以做出实时决策和端到端操作以确保系统可靠性。这与AI的最新进展相一致，正如一篇文章所强调的：

> "最先进的AI结果越来越多地由具有多个组件的复合系统获得，而不仅仅是单体模型...复合AI系统很可能是未来最大化AI结果的最佳方式" ——《从模型到复合AI系统的转变》（Zaharia等人，2024）

AI驱动的工具和基准测试，如WebArena、R2E、HumanEval、LiveCodeBench和SWE-bench，通过加速软件开发显著推进了DevOps的"开发"（Dev）方面。然而，AI在"运维"（Ops）方面的进展，特别是AgentOps，仍然有限，这是由于缺乏多样化、真实场景的高质量基准测试。解决这一差距需要一个能够在交互式环境中帮助AIOps代理设计、开发和评估的框架，这是本文的关键贡献。

### 1.3 挑战与贡献

**挑战：** 构建一个能够让代理与云动态交互的整体基准框架面临多个挑战：

1. **管理评估流程**：需要一个普遍适用于不同代理和云的评估流程，足够强大以通过复杂和真实的运维任务评估代理，并有价值以提供不同的反馈或可观测性，同时具有可扩展性以适应用户的新任务和代理。

2. **缺乏统一工具**：现有工具虽然解决了AIOps评估的个别组件，如可观测性、应用套件和混沌工程，但缺乏支持统一AIOps评估所需的集成。

3. **缺乏真实评估场景**：现有方法通常依赖静态数据集或固定的问答格式，无法捕捉真实云环境的动态、不可预测和演变的特性。

4. **专有服务和数据集**：最近的AgentOps工作使用专有服务和数据集，限制了可重复性。

**我们的贡献：**

- **揭示需求和挑战**：阐明了支持自主AIOps代理设计、开发和评估的整体框架所需的需求和挑战。

- **开发AIOpsLab框架**：不仅能够部署微服务云环境、注入故障、生成工作负载和导出遥测数据，还能协调这些组件并提供代理-云交互接口（Agent-Cloud Interface, ACI）。

- **构建基准测试套件**：利用AIOpsLab框架构建了包含48个问题的基准测试套件，涵盖不同的AIOps任务。

- **详细的性能分析**：通过在AIOpsLab上评估代理，提供了关于代理性能和局限性的详细分析。

- **开源承诺**：将公开发布AIOpsLab。

---

## 2 AIOpsLab

本节讨论AIOpsLab及其组件的设计和实现，如图2所示。

### 2.1 问题定义 (Problem Definition)

为了支持广泛的评估场景（称为**问题**），这些场景在微服务系统中复制真实的事件，我们首先将AIOps问题**P**形式化为一个元组：

**P = ⟨T, C, S⟩**

其中：
- **T（Task，任务）**：代表任务
- **C（Context，上下文）**：代表上下文
- **S（Solution，解决方案）**：代表预期解决方案（oracle）

**任务T** 定义要执行的具体AIOps操作，分为四类：检测、定位、（根因）分析和缓解。我们在表1中定义了这些任务。每种任务类型都与成功标准和评估指标相关联。例如，检测任务使用**检测时间（TTD, Time-to-Detect）**来衡量检测故障所需的时间。

**上下文C** 可以进一步形式化为元组：**C = ⟨E, I⟩**

其中：
- **E（Environment，环境）**：是问题发生的操作环境
- **I（Information，信息）**：是用于向代理描述问题的问题信息

操作环境包括用于生成问题的云服务、故障模型和工作负载模型，这些不与代理共享。问题信息包括服务描述、任务描述和可用API文档等信息，直接与代理共享。它还包括代理在运行时可查询的间接信息（包括在操作环境中观察到的日志、指标和追踪）。

**解决方案S** 是任务的预期结果，用于评估代理的性能。解决方案通常是针对问题和任务特定的，并经过精心设计用于评估。注意，某些问题（如缓解任务）可以通过多种方式解决。在这种情况下，AIOpsLab评估整个系统的一般状态（例如，检查所有服务是否正常运行），而不仅仅是注入故障的目标资源，因为在缓解过程中可能无意中影响了其他服务或资源。

**示例2.1** 考虑在社交网络应用中定位Kubernetes目标端口错误配置的问题。AIOpsLab通过扩展`LocalizationTask`只需几行代码就可以轻松定义这个问题：

```python
from aiopslab import LocalizationTask,
    SocialNetwork, Wrk, VirtFaultInjector

class K8STargetPortMisconfig(LocalizationTask):
    def __init__(self):
        self.app = SocialNetwork()
        self.ans = "user-service"
    
    def start_workload(self):
        wrk = Wrk(rate=100, duration=10)
        wrk.start_workload(url=self.app.frontend_url)
    
    def inject_fault(self):
        inj = VirtFaultInjector(self.app.ns)
        inj.inject([self.ans], "misconfig_k8s")
    
    def eval(self, soln, trace, duration):
        res["TTL"] = duration
        res["success"] = is_exact_match(soln, self.ans)
        return res
```

这里，任务T是故障定位，解决方案S是名为"user-service"的微服务，这也是故障注入目标。上下文C包括社交网络应用、来自AIOpsLab故障库的错误配置故障，以及使用wrk工具的标准工作负载。

### 2.2 协调器 (Orchestrator)

AIOpsLab的**协调器**严格强制代理和服务之间的关注点分离，使用一个定义明确的中心组件——协调器。它提供了一套强大的接口，允许无缝集成和扩展各种系统组件。

#### 2.2.1 代理-云接口 (Agent Cloud Interface, ACI)

协调器的一个关键职责是为代理与云环境交互提供一个定义明确的接口。通常，开发人员使用各种编程（如API、CLI）和用户界面（事件门户、仪表板等）操作云和服务。然而，现有的云接口并非为LLM和代理设计。例如，人类可以可靠地忽略不相关的信息，而这可能会分散代理的注意力并影响性能。

**ACI指定：**
1. 代理可用的有效操作集
2. 服务状态如何作为代理操作的观察反馈给代理

这样，ACI抽象了云环境的复杂性，简化了代理的决策过程。ACI设计为直观且易于使用，具有简洁的API列表，每个都有文档说明，以确保代理能够朝着目标取得有意义的进展。

**AIOpsLab默认提供的一些API包括：**
- `get_logs`：获取日志
- `get_metrics`：获取指标
- `get_traces`：获取追踪
- `exec_shell`：执行shell命令（应用安全策略过滤后）

**示例2.2** 此示例说明了ACI在AIOpsLab中如何定义为代理可使用的API：

```python
class TaskActions:
    def get_traces(ns: str, duration: int = 5) -> str:
        """
        从Jaeger收集服务的追踪数据。
        Args:
            ns (str): K8S命名空间。
            duration (int): 收集追踪的持续时间。
        Returns:
            str: 追踪保存目录的路径。
        """
        trace_api = TraceAPI(ns)
        end_t = datetime.now()
        start_t = end_t - timedelta(duration)
        traces = trace_api.extract_traces(start_t, end_t)
        return trace_api.save_traces(traces)
```

如图所示，ACI将复杂操作封装在简单的API（如`get_traces`）后面。在初始化问题时，协调器自动从这些API中提取文档，作为上下文C提供给代理。在运行时，代理可以通过协调器的特权访问指定对服务的广泛操作（如扩展、重新部署、打补丁）。最后，协调器在每次操作后向代理传达服务状态，包括输出、错误消息和回溯等高质量反馈。

#### 2.2.2 会话接口 (Session Interface)

协调器的另一个关键职责是管理代理和服务的生命周期。我们将协调器实现为一个基于会话的系统，其中为每个代理解决问题的实例创建一个**会话（Session）**。代理注册到协调器，会话通过传递唯一问题标识符的简单API调用开始。AIOpsLab的设计高度灵活，与不断增长的LLM和代理框架空间集成。

我们唯一的要求是代理必须实现一个`get_action`方法，签名如下：
```python
async def get_action(state: str) -> str
```
它接受来自协调器的服务状态作为输入，并返回代理想要执行的下一个操作。注意，这可以是任何现有代理框架的简单包装函数。

**示例2.3** 在这个简化示例中，我们说明了如何将代理载入AIOpsLab：

```python
from aiopslab import Orchestrator

class Agent:
    def __init__(self, prob, instructs, apis):
        self.prompt = self.set_prompt(prob, instructs, apis)
        self.llm = GPT4()
    
    async def get_action(self, state: str) -> str:
        return self.llm.generate(self.prompt + state)

# 初始化协调器
orch = Orchestrator()
pid = "misconfig_app_hotel_res-mitigation-1"
prob_desc, instructs, apis = orch.init_problem(pid)

# 注册并评估代理
agent = Agent(prob_desc, instructs, apis)
orch.register_agent(agent, name="myAgent")
asyncio.run(orch.start_problem(max_steps=10))
```

如图所示，在初始化问题时，协调器共享代理解决问题所需的上下文。然后通过`get_action`轮询代理的下一个操作。

#### 2.2.3 其他接口

**问题初始化器：** 如第2.1节所述，每个问题都定义了包含其操作环境的上下文C。这个环境是问题发生的服务、故障和工作负载条件。这里，协调器部署服务，并使用Helm等基础设施即代码工具为每个问题部署所需的云服务。

为了创建真实的基准场景，协调器与两个实体接口：
1. **工作负载生成器**：AIOpsLab目前使用wrk2工具，支持多种工作负载策略，也可以重放行业工作负载。
2. **故障生成器**：AIOpsLab使用自定义故障库，在系统栈的不同层次（如应用和虚拟化）实例化故障。

**问题评估器：** 最后，协调器在评估代理在问题上的性能方面发挥关键作用。它将代理的解决方案与每个任务特定的预定义成功标准和评估指标进行比较。AIOpsLab支持每个任务的多个默认和通用指标（如检测的检测时间、采取的步骤数以及LLM驱动代理发送给AIOpsLab的token数）。此外，AIOpsLab提供使用LLMs-as-Judges对代理轨迹进行可选的定性评估。除此之外，还运行所有特定于问题的用户定义评估指标。

### 2.3 云服务 (Cloud Services)

AIOpsLab部署实时微服务应用作为云环境。AIOpsLab目前集成了来自**DeathStarBench**的**HotelReservation**和**SocialNetwork**应用。

- **SocialNetwork应用**：有28个微服务，包括Memcached、MongoDB和Redis，共同实现了现实世界社交网络应用的多个功能。
- **HotelReservation应用**：使用Go和gRPC实现，支持酒店推荐和预订等服务。

### 2.4 面向任务的故障库 (Task-oriented Fault Library)

#### 2.4.1 任务分类体系 (Task Taxonomy)

我们提出了一个任务级分类体系（表1），根据事件管理生命周期的不同阶段，按复杂性递增的方式对AIOps代理应完成的任务进行分类。在表1中，级别越高表示任务越难且更有影响力。

**表1：AIOps代理评估的任务分类**

| 级别 | 任务 | 评估重点 |
|------|------|----------|
| Level 1 | **检测 (Detection)** | 该方法能否准确检测异常或偏差？ |
| Level 2 | **定位 (Localization)** | 该方法能否精确定位故障的确切来源（如微服务）？ |
| Level 3 | **根因分析 (Root Cause Analysis, RCA)** | 该方法能否确定故障的根本原因？ |
| Level 4 | **缓解 (Mitigation)** | 该方法能否提供有效的解决方案来恢复环境？ |

- **Level 1**：专注于系统中异常行为的初步识别，例如检测微服务的Kubernetes pod故障。用户也可以定义更复杂的任务或创建子任务。根因分析任务包含系统级别和故障类型预测两个子任务需要解决。

为了在不同任务级别实例化问题，我们使用故障注入将故障注入系统，并为AIOpsLab构建问题池。我们将它们分为两种主要类型：**症状性故障**和**功能性故障**，如图3所示。

#### 2.4.2 症状性故障 (Symptomatic Faults)

症状性故障（如性能下降和崩溃故障）表现为可观察的症状，如延迟增加、资源耗尽或服务中断。这些故障通常帮助构建分类体系中的Level 1和Level 2任务，可以创建评估AIOps方法检测和定位能力的问题。这些故障提供了潜在问题的概览，但不一定揭示更深层的根本原因（因为它们没有根本原因）。AIOpsLab集成了故障注入工具**Chaos-Mesh**来向微服务应用注入症状性故障。

#### 2.4.3 功能性故障 (Functional Faults)

虽然有许多用于测试云系统弹性的故障注入工具，但它们大多只专注于注入系统症状。这些粗粒度的故障只能破坏而不能模拟底层的细粒度根本原因（如错误配置或软件bug），因此无法评估AIOps代理诊断和缓解根本原因的能力。

评估AIOps代理跨任务的失败场景必须超越简单的性能或崩溃故障，反映挑战代理的真实案例，这就是功能性故障发挥作用的地方。**功能性故障**要求方法不仅检测（Level 1）和定位（Level 2）故障，还要诊断根本原因（Level 3）（如不正确的部署或操作），并应用正确的缓解策略（Level 4）。

例如，图4中的故障撤销了地理微服务（Mongodb-geo）的MongoDB数据库的管理员身份验证。由于Geo服务依赖其后端数据库，在调用期间会出现错误。

**示例2.4** 在以下示例中，我们说明了AIOpsLab中撤销身份验证故障的应用级故障注入器结构及其使用示例：

```python
from aiopslab.generators.fault.base import FaultInjector
from aiopslab.service.apps.hotelres import HotelReservation

class ApplicationFaultInjector(FaultInjector):
    def inject_revoke_auth(self, microservices: list[str]):
        """撤销MongoDB管理员权限。"""
        ...
    
    def recover_revoke_auth(self, microservices: list[str]):
        """恢复撤销管理员权限故障。"""
        ...

# 使用示例
class MongoDBRevokeAuth:
    def __init__(self):
        self.app = HotelReservation()
    
    def inject_fault(self):
        injector = ApplicationFaultInjector(ns)
        injector._inject(["mongodb-geo"], "revoke_auth")
```

用户可以使用现有的故障库定义问题。例如，用户可以指定不同的故障服务，甚至构建同时向多个服务注入多个故障的任务。用户也可以自定义故障以生成各种问题。AIOpsLab为其关联的故障场景提供注入功能，并提供相应的缓解机制以从错误状态恢复系统。

**表2：用于在AIOpsLab中实例化评估问题的选定故障**

| 编号 | 名称 | 应用 | 任务级别 | 类别 | 问题数 | 描述 |
|------|------|------|----------|------|--------|------|
| 1 | AuthenticationMissing | HotelReservation | 1,2,3,4 | 功能性/虚拟化 | 4 | 缺少身份验证凭据导致MongoDB访问被拒绝 |
| 2 | TargetPortMisconfig | SocialNetwork | 1,2,3,4 | 功能性/虚拟化 | 12 | 由于错误配置，服务无法连接到指定端口 |
| 3 | RevokeAuth | HotelReservation | 1,2,3,4 | 功能性/应用 | 8 | 撤销的身份验证导致数据库连接失败 |
| 4 | UserUnregistered | HotelReservation | 1,2,3,4 | 功能性/应用 | 8 | 用户注销后数据库服务访问失败 |
| 5 | BuggyAppImage | HotelReservation | 1,2,3,4 | 功能性/应用 | 4 | 应用镜像中的连接代码bug导致访问问题 |
| 6 | ScalePod | SocialNetwork | 1,2,3,4 | 功能性/虚拟化 | 4 | 错误的扩展操作使服务的pod数量变为零 |
| 7 | AssignNonExistentNode | SocialNetwork | 1,2,3,4 | 功能性/虚拟化 | 4 | 由于错误分配到不存在的节点，Pod处于挂起失败状态 |
| 8 | NetworkLoss | HotelReservation | 1,2 | 症状性 | 2 | 网络丢失导致特定服务的通信故障 |
| 9 | PodFailure | HotelReservation | 1,2 | 症状性 | 2 | 由于pod故障导致服务中断 |
| 10 | Noop | HotelReservation/SocialNetwork | 1 | - | 2 | 系统中未注入故障 |

### 2.5 可观测性 (Observability)

AIOpsLab配备了**可扩展的可观测性层**，提供全面的监控能力。AIOpsLab通过其遥测收集器收集广泛的遥测数据，包括：

1. **追踪 (Traces)**：来自Jaeger的追踪，详细描述请求通过分布式系统的端到端路径
2. **日志 (Logs)**：通过Kubectl检索的应用日志，或由Filebeat和Logstash格式化和记录的日志
3. **指标 (Metrics)**：由Prometheus监控的系统指标

AIOpsLab不仅支持在与LLM代理交互期间收集数据，还可以离线导出数据以便于评估其他传统AIOps方法。此外，AIOpsLab设计为从其他维度（如代码库、配置和集群信息）捕获信息。开发人员还可以使用AIOpsLab的接口设计和向代理公开低级系统信息（如系统调用日志）。

---

## 3 评估 (Evaluation)

本节首先概述了AIOpsLab中使用的评估设置和指标。然后深入研究表2中列出的选定故障，这些故障作为AIOpsLab内的多样化评估场景。接下来，我们评估AIOps代理解决这些问题的性能，然后分析代理的成本。我们还深入探讨性能差异背后的原因，以了解挑战和潜在的代理改进。

注意，所有结果都由问题评估器自动收集和记录（第2.2.3节）。

### 3.1 评估设置 (Evaluation Setup)

我们使用AIOpsLab评估了四个基于LLM的代理。注意，为了公平比较，我们在AIOpsLab中注册朴素代理，没有任何微调或修改。

1. **GPT-4-W-SHELL / GPT-3.5-W-SHELL**：使用GPT-4-TURBO和GPT-3.5-TURBO作为基线，只能访问安全shell
2. **ReAct**：扩展链式思维推理，以交错方式集成推理和行动
3. **FLASH**：云运维专用代理，采用工作流自动化系统，监控执行状态并将复杂指令分解为可管理的条件片段，结合事后生成从过去交互中学习

为了与特定任务类型的其他AIOps方法进行比较，我们在AIOpsLab上评估了三种最先进的非LLM AIOps算法，使用（多模态）遥测数据作为输入：
- **MKSMC**：用于检测
- **RMLAD**和**PDiagnose**：用于定位

### 3.2 指标 (Metrics)

**正确性 (Correctness)：** 该指标衡量代理对问题响应的准确性。它评估代理是否按预期成功检测、定位、分析和解决问题。

**时间/步骤 (Time/Steps)：** 这些指标评估AIOps代理在每种任务类型上的效率。例如，**检测时间（TTD, Time-to-Detect）**是从故障发生到检测的时间，**缓解时间（TTM, Time-to-Mitigate）**是从检测到完成故障缓解的时间。还记录解决问题所采取的步骤或操作数量。注意，这是代理与AIOpsLab交互的次数，而不是发送到后端LLM的请求数。

**成本 (Cost)：** 我们使用代理/环境生成的token数量（包括输入token和输出token）作为成本指标。

### 3.3 AIOpsLab基准的问题池 (Problem Pool)

目前，AIOpsLab基准在其问题池中包含**48个问题**。使用六个代理，我们总共评估了**288个案例**。表2列出了用于实例化问题的故障。

如表2所示，所有功能性故障（包括故障1-7）用于在所有四个任务级别创建问题；而症状性故障（包括故障8-9）只能用于在检测和定位级别（Level 1和Level 2）创建问题。

- **检测级别任务**：代理必须实时识别故障的存在。这是一个二元分类任务，代理必须响应"是"（如果存在故障）或"否"（相反）。检测任务（Level 1）可以变得更复杂，例如要求代理标记异常遥测数据；但我们在这里保持简单，将复杂任务留给其他级别。

- **定位任务（Level 2）**：要求代理指定故障的确切位置，通常是Kubernetes中的服务或pod名称。

- **RCA任务（Level 3）**：要求代理识别(1)故障影响的系统层和(2)故障类型（如错误配置或操作错误）。

- **缓解任务（Level 4）**：要求代理与环境交互，通过一系列操作修复故障，如更新配置或回滚到先前版本等。

大多数故障使用户能够通过将故障注入其他目标（如服务）轻松扩展和创建新问题。例如，AIOpsLab中的故障2可以通过简单配置注入目标注入到10个服务中。

### 3.4 性能结果 (Performance Results)

代理的整体性能总结在表3中，任务特定结果在表4中。如表3所示，**FLASH在所有代理中达到最高准确率**。虽然GPT-3.5-TURBO完成任务最快，但其准确率最低，仅为15.25%。

**表3：不同代理的整体性能**

| 代理 | 代码行数 | 时间(s) | 步骤数 | Tokens | 准确率 |
|------|----------|---------|--------|--------|--------|
| GPT-4-W-SHELL | 41 | 28.61 | 6.44 | 6,394.5 | 49.15% |
| GPT-3.5-W-SHELL | 41 | 12.44 | 14.70 | 2,557.95 | 15.25% |
| ReAct | 49 | 43.79 | 11.50 | 16,941.46 | 55.93% |
| FLASH | 60 | 99.64 | 8.48 | 6,484.25 | 59.32% |

**表4：各任务的代理性能**

**(a) 检测任务**

| 代理 | 准确率 | 时间(s) | 步骤数 | 输入 | 输出 |
|------|--------|---------|--------|------|------|
| GPT-4-W-SHELL | 69.23% | 7.08 | 3.85 | 5,492 | 132 |
| GPT-3.5-W-SHELL | 23.07% | 11.05 | 13.60 | 1,940.44 | 385.56 |
| ReAct | 76.92% | 39.00 | 11.46 | 15,608.08 | 933.15 |
| FLASH | **100%** | 78.27 | 6.77 | 12,869.08 | 125.69 |
| MKSMC | 15.38% | 1.00 | N/A | N/A | N/A |

**(b) 定位任务**

| 代理 | Acc.@3 | Acc.@1 | 时间(s) | 步骤数 |
|------|--------|--------|---------|--------|
| GPT-4-W-SHELL | 61.54% | 61.54% | 7.04 | 4.23 |
| GPT-3.5-W-SHELL | 30.77% | 30.77% | 6.26 | 11.92 |
| ReAct | **69.23%** | 53.85%↓ | 38.65 | 11.08 |
| FLASH | 61.54% | 46.15%↓ | 56.60 | 5.77 |
| PDiagnose | 15.38% | 15.38% | 1.02 | N/A |
| RMLAD | 7.69% | 7.69% | 1.98 | N/A |

**(c) 根因分析任务**

| 代理 | 准确率 | 时间(s) | 步骤数 |
|------|--------|---------|--------|
| GPT-4-W-SHELL | 40.90% | 8.68 | 4.81 |
| GPT-3.5-W-SHELL | 9.09% | 10.06 | 14.00 |
| ReAct | **45.45%** | 32.16 | 8.00 |
| FLASH | 36.36% | 59.00 | 6.09 |

**(d) 缓解任务**

| 代理 | 准确率 | 时间(s) | 步骤数 |
|------|--------|---------|--------|
| GPT-4-W-SHELL | 27.27% | 99.47 | 13.72 |
| GPT-3.5-W-SHELL | 0% | 23.78 | 20.00 |
| ReAct | 36.36% | 67.18 | 15.54 |
| FLASH | **54.55%** | 216.41 | 16.09 |

**关键发现：**

- **检测任务**：作为二选一问题，应该是最简单的任务，也是AIOps代理执行的第一步。然而，只有FLASH正确回答了所有检测问题。

- **定位任务**：代理可以给出潜在故障服务列表作为答案。ReAct在使用前3个答案评估时表现最好，但当只考虑前1个答案时准确率下降。

- **RCA和缓解任务**：对代理来说是最具挑战性的。GPT-3.5-W-SHELL在缓解尝试中未能恢复任何故障。

**问题难度因任务级别而异。** 尽管在处理真实运维任务方面显示出前景，但没有代理在AIOpsLab基准的四个任务类别中始终达到高问题解决准确率。即使是表现最好的代理（如FLASH）也表现出局限性，特别是在处理更复杂的任务（如缓解）时。

### 3.5 步骤限制的影响 (Influence of the Step Limit)

我们检查了允许的最大步骤数对代理性能的影响（图5）。步骤限制显著影响某些代理的性能：

- **ReAct和FLASH**：随着步骤增加显示出改进的准确率，FLASH在步骤限制设置为20时达到最高准确率59.32%。
- **GPT-3.5-TURBO**：将步骤限制增加到5以上不会产生更好的性能，只会增加token消耗。

值得注意的是，在一定步骤数后准确率趋于平稳，表明对于AIOps问题，带有环境反馈的自我修复可以很快饱和。相反，在开发任务（如代码生成）中，通过各种组合工具（如linter、类型检查器和测试用例）的反馈帮助代理持续改进。

这表明需要：
1. 使用规划对AIOps问题进行更好的任务分解
2. 改进中间步骤的反馈机制
3. 超越环境反馈和自我修复的解决方案

### 3.6 代理行为：好的、坏的和差距 (Agent Behavior: The Good, the Bad and the Gaps)

我们现在深入研究代理的行为，分析好的方面、挑战和改进机会。

**好的方面：**
- 在表4中，我们看到所有代理在检测和定位任务的问题上都比传统非LLM AIOps方法表现更好。
- `get_logs` API是所有代理中使用最频繁的API，其次是`get_metrics`和`get_traces`。然而，代理在API使用模式上也有所不同。例如，FLASH根本不使用`get_traces` API。

**代理失败的原因和模式分析：**

1. **在不必要的操作上浪费步骤**
   - 重复调用同一API
   - 生成不存在的API
   - 在多代理通信中花费过多步骤

2. **数据消费时的信息过载**
   - 代理有时使用`cat`直接消费收集的数据作为其下一步。由于数据通常很大，这会溢出LLM的输入上下文窗口并损害其性能。
   - 代理需要更好地处理大量遥测数据，因为直接消费会损害效率和性能。
   - 获取指标也很棘手，因为代理会收到大量数字，难以直接解释。

3. **无效的API使用**
   - GPT-3.5-W-SHELL经常生成格式错误的命令并重复错误
   - ReAct能通过推理纠正错误，表现出更好的自我修复能力

4. **假阳性检测问题**
   - 只有GPT-4-W-SHELL能正确识别正常系统执行
   - 其他代理将正常活动误报为故障

---

## 缩写术语对照表 (Abbreviations Glossary)

以下是论文中出现的重要缩写及其中文解释：

### 核心技术术语

| 缩写 | 英文全称 | 中文翻译 | 详细解释 |
|------|----------|----------|----------|
| **AIOps** | Artificial Intelligence for IT Operations | 智能运维/AI运维 | 将人工智能技术应用于IT运维领域，自动化复杂的运维任务 |
| **DevOps** | Development and Operations | 开发与运维 | 一种软件开发方法，强调开发团队和运维团队之间的沟通与协作 |
| **AgentOps** | Agent for Operations | 代理运维 | 本文提出的新范式，使用AI代理自主管理运维任务 |
| **LLM** | Large Language Model | 大型语言模型 | 基于深度学习的自然语言处理模型，如GPT-4 |
| **ACI** | Agent-Cloud Interface | 代理-云接口 | AIOpsLab中代理与云环境交互的统一接口 |
| **RCA** | Root Cause Analysis | 根因分析 | 确定问题根本原因的分析方法 |

### 任务与评估相关

| 缩写 | 英文全称 | 中文翻译 | 详细解释 |
|------|----------|----------|----------|
| **TTD** | Time-to-Detect | 检测时间 | 从故障发生到被检测的时间 |
| **TTM** | Time-to-Mitigate | 缓解时间 | 从检测到完成故障缓解的时间 |
| **TTL** | Time-to-Localize | 定位时间 | 定位故障所需的时间 |
| **LoC** | Lines of Code | 代码行数 | 程序代码的行数，用于衡量代码复杂度 |

### 云原生与容器技术

| 缩写 | 英文全称 | 中文翻译 | 详细解释 |
|------|----------|----------|----------|
| **K8S** | Kubernetes | Kubernetes | 开源的容器编排平台，用于自动化部署、扩展和管理容器化应用 |
| **Pod** | Pod | Pod（容器组） | Kubernetes中可部署的最小计算单元，包含一个或多个容器 |
| **API** | Application Programming Interface | 应用程序编程接口 | 软件组件之间交互的接口定义 |
| **CLI** | Command-Line Interface | 命令行界面 | 通过文本命令与计算机程序交互的界面 |
| **TLS** | Transport Layer Security | 传输层安全协议 | 用于在网络通信中提供安全性的加密协议 |

### 可观测性相关

| 缩写 | 英文全称 | 中文翻译 | 详细解释 |
|------|----------|----------|----------|
| **Traces** | Traces | 追踪/链路追踪 | 分布式系统中请求的端到端路径记录 |
| **Logs** | Logs | 日志 | 系统运行过程中产生的事件记录 |
| **Metrics** | Metrics | 指标 | 可量化的系统性能测量数据 |
| **KPI** | Key Performance Indicator | 关键性能指标 | 用于评估目标达成程度的量化指标 |

### 代理框架与方法

| 缩写 | 英文全称 | 中文翻译 | 详细解释 |
|------|----------|----------|----------|
| **ReAct** | Reasoning + Acting | 推理与行动 | 一种将推理和行动以交错方式结合的代理框架 |
| **CoT** | Chain-of-Thought | 链式思维 | 让LLM展示推理过程的提示技术 |
| **FLASH** | - | FLASH | 微软开发的工作流自动化代理，用于诊断重复事件 |
| **RAG** | Retrieval-Augmented Generation | 检索增强生成 | 结合检索和生成的AI技术 |

### 工具与平台

| 名称 | 中文翻译 | 详细解释 |
|------|----------|----------|
| **Jaeger** | 耶格 | 开源的端到端分布式追踪系统 |
| **Prometheus** | 普罗米修斯 | 开源的系统监控和警报工具 |
| **Chaos-Mesh** | 混沌网格 | Kubernetes原生的混沌工程平台 |
| **Helm** | Helm | Kubernetes的包管理器 |
| **Filebeat** | Filebeat | 轻量级日志收集器 |
| **Logstash** | Logstash | 服务器端数据处理管道 |
| **gRPC** | gRPC | 高性能、开源的远程过程调用框架 |
| **MongoDB** | MongoDB | 面向文档的NoSQL数据库 |
| **Memcached** | Memcached | 分布式内存对象缓存系统 |
| **Redis** | Redis | 开源的内存数据结构存储系统 |

### 微服务应用

| 名称 | 中文翻译 | 详细解释 |
|------|----------|----------|
| **DeathStarBench** | 死星基准测试套件 | 用于微服务研究的开源基准测试套件 |
| **SocialNetwork** | 社交网络 | DeathStarBench中的社交网络应用，有28个微服务 |
| **HotelReservation** | 酒店预订 | DeathStarBench中的酒店预订应用 |

### 学术术语

| 缩写 | 英文全称 | 中文翻译 | 详细解释 |
|------|----------|----------|----------|
| **SOTA** | State-of-the-Art | 最先进的 | 指某领域当前最先进的技术或方法 |
| **N/A** | Not Applicable | 不适用 | 表示某项数据或指标不适用于当前情况 |
| **Acc.** | Accuracy | 准确率 | 正确预测数占总预测数的比例 |

---

## 相关工作与参考文献

### 主要引用论文

1. **ReAct** (Yao et al., 2023): "ReAct: Synergizing Reasoning and Acting in Language Models" - ICLR 2023
2. **Chain-of-Thought** (Wei et al., 2022): "Chain of thought prompting elicits reasoning in large language models" - NeurIPS 2022
3. **SWE-bench** (Jimenez et al., 2024): 用于评估LLM解决实际GitHub问题的基准
4. **RCAgent** (Wang et al., 2023): 使用工具增强LLM进行云根因分析的自主代理
5. **FLASH** (Zhang et al., 2024b): 用于诊断重复事件的工作流自动化代理

### 相关会议与期刊
- **ICLR**: International Conference on Learning Representations（国际学习表征会议）
- **NeurIPS**: Neural Information Processing Systems（神经信息处理系统）
- **OSDI**: USENIX Symposium on Operating Systems Design and Implementation（操作系统设计与实现研讨会）
- **SIGKDD**: ACM SIGKDD Conference on Knowledge Discovery and Data Mining（知识发现与数据挖掘会议）
- **FSE**: ACM SIGSOFT International Symposium on Foundations of Software Engineering（软件工程基础国际研讨会）

---

*本翻译文档由学习助手整理，供学术研究和学习参考使用*

*最后更新时间：2025年12月*
