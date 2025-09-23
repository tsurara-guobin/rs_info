
# 问题
现在大模型的多模态能力，尤其是读UE交互图不够强，很容易漏信息或者理解错误。

# 结构化方式

UIUE控件信息提取+UE图识别
- 【UI】导出控件信息：Sketch都能导出控件树（JSON或DOM树式结构）。可把UI元素（按钮、输入框、下拉框等）的属性导出来。
- 【UE】大模型多模态能力较弱，需要人工补充
  - 约束信息：多为文字
  - 状态依赖：部分是文字，部分是图
  - 触发条件，控件逻辑、控件或页面跳转关系：多为箭头

## 结构化的目标
- 完整性：页面元素 + 属性 + 约束 + 交互关系。
- 层次性：页面 → 区块/模块 → 元素 → 动作/状态。
- 可追踪性（待定）：能映射到需求项、甚至测试点。

## 我建议的结构化方式

- **页面 (Page)**
    - 页面名称、唯一ID、场景说明
    - 前置/后置条件（进入页面前提，退出页面结果）
- **区域/模块 (Section/Block)**
    - 区域名称、作用（如：导航区/表单区/结果展示区）
    - 是否可折叠、动态显示条件        
- **界面元素 (UI Element)**
    - 类型（按钮、文本框、下拉框、表格、列表、开关等）
    - 属性（label、默认值、占位符、是否必填、取值范围、约束）
    - 状态依赖（例如“仅当选择‘手机号登录’时显示验证码输入框”）
- **交互逻辑 (Interaction/Flow)**
    - 动作（如点击、输入、悬停、拖拽）
    - 触发条件
    - 系统响应（如弹窗、跳转、状态更新、接口调用）
    - 错误提示/异常分支
 
## Json schema设计

### schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "UE Information Schema",
  "description": "结构化表示界面交互图的页面、区域、元素和交互逻辑，用于功能性需求提取",
  "type": "object",
  "properties": {
    "page": {
      "type": "object",
      "required": ["id", "name", "sections"],
      "properties": {
        "id": {
          "type": "string",
          "description": "页面唯一ID（必须）"
        },
        "name": {
          "type": "string",
          "description": "页面名称（必须）"
        },
        "scenario": {
          "type": "string",
          "description": "页面业务场景说明（建议）"
        },
        "preconditions": {
          "type": "array",
          "items": { "type": "string" },
          "description": "进入页面的前置条件（可选）"
        },
        "postconditions": {
          "type": "array",
          "items": { "type": "string" },
          "description": "退出页面的后置条件（可选）"
        },
        "sections": {
          "type": "array",
          "description": "页面包含的功能区域",
          "items": {
            "type": "object",
            "required": ["id", "name", "elements"],
            "properties": {
              "id": {
                "type": "string",
                "description": "区域唯一ID（必须）"
              },
              "name": {
                "type": "string",
                "description": "区域名称（必须）"
              },
              "purpose": {
                "type": "string",
                "description": "区域作用/用途，如表单区、导航区、结果展示区（建议）"
              },
              "collapsible": {
                "type": "boolean",
                "description": "是否可折叠（可选）"
              },
              "dynamic_visibility": {
                "type": "string",
                "description": "动态显示条件（可选）"
              },
              "elements": {
                "type": "array",
                "description": "区域包含的界面元素",
                "items": {
                  "type": "object",
                  "required": ["id", "type", "label", "required", "interactions"],
                  "properties": {
                    "id": {
                      "type": "string",
                      "description": "元素唯一ID（必须）"
                    },
                    "type": {
                      "type": "string",
                      "description": "控件类型，如 button/text_input/checkbox（必须）"
                    },
                    "label": {
                      "type": "string",
                      "description": "元素显示标签或字段名（必须）"
                    },
                    "default_value": {
                      "type": ["string", "null"],
                      "description": "默认值（可选）"
                    },
                    "placeholder": {
                      "type": ["string", "null"],
                      "description": "占位符（可选）"
                    },
                    "required": {
                      "type": "boolean",
                      "description": "是否必填（必须）"
                    },
                    "value_range": {
                      "type": "array",
                      "items": { "type": "string" },
                      "description": "取值范围（可选）"
                    },
                    "constraints": {
                      "type": "array",
                      "items": { "type": "string" },
                      "description": "输入/使用约束（建议）"
                    },
                    "state_dependency": {
                      "type": "string",
                      "description": "状态依赖，如仅在某些条件下显示（可选）"
                    },
                    "interactions": {
                      "type": "array",
                      "description": "元素交互逻辑",
                      "items": {
                        "type": "object",
                        "required": ["action", "system_response"],
                        "properties": {
                          "action": {
                            "type": "string",
                            "description": "动作，如 click/input（必须）"
                          },
                          "condition": {
                            "type": "string",
                            "description": "触发条件（建议）"
                          },
                          "system_response": {
                            "type": "string",
                            "description": "系统响应（必须）"
                          },
                          "error_feedback": {
                            "type": "array",
                            "items": { "type": "string" },
                            "description": "错误提示/异常分支（建议）"
                          }
                        }
                      }
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  },
  "required": ["page"]
}
```

### 示例

```json
```

### 字段必须/建议/可选说明

|层级|字段|必须/建议/可选|理由|
|---|---|---|---|
|Page|id, name|必须|保证唯一性与上下文|
|Page|scenario|建议|提炼业务目的|
|Page|preconditions, postconditions|可选|补充场景/流程约束|
|Section|id, name|必须|区分区域|
|Section|role|可选|分类辅助|
|Section|collapsible, dynamic_visibility|可选|主要影响 UI/交互，不影响核心 FR|
|Element|id, type, label, required|必须|直接决定需求描述|
|Element|constraints|建议|生成输入校验需求|
|Element|default_value, placeholder|可选|UI需求，不影响 FR|
|Element|value_range, state_dependency|可选|主要用于扩展需求覆盖|
|Interaction|action, system_response|必须|定义核心 FR|
|Interaction|condition, error_feedback|建议|区分正/负向用例，生成异常处理需求|

