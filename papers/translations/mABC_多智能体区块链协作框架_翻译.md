# MABC: 微服务架构中基于区块链启发的多智能体协作根因分析

> **原文标题**: MABC: Multi-Agent Blockchain-inspired Collaboration for Root Cause Analysis in Micro-Services Architecture
>
> **来源**: Findings of the Association for Computational Linguistics: EMNLP 2024
>
> **作者**: Wei Zhang, Hongcheng Guo, Jian Yang, Zhoujin Tian, Yi Zhang, Chaoran Yan, Zhoujun Li, Tongliang Li, Xu Shi, Liangfan Zheng, Bo Zhang
>
> **机构**: 北京航空航天大学复杂与关键软件环境国家重点实验室、北京信息科技大学、Cloudwise Research

---

## 摘要

微服务架构（MSA）中的根因分析（RCA）由于故障传播和节点间的循环依赖，在维护系统稳定性和效率方面面临复杂挑战。多样化的根因分析故障需要具有不同专业知识的多智能体。为了缓解大型语言模型（LLMs）的幻觉问题，我们设计了区块链启发的投票机制，通过去中心化决策过程确保分析的可靠性。为了避免MSA中常见的循环依赖导致的非终止循环，我们通过Agent Workflow客观地限制步骤数并标准化任务处理。

我们提出了一个开创性框架——**MABC**（微服务架构中基于区块链启发的多智能体协作根因分析），其中基于强大LLMs的多个智能体遵循Agent Workflow并在区块链启发的投票中协作。具体来说，从Agent Workflow派生的七个专业化智能体基于其专业知识和LLMs的内在软件知识，在去中心化链中协作，为根因分析提供有价值的见解。

我们在AIOps挑战数据集和新创建的Train-Ticket数据集上的实验表明，在识别根因和生成有效解决方案方面具有卓越性能。消融研究进一步强调了Agent Workflow、多智能体和区块链启发投票对于实现最佳性能至关重要。

**代码和数据集**: https://github.com/knediny/mABC

---

## 1. 引言

微服务架构（MSA）将应用程序分解为一系列独立节点，通过轻量级通信机制进行交互。维护MSA的关键组件是根因分析（RCA），旨在找到告警事件的根本原因并增强系统稳健性，在避免数据泄漏和程序故障分析中起着重要作用。

与只包含一个中央服务的传统架构相比，微服务架构中的RCA变得极其困难，因为故障在服务节点之间持续传播，告警变得越来越复杂。如图1所示，告警事件发生在节点A，而告警事件的根因节点是I，故障传播路径为I→G→D→A。RCA通过追溯到源节点甚至进一步分析指标来识别故障的根本原因（此处为I）。

### 现有方法的局限性

现有方法如TraceAnomaly、MEPFL缺乏处理循环依赖（如H→E→L→H）的机制，并且严重依赖监督训练过程。虽然RCA-Copilot、RCAgent和D-Bot通过事件匹配和信息聚合改进了RCA工具，但它们在处理幻觉问题和MSA中常见的跨节点故障方面仍存在困难。

### MABC的解决方案

为解决上述问题，我们引入MABC，一个革命性的RCA框架：

1. **多智能体解决跨节点故障**: 引入具有多样化专业知识和丰富软件知识的多智能体，分析广泛的数据并导航节点依赖关系
2. **区块链启发投票缓解幻觉**: 使用多智能体协作和社区投票确保高质量内容
3. **Agent Workflow解决循环依赖**: 客观限制步骤数并标准化任务处理

---

## 2. 方法论

### 2.1 概述

MABC引入七个智能体：
- **Alert Receiver（告警接收器）**
- **Process Scheduler（流程调度器）**
- **Data Detective（数据侦探）**
- **Dependency Explorer（依赖探索器）**
- **Probability Oracle（概率预言机）**
- **Fault Mapper（故障映射器）**
- **Solution Engineer（解决方案工程师）**

这些智能体透明、平等地协作，在MABC管道中相互调用以处理告警事件。

### 2.2 Agent Workflow

Agent Workflow使所有智能体能够有效完成任务，遵循规定的方法：

1. **ReAct回答工作流**: 对于需要实时数据或额外信息的问题，涉及思考、行动和观察的迭代循环直到达到满意答案
2. **直接回答工作流**: 当不需要外部工具时，直接基于提供的提示制定响应

为解决循环依赖导致的非终止循环，我们在20步时终止过程。

### 2.3 多智能体详解

#### 2.3.1 Alert Receiver（A1）- 告警接收器
**职责**: 根据时间、紧急程度和影响范围对接收的告警事件进行排序。确定告警事件优先级后，将最紧急、影响最广的告警事件分派给Process Scheduler进行进一步处理。

#### 2.3.2 Process Scheduler（A2）- 流程调度器
**职责**: 当告警到达时，调动专门智能体执行数据收集、故障网络更新、依赖分析和概率评分等任务。将关键见解转发给Solution Engineer进行解决。每个子任务后检查是否识别出根因，如未解决则迭代生成新子任务直到确定根因。

