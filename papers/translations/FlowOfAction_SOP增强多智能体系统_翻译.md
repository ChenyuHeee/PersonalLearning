# Flow-of-Action: 基于SOP增强的LLM多智能体根因分析系统

> **原文标题**: Flow-of-Action: SOP Enhanced LLM-Based Multi-Agent System for Root Cause Analysis
>
> **来源**: WWW'25 Companion (The Web Conference 2025, Industry Track)
>
> **作者**: Changhua Pei 等
>
> **机构**: 清华大学、阿里巴巴等

---

## 摘要

微服务架构（MSA）中的根因分析（RCA）是一项复杂且关键的任务，现有方法难以处理多样化且动态变化的故障。尽管基于LLM的智能体展现出潜力，但在微服务RCA中面临挑战：工具编排混乱和搜索空间庞大。

本文提出**Flow-of-Action**，一个创新框架，引入：
1. **SOP（标准操作流程）**来约束LLM多智能体系统
2. **辅助智能体**过滤噪声、优化搜索空间

实验表明，Flow-of-Action在RCA准确率上达到**64%**，对比之前最优方法的**35.5%**提升显著。

---

## 1. 引言

### 1.1 微服务RCA的挑战

微服务架构（MSA）将单体系统分解为服务，这些服务间通过复杂的调用拓扑连接。RCA在维护和确保系统可靠性方面至关重要。

**主要挑战**:
1. **故障多样性**: 故障类型高度多样化
2. **数据复杂性**: 日志、指标、追踪数据庞大
3. **变化频繁**: 新故障类型不断出现
4. **关联复杂**: 服务间依赖关系复杂

### 1.2 现有方法的局限性

1. **传统ML方法**: 需要大量标注数据，难以适应新故障
2. **基于LLM的Agent**: 工具编排混乱、搜索空间庞大、精度不足

### 1.3 Flow-of-Action的创新

- **SOP约束**: 使用标准操作流程引导Agent决策
- **多Agent协作**: MainAgent + ActionAgent + JudgeAgent + ObAgent
- **Action Set机制**: 优化动作选择空间
- **SOP代码执行**: 将SOP转换为代码精确执行

---

## 2. 方法论

### 2.1 系统架构

```
                    ┌────────────────────┐
                    │     MainAgent      │
                    │   (主决策Agent)     │
                    └─────────┬──────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  ActionAgent │     │  JudgeAgent  │     │   ObAgent    │
│ (动作生成)    │     │ (判断评估)    │     │ (观察分析)   │
└──────────────┘     └──────────────┘     └──────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              ▼
                    ┌────────────────────┐
                    │     SOP Flow       │
                    │   (标准流程控制)    │
                    └────────────────────┘
```

### 2.2 工具设计

#### 2.2.1 数据收集工具

| 工具 | 功能 |
|-----|------|
| `metric_anomaly_detect` | 检测指标异常并转换为文本 |
| `collect_trace` | 捕获调用链异常span |
| `kubectl_logs` | 提取Kubernetes pod日志 |

#### 2.2.2 SOP流程工具

| 工具 | 功能 |
|-----|------|
| `match_sop` | 匹配相关SOP |
| `generate_sop` | 生成新SOP |
| `generate_sop_code` | 将SOP转换为代码 |
| `run_sop` | 执行SOP代码 |
| `match_observation` | 匹配历史观察 |

#### 2.2.3 分析工具

| 工具 | 功能 |
|-----|------|
| `pod_analyze` | 分析Pod状态 |
| `service_analyze` | 分析服务状态 |
| `Speak` | 通报根因结果 |

### 2.3 SOP Flow详解

#### 2.3.1 故障信息 → SOP

**流程**:
1. 使用`match_sop`计算查询与SOP名称的相似度
2. 选择top-k匹配的SOP
3. 设置过滤阈值避免无关匹配
4. 无匹配时触发`generate_sop`

**SOP生成示例**:
```
IO Error SOP:
1. 检查磁盘使用率
2. 验证文件系统完整性
3. 检查IO等待时间
4. 分析相关进程
5. 检查硬件状态
```

#### 2.3.2 SOP → SOP Code

**为什么要转换为代码**:
1. **精确性**: 代码执行比文本执行更准确
2. **原子性**: 确保步骤完整执行
3. **效率**: 一次调用执行多个操作，节省token

