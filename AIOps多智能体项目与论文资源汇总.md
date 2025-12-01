# AIOps 多智能体项目与论文资源汇总

> **整理目的**: 帮助优化根因定位和分析方案
> 
> **关注重点**: 多智能体（Multi-Agent）项目
>
> **更新时间**: 2025年12月

---

## 目录

1. [核心开源项目](#一核心开源项目)
2. [重要学术论文](#二重要学术论文)
3. [基准数据集与评测框架](#三基准数据集与评测框架)
4. [Agent开发框架](#四agent开发框架)
5. [资源合集与学习资料](#五资源合集与学习资料)
6. [技术趋势分析](#六技术趋势分析)
7. [推荐学习路线](#七推荐学习路线)

---

## 一、核心开源项目

### 1.1 AIOpsLab (Microsoft) ⭐⭐⭐⭐⭐

**项目简介**: 微软研究院开发的AIOps代理评估整体框架，是目前最完整的AIOps评测平台

**核心特性**:
- 支持微服务云环境部署
- 故障注入与工作负载生成
- 遥测数据导出
- Agent-Cloud Interface (ACI) 代理云交互接口
- 48个问题的基准测试套件

**技术亮点**:
- 四级任务体系：检测 → 定位 → 根因分析 → 缓解
- 支持症状性故障和功能性故障的注入
- 可观测性三要素：日志、指标、追踪

**资源链接**:
- GitHub: https://aka.ms/aiopslab-repo
- 官网: https://microsoft.github.io/AIOpsLab/
- 论文: https://arxiv.org/abs/2501.07606

---

### 1.2 mABC: 多智能体区块链启发式协作框架 ⭐⭐⭐⭐⭐

**项目简介**: 首创使用区块链投票机制的多智能体根因分析框架，发表于EMNLP 2024

**核心特性**:
- 7个专业化Agent协作
- 区块链启发的投票协议
- 有效缓解LLM幻觉问题
- 解决循环依赖问题

**技术亮点**:
- 每个Agent专注特定RCA子任务
- 结构化工作流程设计
- 去中心化决策机制减少单点故障

**资源链接**:
- GitHub: https://github.com/zwpride/mABC
- 论文: https://arxiv.org/abs/2404.12135
- PDF: https://aclanthology.org/2024.findings-emnlp.232.pdf

---

### 1.3 OpenRCA (Microsoft) ⭐⭐⭐⭐

**项目简介**: 微软开源的LLM根因分析基准测试项目

**核心特性**:
- LLM代理RCA能力评估
- 时间序列和日志数据分析
- 追踪图分析
- Python实现

**资源链接**:
- GitHub: https://github.com/microsoft/OpenRCA

---

### 1.4 AIOps Polaris ⭐⭐⭐⭐

**项目简介**: 基于多智能体架构和RAG的自动化RCA系统

**核心特性**:
- 多Agent协作（Knowledge Agent、Reasoning Agent、Executor Agent等）
- 检索增强生成（RAG）
- 故障数据建模
- 解决方案自动生成

**资源链接**:
- GitHub: https://github.com/pkusnail/AIOpsPolaris

---

### 1.5 MicroRCA-Agent ⭐⭐⭐⭐

**项目简介**: CCF国际AIOps挑战赛决赛项目，模块化多智能体微服务RCA解决方案

**核心特性**:
- 多模态数据支持（日志、追踪、指标）
- 结构化RCA输出
- 闭环推理
- 可扩展模块分离

**资源链接**:
- GitHub: https://github.com/tangpan360/MicroRCA-Agent

---

### 1.6 RCAgent (阿里巴巴/学术界) ⭐⭐⭐⭐

**项目简介**: 工具增强的自主云根因分析Agent，发表于CIKM 2024

**核心特性**:
- Controller Agent + Expert Agent架构
- 自由形式数据收集
- 动作轨迹与自一致性机制
- 隐私安全设计（支持本地部署）

**技术亮点**:
- 在阿里云真实生产环境部署
- 超越ReAct等基线方法
- 高级上下文管理

**资源链接**:
- 论文: https://arxiv.org/pdf/2310.16340
- 相关资源: https://www.catalyzex.com/paper/rcagent-cloud-root-cause-analysis-by

---

### 1.7 FLASH (Microsoft) ⭐⭐⭐⭐

**项目简介**: 微软工作流自动化代理，专注重复事件诊断

**全称**: workFLow Automation agent with Status supervision and Hindsight integration

**核心特性**:
- 状态监督（Status Supervision）
- 事后学习集成（Hindsight Integration）
- 工作流自动化
- 比SOTA代理提升13.2%诊断准确率

**技术亮点**:
- 将复杂诊断指令分解为可管理的条件片段
- 从过去失败中自动学习
- 限制错误传播

**资源链接**:
- 论文: https://www.microsoft.com/en-us/research/wp-content/uploads/2024/10/FLASH_Paper.pdf
- 项目页: https://www.microsoft.com/en-us/research/project/flash-a-reliable-workflow-automation-agent/

---

### 1.8 Intelligent Fault Diagnosis Multi-Agent System ⭐⭐⭐

**项目简介**: 基于CrewAI的智能故障诊断多智能体系统

**核心特性**:
- 电信网络故障诊断
- RAG工作流程
- 验证层和会话管理
- 生产级错误处理

**资源链接**:
- GitHub: https://github.com/Sodz99/multi-agent-fault-diagnosis

---

## 二、重要学术论文

### 2.1 多智能体系统论文

#### Flow-of-Action: SOP Enhanced LLM-Based Multi-Agent System for Root Cause Analysis
- **会议**: WWW'25 (2025) 工业赛道
- **作者**: Changhua Pei 等
- **核心贡献**: 
  - 使用SOP（标准操作流程）约束LLM多智能体系统
  - 辅助Agent过滤噪声、优化搜索空间
  - 达到64%准确率（对比之前35.5%）
- **链接**: https://arxiv.org/abs/2502.08224

#### MA-RCA: Multi-Agent Root Cause Analysis
- **期刊**: Complex & Intelligent Systems (Springer, Nov 2025)
- **核心贡献**:
  - 检索和验证Agent动态验证假设
  - 减少上下文切换失败
  - 在真实数据集上达到更高F1分数
- **链接**: https://link.springer.com/article/10.1007/s40747-025-02096-0

#### Chain-of-Event: Interpretable Root Cause Analysis for Microservices
- **会议**: FSE Companion '24
- **核心贡献**:
  - 自动学习事件因果图
  - 集成SRE专业知识
  - 在电商数据集（5000+服务）上达到98.8% top-3准确率
- **链接**: https://netman.aiops.org/wp-content/uploads/2024/07/Chain-of-Event_Interpretable-Root-Cause-Analysis-for-MicroservicesFSE24-Camera-Ready.pdf

---

### 2.2 LLM Agent 论文

#### Exploring LLM-based Agents for Root Cause Analysis
- **会议**: FSE 2024
- **作者**: Roy 等 (Microsoft)
- **核心贡献**:
  - ReAct LLM Agent + 检索工具
  - 微软生产事件数据集评估
  - 显著提升事实准确性
- **链接**: https://arxiv.org/abs/2403.04123

#### TAMO: Fine-Grained Root Cause Analysis via Tool-Assisted LLM Agent
- **时间**: 2025
- **核心贡献**:
  - 工具增强LLM Agent
  - 克服多模态输入约束
  - 解决动态依赖问题
- **链接**: https://arxiv.org/abs/2504.20462

#### AIOps for Reliability: Evaluating LLMs for Automated RCA in Chaos Engineering
- **时间**: 2025
- **核心贡献**:
  - 评估GPT-4o、Gemini-1.5、Mistral-small
  - 混沌工程故障场景
  - 提供代码和数据集
- **链接**: https://github.com/szandala/llms-chaos-engineering

---

### 2.3 图神经网络相关论文

#### QMIX-GNN: GNN-Based Heterogeneous Multi-Agent Information Fusion
- **核心贡献**: 通过GNN融合异构多智能体信息，改进协作决策
- **链接**: https://www.mdpi.com/2076-3417/15/7/3794

#### Reliable and Efficient Multi-Agent Coordination via Graph Neural Networks
- **核心贡献**: GNN-VAE学习协调和故障模式
- **链接**: https://arxiv.org/abs/2503.02954

#### A Heterogeneous Graph-Based Multi-Task Learning for Fault Event Classification
- **核心贡献**: 配电网故障事件分类的多任务GNN
- **链接**: https://ieeexplore.ieee.org/document/10643407

---

## 三、基准数据集与评测框架

### 3.1 RCAEval ⭐⭐⭐⭐⭐

**简介**: ASE 2024/WWW 2025发布的开源RCA基准测试

**数据规模**:
- 3个数据集（RE1, RE2, RE3）
- 735个真实故障案例
- 3个微服务系统：Online Boutique、Sock Shop、Train Ticket
- 11种故障类型

**资源链接**:
- GitHub: https://github.com/phamquiluan/RCAEval
- 论文: https://arxiv.org/html/2412.17015v5
- 数据: https://zenodo.org/records/14504481

---

### 3.2 NetManAIOps 数据集 ⭐⭐⭐⭐⭐

**简介**: 清华大学NetMan实验室的AIOps数据集合集

**包含数据集**:
- AIOps-Challenge-2020-Data
- LatentScope
- OpsEval-Datasets

**资源链接**:
- GitHub: https://github.com/NetManAIOps
- 论文: arXiv:2208.03938

---

### 3.3 AIOpsArena & MicroServo ⭐⭐⭐⭐

**简介**: 场景导向的AIOps评估环境

**特性**:
- 实时微服务数据集生成
- 动态故障编排
- 算法热插拔
- 排行榜比较

**资源链接**:
- AIOpsArena GitHub: https://github.com/AIOpsArena/aiopsarena
- MicroServo论文: https://arxiv.org/pdf/2407.14532

---

### 3.4 DeathStarBench ⭐⭐⭐⭐

**简介**: 微服务研究标准基准测试套件

**包含应用**:
- SocialNetwork（28个微服务）
- HotelReservation（酒店预订系统）

**资源链接**:
- GitHub: https://github.com/delimitrou/DeathStarBench

---

## 四、Agent开发框架

### 4.1 通用Agent框架

| 框架 | 特点 | 链接 |
|------|------|------|
| **LangChain** | 上下文感知推理、知识检索 | https://python.langchain.com |
| **AutoGen (Microsoft)** | 协作多智能体设计 | https://github.com/microsoft/autogen |
| **CrewAI** | 结构化多智能体工作流 | https://github.com/joaomdmoura/crewAI |
| **CAMEL** | 首个多智能体LLM框架 | https://github.com/camel-ai/camel |

### 4.2 可观测性工具

| 工具 | 用途 | 链接 |
|------|------|------|
| **Prometheus** | 指标监控 | https://prometheus.io |
| **Jaeger** | 分布式追踪 | https://www.jaegertracing.io |
| **Chaos-Mesh** | 故障注入 | https://chaos-mesh.org |

---

## 五、资源合集与学习资料

### 5.1 Awesome 列表

| 名称 | 描述 | 链接 |
|------|------|------|
| **awesome-LLM-AIOps** | LLM驱动的AIOps资源合集 | https://github.com/Jun-jie-Huang/awesome-LLM-AIOps |
| **awesome-failure-diagnosis** | 故障诊断资源合集 | https://github.com/phamquiluan/awesome-failure-diagnosis |

### 5.2 微软相关资源

| 资源 | 描述 | 链接 |
|------|------|------|
| AIOpsLab教程 | ICML 2025教程 | https://microsoft.github.io/AIOpsLab/ |
| RCA Agent方案 | Copilot Studio集成指南 | https://adoption.microsoft.com/en-us/scenario-library/information-technology/root-cause-analysis-agent/ |
| Triangle System | Azure AIOps优化 | https://azure.microsoft.com/en-us/blog/optimizing-incident-management-with-aiops-using-the-triangle-system/ |

---

## 六、技术趋势分析

### 6.1 多智能体协作趋势

1. **去中心化决策机制**
   - 区块链启发的投票协议
   - 减少单点LLM故障
   - 缓解Agent幻觉

2. **SOP与验证Agent**
   - 标准化推理步骤
   - 验证Agent检查假设
   - 减少错误传播

3. **多模态多领域数据**
   - 日志 + 指标 + 追踪
   - 真实场景故障定位

### 6.2 关键技术方向

| 方向 | 描述 | 相关项目 |
|------|------|----------|
| **Agent协作架构** | 多专业Agent分工合作 | mABC, Flow-of-Action |
| **RAG增强** | 检索增强减少幻觉 | AIOps Polaris, RCAgent |
| **工具调用** | LLM调用外部诊断工具 | TAMO, AIOpsLab |
| **事后学习** | 从历史事件学习 | FLASH |
| **图神经网络** | 服务依赖关系建模 | Chain-of-Event |

### 6.3 现有方法局限性

1. **信息过载**: Agent处理大量遥测数据时性能下降
2. **幻觉问题**: LLM误判系统状态
3. **无效操作**: Agent重复调用无效API，浪费步骤
4. **反馈机制**: 运维任务反馈模糊，难以迭代改进

---

## 七、推荐学习路线

### 7.1 入门阶段（1-2周）

1. **阅读AIOpsLab论文** - 理解AIOps评估框架
2. **了解四级任务体系** - 检测→定位→RCA→缓解
3. **复现简单Agent** - 使用LangChain实现基础ReAct框架

### 7.2 进阶阶段（3-4周）

1. **深入mABC论文** - 理解多智能体协作机制
2. **学习FLASH架构** - 掌握工作流自动化设计
3. **实验RCAEval基准** - 在标准数据集上测试算法

### 7.3 实践阶段（1-2月）

1. **搭建AIOpsLab环境** - 部署微服务测试应用
2. **设计多智能体方案** - 结合项目需求定制
3. **对比实验** - 在多个数据集上验证效果

### 7.4 优化方向建议

针对你的根因定位和分析优化需求，推荐关注：

1. **Agent协作架构优化**
   - 参考mABC的投票机制
   - 引入验证Agent减少误判

2. **RAG增强策略**
   - 结构化知识库检索
   - 领域约束生成

3. **多模态数据融合**
   - 日志-指标-追踪联合分析
   - 服务依赖图建模

4. **小模型蒸馏**
   - 大模型知识迁移到小模型
   - 降低推理成本

---

## 联系信息与贡献

如果你发现了其他优秀的AIOps多智能体项目或论文，欢迎补充！

---

*本文档由学习助手整理，供学术研究参考使用*

*最后更新：2025年12月*