#### 2.3.3 Data Detective（A3）- 数据侦探
**职责**: 按照Process Scheduler的指示，在指定时间窗口内从指定节点收集数据。排除非必要数据，处理关键指标如平均延迟、流量、错误率、资源饱和度和并发用户数。

#### 2.3.4 Dependency Explorer（A4）- 依赖探索器
**职责**: 查询微服务节点间的依赖关系，包括特定节点和告警时间。基于全局拓扑和时间窗口内的调用识别直接和间接依赖。

#### 2.3.5 Probability Oracle（A5）- 概率预言机
**职责**: 评估节点的故障概率。不可访问节点获得高默认故障概率，可访问节点基于响应时间、错误率等性能指标评估。通过分析数据相关性调整故障概率。

#### 2.3.6 Fault Mapper（A6）- 故障映射器
**职责**: 基于节点及其故障概率信息创建或更新Fault Web，可视化表示不同节点间的故障概率。

#### 2.3.7 Solution Engineer（A7）- 解决方案工程师
**职责**: 接收根因分析和解决方案请求，基于可用节点数据决定最终根因分析和解决方案。执行节点级或指标级分析，参考以前成功案例指导当前解决方案开发。

### 2.4 区块链启发投票

#### 2.4.1 区块链通信
为缓解LLM幻觉并避免陷入非终止循环，我们设计了区块链启发投票作为任何智能体任何答案的反思机制。

**特点**:
- 智能体回答后，其他所有智能体决定是否发起投票并通过加权投票获得结果
- 未发起投票或通过投票的答案被视为高质量
- 未通过的答案将由作者智能体重新生成以提高质量
- 智能体透明平等，组成去中心化的Agent Chain

#### 2.4.2 权重设计
- **贡献指数**: 基于通过率（历史上被投票认可的答案比例）
- **专业指数**: 基于智能体在不同问题上的历史表现
- **投票权重**: 贡献指数 × 专业指数

#### 2.4.3 动态权重调整
- 为未能做出足够决策的智能体引入惩罚
- 设置上限防止权重过度集中
- 确保公平和去中心化

---

## 3. 实验

### 3.1 数据集

1. **AIOps Challenge 2022 Dataset**: 工业真实数据集，包含8,333条日志
2. **Train-Ticket Dataset**: 新创建数据集，基于FuDan-SE Lab项目，在41个微服务上运行，自动生成1,200个告警事件

### 3.2 基线方法
- ReAct
- AutoGPT
- D-Bot

### 3.3 评估指标
- **根因分析准确率**: 识别根因节点
- **解决方案有效性**: 人工评估解决方案实用性

### 3.4 主要结果

| 方法 | 根因定位准确率 | 解决方案质量 |
|------|--------------|------------|
| ReAct | 41.3% | 3.2 |
| AutoGPT | 45.7% | 3.4 |
| D-Bot | 52.1% | 3.6 |
| **MABC** | **67.8%** | **4.1** |

### 3.5 消融研究
证明以下组件对性能至关重要：
- Agent Workflow
- 多智能体协作
- 区块链启发投票

---

## 4. 相关工作

### 4.1 微服务架构中的根因分析
- TraceAnomaly
- DeepTraLog
- 基于追踪图的方法

### 4.2 大型语言模型
- GPT系列的能力增长
- LLM在IT运维中的应用

### 4.3 多智能体协作
- 自然语言交互
- 任务编排方法

---

## 5. 结论

MABC通过区块链启发的投票使多智能体协作能够准确分析微服务架构中的根因并开发有效解决方案。Agent Workflow和多智能体协作提供了一个标准化但灵活的框架，简化复杂任务并全面聚合有价值的见解，而区块链启发投票确保了结果的可靠性。实验证明MABC优于基线方法，进一步证明了整个解决方案的有效性。

---

## 附录：智能体工具总览

| 智能体 | 工具名称 | 描述 |
|-------|---------|-----|
| Alert Receiver | Receive Alert Tool | 接收告警并添加到调度队列 |
| Alert Receiver | Prioritize Highest Alert Tool | 选择最高优先级告警 |
| Process Scheduler | Call For Help Tool | 向其他智能体求助 |
| Process Scheduler | Judge Sub-task Tool | 分解任务 |
| Data Detective | Data Collection Tool | 收集节点数据 |
| Data Detective | Data Cleaning Tool | 清洗预处理数据 |
| Dependency Explorer | Dependency Query Tool | 识别节点依赖 |
| Dependency Explorer | Dependency Visualization Tool | 可视化依赖关系 |
| Probability Oracle | Fault Probability Tool | 评估故障概率 |
| Probability Oracle | Correlation Analysis Tool | 分析指标相关性 |
| Fault Mapper | Fault Web Tool | 可视化更新故障网络 |
| Fault Mapper | Impact Analysis Tool | 分析故障影响 |
| Solution Engineer | Solution Development Tool | 开发解决方案 |
| Solution Engineer | Case Reference Tool | 参考历史成功案例 |
| General | Metric Explorer | 检索节点统计信息 |
| General | Alert Aggregation Tool | 聚合告警 |
| General | JSON Tool | JSON读写 |
