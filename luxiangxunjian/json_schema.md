```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "https://example.com/schemas/driver-functional-requirement.json",
  "title": "Driver-Side Functional Requirement Item",
  "description": "司机端功能性需求条目（强制原子化字段，不允许含糊的“详情/信息/数据/内容”等命名）。",
  "type": "object",
  "additionalProperties": false,
  "properties": {
    "requirementId": {
      "type": "string",
      "minLength": 1,
      "pattern": "^[A-Za-z0-9._-]+$",
      "description": "稳定ID，如 FR-DR-001"
    },
    "functionName": { "type": "string", "minLength": 1 },
    "requirementDescription": { "type": "string", "minLength": 1 },
    "inputs": {
      "type": "array",
      "minItems": 1,
      "items": { "$ref": "#/definitions/inputItem" }
    },
    "outputs": {
      "type": "array",
      "minItems": 1,
      "items": { "$ref": "#/definitions/outputItem" }
    },
    "processingLogic": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "coreSteps": {
          "type": "array",
          "minItems": 1,
          "items": { "$ref": "#/definitions/step" },
          "description": "核心处理逻辑步骤清单"
        },
        "conditionalBranches": {
          "type": "array",
          "items": { "$ref": "#/definitions/branch" },
          "description": "条件分支逻辑；每个分支单独列出触发条件、关键步骤、状态影响"
        }
      },
      "required": ["coreSteps", "conditionalBranches"]
    },
    "triggers": {
      "type": "array",
      "minItems": 1,
      "items": { "$ref": "#/definitions/descWithSources" },
      "description": "触发条件"
    },
    "stateDependencies": {
      "type": "array",
      "minItems": 1,
      "items": { "$ref": "#/definitions/descWithSources" },
      "description": "状态依赖性/外部依赖"
    }
  },
  "required": [
    "requirementId",
    "functionName",
    "requirementDescription",
    "inputs",
    "outputs",
    "processingLogic",
    "triggers",
    "stateDependencies"
  ],
  "definitions": {
    "source": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "sourceType": {
          "type": "string",
          "enum": ["UE文档", "研发设计文档", "接口文档", "需求文档", "模型推断"]
        },
        "evidenceLocator": { "type": "string" },
        "note": { "type": "string" }
      },
      "required": ["sourceType"]
    },
    "constraints": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "是否必须": { "type": "string", "enum": ["是", "否"] },
        "检验规则": { "type": "string" }
      },
      "required": ["是否必须", "检验规则"]
    },
    "atomicField": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "fieldName": {
          "type": "string",
          "minLength": 1,
          "pattern": "^(?!.*(详情|信息|资料|数据|内容|概况|情况)$).+",
          "description": "原子字段名；禁止使用泛化/容器型词汇（如“详情/信息/数据/内容”等）"
        },
        "type": { "type": "string", "enum": ["数值", "字符串", "数组", "对象"] },
        "constraints": { "$ref": "#/definitions/constraints" },
        "sources": {
          "type": "array",
          "items": { "$ref": "#/definitions/source" },
          "minItems": 1
        }
      },
      "required": ["fieldName", "type", "constraints", "sources"]
    },
    "inputItem": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "inputName": {
          "type": "string",
          "minLength": 1,
          "pattern": "^(?!.*(详情|信息|资料|数据|内容|概况|情况)$).+",
          "description": "输入项名称；禁止使用泛化/容器型命名"
        },
        "type": { "type": "string", "enum": ["数值", "字符串", "数组", "对象"] },
        "inputSource": { "type": "string", "enum": ["用户", "系统", "外部系统", "设备"] },
        "constraints": { "$ref": "#/definitions/constraints" },
        "sources": {
          "type": "array",
          "items": { "$ref": "#/definitions/source" },
          "minItems": 1
        },
        "subFields": {
          "type": "array",
          "items": { "$ref": "#/definitions/atomicField" },
          "description": "当 type=对象 时必须提供的原子字段清单"
        },
        "itemFields": {
          "type": "array",
          "items": { "$ref": "#/definitions/atomicField" },
          "description": "当 type=数组 时必须提供的单元素原子字段清单"
        }
      },
      "required": ["inputName", "type", "inputSource", "constraints", "sources"],
      "allOf": [
        {
          "if": { "properties": { "type": { "const": "对象" } }, "required": ["type"] },
          "then": { "required": ["subFields"], "properties": { "subFields": { "minItems": 3 } } }
        },
        {
          "if": { "properties": { "type": { "const": "数组" } }, "required": ["type"] },
          "then": { "required": ["itemFields"], "properties": { "itemFields": { "minItems": 3 } } }
        }
      ]
    },
    "outputItem": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "outputName": {
          "type": "string",
          "minLength": 1,
          "pattern": "^(?!.*(详情|信息|资料|数据|内容|概况|情况)$).+",
          "description": "输出项名称；禁止使用泛化/容器型命名"
        },
        "target": { "type": "string", "enum": ["用户", "系统", "设备", "数据库"] },
        "sources": {
          "type": "array",
          "items": { "$ref": "#/definitions/source" },
          "minItems": 1
        },
        "type": {
          "type": "string",
          "enum": ["数值", "字符串", "数组", "对象"],
          "description": "可选；若为对象/数组，必须列出 subFields/itemFields"
        },
        "subFields": {
          "type": "array",
          "items": { "$ref": "#/definitions/atomicField" },
          "description": "当 type=对象 时必须提供的原子字段清单（输出结构化时使用）"
        },
        "itemFields": {
          "type": "array",
          "items": { "$ref": "#/definitions/atomicField" },
          "description": "当 type=数组 时必须提供的单元素原子字段清单（输出结构化时使用）"
        }
      },
      "required": ["outputName", "target", "sources"],
      "allOf": [
        {
          "if": { "properties": { "type": { "const": "对象" } } },
          "then": { "required": ["subFields"], "properties": { "subFields": { "minItems": 3 } } }
        },
        {
          "if": { "properties": { "type": { "const": "数组" } } },
          "then": { "required": ["itemFields"], "properties": { "itemFields": { "minItems": 3 } } }
        }
      ]
    },
    "step": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "step": { "type": "string", "minLength": 1 },
        "sources": {
          "type": "array",
          "items": { "$ref": "#/definitions/source" },
          "minItems": 1
        }
      },
      "required": ["step", "sources"]
    },
    "descWithSources": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "description": { "type": "string", "minLength": 1 },
        "sources": {
          "type": "array",
          "items": { "$ref": "#/definitions/source" },
          "minItems": 1
        }
      },
      "required": ["description", "sources"]
    },
    "branch": {
      "type": "object",
      "additionalProperties": false,
      "properties": {
        "branchName": { "type": "string", "minLength": 1 },
        "triggerCondition": { "$ref": "#/definitions/descWithSources" },
        "keySteps": {
          "type": "array",
          "items": { "$ref": "#/definitions/step" },
          "description": "分支下的关键步骤（可选）"
        },
        "stateImpact": { "$ref": "#/definitions/descWithSources" }
      },
      "required": ["branchName", "triggerCondition", "stateImpact"]
    }
  }
}

```
