## StRS推荐结构和信息项

### 推荐结构

第 8.3.2 节和图 Figure 6 展示了 StRS（Stakeholder Requirements Specification）的**示例结构**，它包含以下完整**18个信息项**

```markdown
1. Introduction（引言）  
     1.1 Stakeholder Purpose（干系人目的）  
     1.2 Stakeholder Scope（干系人范围）  
     1.3 Overview（概述）  
     1.4 Definitions（定义）  
     1.5 Stakeholders（干系人）
2. References（参考资料）
3. Business Management Requirements（业务管理需求）  
     3.1 Business Environment（业务环境）  
     3.2 Mission, Goals, and Objectives（使命、目标与目标值）  
     3.3 Business Model（业务模型）  
     3.4 Information Environment（信息环境）
4. System Operational Requirements（系统运行需求）  
     4.1 System Processes（系统流程）  
     4.2 System Operational Policies and Rules（操作规则）  
     4.3 System Operational Constraints（运行约束）  
     4.4 System Operational Modes and States（运行模式和状态）
5. User Requirements（用户需求）
6. Detailed Life-Cycle Concepts of Proposed System（生命周期概念）  
     6.1 Operational Concept（运行概念）  
     6.2 Operational Scenarios（运行场景）  
     6.3 Acquisition Concept（采购概念）  
     6.4 Deployment Concept（部署概念）  
     6.5 Support Concept（运维支持）  
     6.6 Retirement Concept（退役概念）
7. Project Constraints（项目约束）
8. Appendix（附录）  
     8.1 Acronyms and Abbreviations（缩略语）
```

### 信息项


| 章节编号   | 信息项                                  | 定义/说明（标准原意）           | 是否必须 | 车位管理系统示例                        | 测试关联（测试点/用例）             |
|--------|--------------------------------------|-----------------------|------|---------------------------------|--------------------------|
| 9.4.2  | Stakeholder Purpose（干系人目的）           | 描述每类干系人对系统的核心期望和使用目标  | 必须   | 业主希望预约并查看车位使用记录；物业希望高效分配和监控车位状态 | UAT测试：角色目标达成；是否满足核心业务目标  |
| 9.4.3  | Stakeholder Scope（干系人范围）             | 定义干系人角色在系统中的操作边界和访问范围 | 必须   | 访客只能预约访客车位；物业可查看全区车位状态          | 权限测试：角色操作是否越界；数据访问控制验证   |
| 9.4.4  | Overview（概述）                         | 系统整体用途和干系人期望的简要介绍     | 建议   | 本系统提供预约、状态查看、分配等功能供不同用户使用       | 场景测试：是否覆盖不同用户路径；交互流程是否清晰 |
| 9.4.5  | Definitions（定义）                      | 列出系统相关术语定义，减少歧义       | 建议   | ‘预约’指用户申请指定时段使用某车位              | 测试用例术语对齐；避免用例设计歧义        |
| 9.4.6  | Stakeholders（干系人列表）                  | 列出所有系统相关干系人和职责        | 必须   | 业主、访客、物业管理员、安保岗亭                | 测试角色矩阵；是否每角色功能覆盖         |
| 9.4.7  | References（参考资料）                     | 引用相关标准、政策或业务文档        | 建议   | 如《物业管理条例》《车场管理制度》               | 合规性测试依据；功能合法性验证          |
| 9.4.8  | Business Environment（业务环境）           | 定义系统运行的政策、技术、管理背景     | 建议   | 需适配多物业环境，部分区域无网络                | 环境兼容性测试；离线/多租户测试         |
| 9.4.9  | Mission, Goals, Objectives（使命与目标）    | 系统上线后的期望达成目标          | 建议   | 预约响应<2秒，资源利用率提升20%              | 验收测试：性能、使用率等指标测试         |
| 9.4.10 | Business Model（业务模型）                 | 组织、服务、运营模式说明          | 建议   | 物业负责运维，开发商部署，住户免费使用             | 角色测试；权限与业务规则一致性验证        |
| 9.4.11 | Information Environment（信息环境）        | 说明系统对接的其他信息系统和数据依赖    | 建议   | 对接住户信息库、摄像头、报修系统                | 集成测试；接口数据准确性校验           |
| 9.4.12 | System Processes（系统流程）               | 系统内部操作流程与处理逻辑         | 建议   | 预约申请 → 审批 → 通知 → 入场             | 流程测试；是否按节点顺序处理           |
| 9.4.13 | Operational Policies and Rules（操作规则） | 操作层面的行为规范和判断逻辑        | 建议   | 预约超时取消、每车位每天限用1次                | 规则验证；边界与约束测试             |
| 9.4.14 | Operational Constraints（操作约束）        | 系统运行限制，如时效、安全等        | 建议   | 所有数据24小时内同步；操作需日志记录             | 日志测试；同步机制有效性测试           |
| 9.4.15 | Operational Modes and States（操作模式）   | 定义系统的状态和运行模式          | 建议   | 正常、维护、离线三种模式                    | 模式切换测试；离线容错测试            |
| 9.4.16 | User Requirements（用户需求）              | 从用户角度表达的具体需求项         | 必须   | 业主希望随时查看剩余车位并在线预约               | 功能性测试：功能是否满足用户意图         |
| 9.4.17 | Operational Concept（运行概念）            | 从全局描述系统如何被使用和部署       | 建议   | 系统部署在园区云平台，多入口接入                | 部署测试；入口可用性测试             |
| 9.4.18 | Operational Scenarios（运行场景）          | 典型使用场景示例              | 建议   | 张先生预约访客车位 → 到场 → 登记 → 离开        | 端到端测试；典型路径覆盖验证           |
| 9.4.19 | Project Constraints（项目约束）            | 项目预算、周期、法律、合规等限制      | 建议   | 需在3个月内上线，符合小区数据保护规范             | 时间/合规测试；数据保护验证           |