**代码生成示例**:
```python
def execute_io_error_sop():
    disk_usage = check_disk_usage()
    if disk_usage > 90:
        return "High disk usage detected"
    
    fs_status = verify_filesystem()
    io_wait = check_io_wait()
    
    if io_wait > threshold:
        return analyze_io_processes()
    
    return check_hardware_status()
```

#### 2.3.3 SOP Code → Observation

**执行流程**:
1. 调用`run_sop`执行SOP代码
2. 处理可能的语法错误
3. 重新匹配参数并重新生成代码
4. 获取执行结果作为观察

#### 2.3.4 SOP Code → 故障类型

**层次化方法**:
- 从宏观到微观
- 从一般到具体
- 例：先定位网络问题 → 再确定网络分区问题

使用`match_observation`召回历史相似事件，辅助判断故障类型

### 2.4 Action Set机制

**问题**: LLM难以从众多可行动作中即时选择最合理的

**解决方案**: 生成动作集合（Action Set）

**组成**:
1. **ActionAgent生成**: 基于flow和示例生成候选动作
2. **JudgeAgent补充**: 基于规则确保动作集完整

**示例**:
```
当前观察: 高IO等待
生成的Action Set:
1. check_disk_io - 检查磁盘IO详情
2. analyze_process_io - 分析进程IO
3. check_storage_health - 检查存储健康
4. Speak - 已确定根因时通报
```

---

## 3. 多智能体设计

### 3.1 MainAgent（主智能体）

**职责**: 
- 接收SOP Flow提示
- 协调其他Agent
- 做出最终决策

### 3.2 ActionAgent（动作智能体）

**职责**:
- 基于当前状态生成候选动作
- 为每个动作提供理由说明
- 整合flow信息和示例

### 3.3 JudgeAgent（判断智能体）

**职责**:
- 评估当前RCA进度
- 判断是否已找到根因
- 补充遗漏的关键动作

### 3.4 ObAgent（观察智能体）

**职责**:
- 分析执行结果
- 确定潜在故障类型
- 提供后续RCA指导

---

## 4. 实验

### 4.1 数据集

使用开源微服务系统:
- Train-Ticket
- Online Boutique
- Sock Shop

故障类型：30+种

### 4.2 基线方法

- ReAct
- AutoGPT
- D-Bot
- mABC
- RCAgent

### 4.3 主要结果

| 方法 | 准确率 |
|-----|-------|
| ReAct | 22.3% |
| AutoGPT | 28.1% |
| D-Bot | 32.5% |
| mABC | 35.5% |
| **Flow-of-Action** | **64.0%** |

**关键发现**:
- 相比最优基线提升80%
- SOP约束显著提高决策质量
- 多Agent协作有效减少错误

### 4.4 消融研究

| 组件 | 移除后准确率下降 |
|-----|----------------|
| SOP Flow | -18.5% |
| Action Set | -12.3% |
| ObAgent | -8.7% |
| Code Execution | -6.2% |

---

## 5. 案例分析

### 5.1 成功案例

**场景**: 订单服务响应缓慢

**Flow-of-Action执行过程**:
1. `match_sop` → 匹配"服务延迟SOP"
2. `generate_sop_code` → 生成诊断代码
3. `run_sop` → 执行诊断
4. 观察：数据库连接池耗尽
5. `match_observation` → 确认故障类型
6. `Speak` → 输出根因

**结果**: 准确定位到数据库连接池问题

### 5.2 与基线对比

| 方面 | ReAct | Flow-of-Action |
|-----|-------|---------------|
| 步骤数 | 15+ | 6 |
| 无效调用 | 多 | 少 |
| 最终结果 | 模糊 | 精确 |

---

## 6. 结论

Flow-of-Action通过引入SOP约束和多Agent协作，有效解决了微服务RCA中的工具编排混乱和搜索空间庞大问题。实验证明，该方法在准确率上显著优于现有方法，为实际运维场景提供了可行的解决方案。

---

## 附录：关键提示词

### SOP Flow Prompt

```
你是一个专业的SRE，正在执行根因分析。
请遵循以下SOP流程：

1. 首先匹配相关SOP (match_sop)
2. 如无匹配，生成新SOP (generate_sop)
3. 将SOP转换为代码 (generate_sop_code)
4. 执行SOP代码 (run_sop)
5. 分析执行结果
6. 确定故障类型或继续深入分析
7. 找到根因后通报 (Speak)
```
