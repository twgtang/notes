---
{"dg-publish":true,"permalink":"//oa/oa/","tags":["gardenEntry"],"dg-note-properties":{}}
---
# OA 合同派工优化

## 一、背景与目标

针对 OA 合同派工流程，增加每日派工次数限制和跨天派工扣减机制，实现派工资源的合理管控。

## 二、适用范围

| 维度 | 范围 |
|------|------|
| 签约主体（乙方） | 上海总部、张江智电、临港电管家 |
| 系统 | 变电站运维系统（法拉第） |
| 派工类型 | 工程、售后、电试 |

> **不在范围内的签约主体**：派工流程不变，不受每日次数限制。
> **不在范围内的派工类型**：不受每日次数限制。

## 三、核心规则

### 3.1 每日派工上限

- 额度按**全局**计算，三家签约主体（上海总部、张江智电、临港电管家）**共享同一额度池**
- 每天**工程 + 售后合计最多 5 次**（共享额度），**电试独立最多 5 次**（5 次为默认值，后台可配置）
- 一次派工只能选择**一种**派工类型（工程、售后或电试）
- 额度通过**后台管理**调整

### 3.2 跨天派工扣减

- 派工时间通过**日历选择**，最小颗粒度为**小时**（例：10:00）
- 定义：派工时间跨越多个自然日（例：周一 10:00 至周三 10:00）
- 自然日边界：只要派工时间落在某自然日内，该日即计入扣减（例：周一 10:00 → 周二 09:00，涉及周一、周二共 **2 个自然日**）
- 扣减规则：**按派工涉及的自然日逐日扣减**
  - 例：周一 10:00 → 周三 10:00，涉及周一、周二、周三三个自然日
    - 工程/售后：分别从各天的**共享额度**中各扣 1 次
    - 电试：从各天的**独立额度**中各扣 1 次
- **任一天额度不足时，无法提交**，提示"X月X日 工程/售后或电试额度已满"

### 3.3 额度回退

- 派工被**取消、作废或驳回**后，对应日期的额度**自动回退**
- 跨天派工取消/驳回时，涉及的每个自然日额度均回退

### 3.4 额度校验时机

- 用户选择日期和时间后，系统实时校验**所有涉及日期**的剩余额度
- 所有涉及日期额度均充足时，方可提交
- **任一日期**额度不足时，禁止提交并给出明确提示
- 提交时需**二次校验**（并发控制），防止多人同时提交导致超限

### 3.5 派工时间修改

- 派工流程流转到**调度人员审核节点**时，调度人员可**修改派工时间**（调整开始/结束时间或日期）
- 修改时间后，系统**重新校验**涉及日期的剩余额度
  - 若修改后涉及日期范围变化（如单天变跨天），按新范围重新扣减校验
  - 若任一日期额度不足，禁止提交并提示
- 修改时先**回退原涉及日期额度**，再按新时间重新扣减校验

## 四、角色与权限

| 角色 | 权限 |
|------|------|
| 派工人 | 执行派工操作 |
| 调度人员 | 审核节点修改派工时间、实时调整每日额度（当天或未来某天） |

> 派工人不区分角色，不需要单独的派工权限配置。

## 五、后台管理功能

### 5.1 额度配置

- 设置每日默认额度（工程+售后共享额度 / 电试独立额度），三家签约主体共享
- 支持针对**指定日期**临时调整额度（增加或减少）
- 仅支持调整**当天及未来**日期的额度，不可调整过去日期
- 调整额度时，**不可低于当日已用次数**
- 调整记录可追溯（操作人、时间、调整前后数值）

## 六、交互流程

📎 业务流程图：

<style> .container {font-family: sans-serif; text-align: center;} .button-wrapper button {z-index: 1;height: 40px; width: 100px; margin: 10px;padding: 5px;} .excalidraw .App-menu_top .buttonList { display: flex;} .excalidraw-wrapper { height: 800px; margin: 50px; position: relative;} :root[dir="ltr"] .excalidraw .layer-ui__wrapper .zen-mode-transition.App-menu_bottom--transition-left {transform: none;} </style><script src="https://cdn.jsdelivr.net/npm/react@17/umd/react.production.min.js"></script><script src="https://cdn.jsdelivr.net/npm/react-dom@17/umd/react-dom.production.min.js"></script><script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@excalidraw/excalidraw@0/dist/excalidraw.production.min.js"></script><div id="OA派工优化-流程图excalidraw.md1"></div><script>(function(){const InitialData={
	"type": "excalidraw",
	"version": 2,
	"source": "https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/2.25.2",
	"elements": [
		{
			"id": "SYYZ6WodYEurE3D4vhqB",
			"type": "ellipse",
			"x": 375,
			"y": 0,
			"width": 150,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#d3f9d8",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 2134927282,
			"version": 3,
			"versionNonce": 1538394194,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "wnVGh3SN",
					"type": "text"
				},
				{
					"id": "4cap8l61",
					"type": "arrow"
				}
			],
			"updated": 1783502513654,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "a0",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "wnVGh3SN",
			"type": "text",
			"x": 414,
			"y": 13.75,
			"width": 72,
			"height": 22.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 170767202,
			"version": 2,
			"versionNonce": 1847469467,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "开始派工",
			"fontSize": 18,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "SYYZ6WodYEurE3D4vhqB",
			"originalText": "开始派工",
			"lineHeight": 1.25,
			"baseline": 18.5,
			"autoResize": true,
			"index": "a1",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "开始派工"
		},
		{
			"id": "p6A00SU3Yvn7pHDz0I4u",
			"type": "diamond",
			"x": 350,
			"y": 100,
			"width": 200,
			"height": 120,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#fff3bf",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1355666785,
			"version": 3,
			"versionNonce": 462286610,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "73gYQswR",
					"type": "text"
				},
				{
					"id": "4cap8l61",
					"type": "arrow"
				}
			],
			"updated": 1783502515694,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "a2",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "73gYQswR",
			"type": "text",
			"x": 416.5050048828125,
			"y": 141.25,
			"width": 66.989990234375,
			"height": 37.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1313222325,
			"version": 2,
			"versionNonce": 1209842235,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "签约主体\n在范围内?",
			"fontSize": 15,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "p6A00SU3Yvn7pHDz0I4u",
			"originalText": "签约主体\n在范围内?",
			"lineHeight": 1.25,
			"baseline": 33.5,
			"autoResize": true,
			"index": "a3",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "签约主体\n在范围内?"
		},
		{
			"id": "QV-89RUGCm5VL7cDZyi4",
			"type": "rectangle",
			"x": 70,
			"y": 120,
			"width": 220,
			"height": 56,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#e9ecef",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 174468336,
			"version": 2,
			"versionNonce": 1624201973,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "CbJ9bAGv",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "a4",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "CbJ9bAGv",
			"type": "text",
			"x": 131,
			"y": 130.5,
			"width": 98,
			"height": 35,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 2026710487,
			"version": 2,
			"versionNonce": 1520417499,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "走原有派工流程\n不受额度限制",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "QV-89RUGCm5VL7cDZyi4",
			"originalText": "走原有派工流程\n不受额度限制",
			"lineHeight": 1.25,
			"baseline": 31,
			"autoResize": true,
			"index": "a5",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "走原有派工流程\n不受额度限制"
		},
		{
			"id": "eaRjMXYBFBZ64TNbgIYf",
			"type": "arrow",
			"x": 350,
			"y": 160,
			"width": 60,
			"height": 12,
			"angle": 0,
			"strokeColor": "#868e96",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1270871257,
			"version": 2,
			"versionNonce": 1819423829,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "E2mvTZlI",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-60,
					-12
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "a6",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "E2mvTZlI",
			"type": "text",
			"x": 313,
			"y": 145.25,
			"width": 14,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1089870648,
			"version": 2,
			"versionNonce": 1916381051,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "否",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "eaRjMXYBFBZ64TNbgIYf",
			"originalText": "否",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "a7",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "否"
		},
		{
			"id": "2rbFtkde4MxI1P8kIvWg",
			"type": "rectangle",
			"x": 330,
			"y": 270,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#d0ebff",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1714209865,
			"version": 2,
			"versionNonce": 624091573,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "NLQktt88",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "a8",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "NLQktt88",
			"type": "text",
			"x": 392.52000427246094,
			"y": 283.25,
			"width": 114.95999145507812,
			"height": 37.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 267650874,
			"version": 2,
			"versionNonce": 623706139,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "选择系统: 法拉第\n选择派工类型",
			"fontSize": 15,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "2rbFtkde4MxI1P8kIvWg",
			"originalText": "选择系统: 法拉第\n选择派工类型",
			"lineHeight": 1.25,
			"baseline": 33.5,
			"autoResize": true,
			"index": "a9",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "选择系统: 法拉第\n选择派工类型"
		},
		{
			"id": "vMbLp6SfuceoglTZikFn",
			"type": "arrow",
			"x": 450,
			"y": 220,
			"width": 0,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 546001740,
			"version": 2,
			"versionNonce": 1345774357,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "bYPz1c48",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					0,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aA",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "bYPz1c48",
			"type": "text",
			"x": 443,
			"y": 236.25,
			"width": 14,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1831451612,
			"version": 2,
			"versionNonce": 493634747,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "是",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "vMbLp6SfuceoglTZikFn",
			"originalText": "是",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "aB",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "是"
		},
		{
			"id": "jBh3N0hzHDgr5mqEuoeY",
			"type": "diamond",
			"x": 350,
			"y": 384,
			"width": 200,
			"height": 120,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#fff3bf",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 12805890,
			"version": 2,
			"versionNonce": 1911797877,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "EZgmUalW",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aC",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "EZgmUalW",
			"type": "text",
			"x": 416.5050048828125,
			"y": 434.625,
			"width": 66.989990234375,
			"height": 18.75,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1107020882,
			"version": 2,
			"versionNonce": 580515163,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "派工类型?",
			"fontSize": 15,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "jBh3N0hzHDgr5mqEuoeY",
			"originalText": "派工类型?",
			"lineHeight": 1.25,
			"baseline": 14.75,
			"autoResize": true,
			"index": "aD",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "派工类型?"
		},
		{
			"id": "opAJL97WfYS8cLj3mV96",
			"type": "arrow",
			"x": 450,
			"y": 334,
			"width": 0,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 101826289,
			"version": 2,
			"versionNonce": 1680794069,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					0,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "aE",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "1dFkipyNEvObrrmItdoG",
			"type": "rectangle",
			"x": 160,
			"y": 554,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#fff4e6",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 2087184611,
			"version": 2,
			"versionNonce": 1678283259,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "gnnDMzqQ",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aF",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "gnnDMzqQ",
			"type": "text",
			"x": 226.94701385498047,
			"y": 568.5,
			"width": 106.10597229003906,
			"height": 35,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1384501992,
			"version": 2,
			"versionNonce": 738058037,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "工程/售后\n共享额度 5次/天",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "1dFkipyNEvObrrmItdoG",
			"originalText": "工程/售后\n共享额度 5次/天",
			"lineHeight": 1.25,
			"baseline": 31,
			"autoResize": true,
			"index": "aG",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "工程/售后\n共享额度 5次/天"
		},
		{
			"id": "cxa8sZJ2zdAmiYJIy6vX",
			"type": "rectangle",
			"x": 500,
			"y": 554,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#e3fafc",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1737982050,
			"version": 2,
			"versionNonce": 45783707,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "gFreYhhF",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aH",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "gFreYhhF",
			"type": "text",
			"x": 566.9470138549805,
			"y": 568.5,
			"width": 106.10597229003906,
			"height": 35,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1116873960,
			"version": 2,
			"versionNonce": 264646805,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "电试\n独立额度 5次/天",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "cxa8sZJ2zdAmiYJIy6vX",
			"originalText": "电试\n独立额度 5次/天",
			"lineHeight": 1.25,
			"baseline": 31,
			"autoResize": true,
			"index": "aI",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "电试\n独立额度 5次/天"
		},
		{
			"id": "-GfuYeIrXxCkNM3l6I9-",
			"type": "arrow",
			"x": 350,
			"y": 444,
			"width": 50,
			"height": 142,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1017774126,
			"version": 2,
			"versionNonce": 1533185851,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "ayfhEz2B",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					50,
					142
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aJ",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "ayfhEz2B",
			"type": "text",
			"x": 345.35350036621094,
			"y": 506.875,
			"width": 59.292999267578125,
			"height": 16.25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 152623380,
			"version": 2,
			"versionNonce": 1832736245,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "工程/售后",
			"fontSize": 13,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "-GfuYeIrXxCkNM3l6I9-",
			"originalText": "工程/售后",
			"lineHeight": 1.25,
			"baseline": 12.25,
			"autoResize": true,
			"index": "aK",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "工程/售后"
		},
		{
			"id": "XGYKrEGvLEcZAVmqdCHS",
			"type": "arrow",
			"x": 550,
			"y": 444,
			"width": 50,
			"height": 142,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1690973766,
			"version": 2,
			"versionNonce": 274919387,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "REoGOiOR",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-50,
					142
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aL",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "REoGOiOR",
			"type": "text",
			"x": 512,
			"y": 506.875,
			"width": 26,
			"height": 16.25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 229292120,
			"version": 2,
			"versionNonce": 1332268885,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "电试",
			"fontSize": 13,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "XGYKrEGvLEcZAVmqdCHS",
			"originalText": "电试",
			"lineHeight": 1.25,
			"baseline": 12.25,
			"autoResize": true,
			"index": "aM",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "电试"
		},
		{
			"id": "5WI5FI3xLLyX69NSBp57",
			"type": "rectangle",
			"x": 330,
			"y": 668,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#d0ebff",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1830170748,
			"version": 2,
			"versionNonce": 1406024827,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "i9E4IJpb",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aN",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "i9E4IJpb",
			"type": "text",
			"x": 397.5,
			"y": 681.25,
			"width": 105,
			"height": 37.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1747597791,
			"version": 2,
			"versionNonce": 1151212725,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "日历展示各日期\n剩余额度",
			"fontSize": 15,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "5WI5FI3xLLyX69NSBp57",
			"originalText": "日历展示各日期\n剩余额度",
			"lineHeight": 1.25,
			"baseline": 33.5,
			"autoResize": true,
			"index": "aO",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "日历展示各日期\n剩余额度"
		},
		{
			"id": "touU0IITFZtzZFKnV9Ds",
			"type": "arrow",
			"x": 280,
			"y": 618,
			"width": 150,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1962843499,
			"version": 2,
			"versionNonce": 1902572827,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					150,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "aP",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "i4NHXJZn6k5MWG6ecXrA",
			"type": "arrow",
			"x": 620,
			"y": 618,
			"width": 150,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 318859794,
			"version": 2,
			"versionNonce": 1954725397,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-150,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "aQ",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "YU1yBVML0AiaxvhDRXBt",
			"type": "rectangle",
			"x": 330,
			"y": 782,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#d0ebff",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1515446815,
			"version": 2,
			"versionNonce": 413112763,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "ZJE4PKUS",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aR",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "ZJE4PKUS",
			"type": "text",
			"x": 382.5,
			"y": 795.25,
			"width": 135,
			"height": 37.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1291808216,
			"version": 2,
			"versionNonce": 1980511093,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "选择派工日期和时间\n(支持跨天)",
			"fontSize": 15,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "YU1yBVML0AiaxvhDRXBt",
			"originalText": "选择派工日期和时间\n(支持跨天)",
			"lineHeight": 1.25,
			"baseline": 33.5,
			"autoResize": true,
			"index": "aS",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "选择派工日期和时间\n(支持跨天)"
		},
		{
			"id": "nEIz4-5C81C9jVnxbRo0",
			"type": "arrow",
			"x": 450,
			"y": 732,
			"width": 0,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 194187977,
			"version": 2,
			"versionNonce": 1721412187,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					0,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "aT",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "NTAO0Jx9BRLPOOSlLx96",
			"type": "diamond",
			"x": 350,
			"y": 896,
			"width": 200,
			"height": 120,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#fff3bf",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 934349501,
			"version": 2,
			"versionNonce": 1702886613,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "PUzuLn6T",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aU",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "PUzuLn6T",
			"type": "text",
			"x": 416.5050048828125,
			"y": 946.625,
			"width": 66.989990234375,
			"height": 18.75,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 2110867603,
			"version": 2,
			"versionNonce": 808101627,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "是否跨天?",
			"fontSize": 15,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "NTAO0Jx9BRLPOOSlLx96",
			"originalText": "是否跨天?",
			"lineHeight": 1.25,
			"baseline": 14.75,
			"autoResize": true,
			"index": "aV",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "是否跨天?"
		},
		{
			"id": "E5hH504YeZ6krx98YPev",
			"type": "arrow",
			"x": 450,
			"y": 846,
			"width": 0,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 597324766,
			"version": 2,
			"versionNonce": 980654645,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					0,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "aW",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "I1gta2r9GEn9U1Yu1x6E",
			"type": "rectangle",
			"x": 160,
			"y": 1066,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#d0ebff",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 2129411444,
			"version": 2,
			"versionNonce": 198315931,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "exhfE9yo",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aX",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "exhfE9yo",
			"type": "text",
			"x": 238,
			"y": 1080.5,
			"width": 84,
			"height": 35,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1652550394,
			"version": 2,
			"versionNonce": 1573686165,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "逐日校验\n所有日期额度",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "I1gta2r9GEn9U1Yu1x6E",
			"originalText": "逐日校验\n所有日期额度",
			"lineHeight": 1.25,
			"baseline": 31,
			"autoResize": true,
			"index": "aY",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "逐日校验\n所有日期额度"
		},
		{
			"id": "c4XRfpTR1Inh9QSgkGip",
			"type": "rectangle",
			"x": 500,
			"y": 1066,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#d0ebff",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 276350430,
			"version": 2,
			"versionNonce": 465790011,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "PyYnDgeA",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aZ",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "PyYnDgeA",
			"type": "text",
			"x": 578,
			"y": 1089.25,
			"width": 84,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 2155413,
			"version": 2,
			"versionNonce": 2143310069,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "校验当日额度",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "c4XRfpTR1Inh9QSgkGip",
			"originalText": "校验当日额度",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "aa",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "校验当日额度"
		},
		{
			"id": "cfZH8jaM5ZHVRjsiNdni",
			"type": "arrow",
			"x": 350,
			"y": 956,
			"width": 50,
			"height": 142,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 512662472,
			"version": 2,
			"versionNonce": 400631003,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "Lqt2nVr1",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					50,
					142
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "ab",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "Lqt2nVr1",
			"type": "text",
			"x": 368,
			"y": 1018.25,
			"width": 14,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1300462114,
			"version": 2,
			"versionNonce": 768895573,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "是",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "cfZH8jaM5ZHVRjsiNdni",
			"originalText": "是",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "ac",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "是"
		},
		{
			"id": "EtN-mtRBuTDBuc26M80R",
			"type": "arrow",
			"x": 550,
			"y": 956,
			"width": 50,
			"height": 142,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 427915510,
			"version": 2,
			"versionNonce": 398315899,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "mMzrfPxR",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-50,
					142
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "ad",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "mMzrfPxR",
			"type": "text",
			"x": 518,
			"y": 1018.25,
			"width": 14,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 558615669,
			"version": 2,
			"versionNonce": 685660085,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "否",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "EtN-mtRBuTDBuc26M80R",
			"originalText": "否",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "ae",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "否"
		},
		{
			"id": "V3lDhoErmLpfabtGXyi4",
			"type": "diamond",
			"x": 350,
			"y": 1180,
			"width": 200,
			"height": 120,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#fff3bf",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1332672291,
			"version": 2,
			"versionNonce": 479980059,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "ZQMSycBv",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "af",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "ZQMSycBv",
			"type": "text",
			"x": 416.5050048828125,
			"y": 1230.625,
			"width": 66.989990234375,
			"height": 18.75,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1620375922,
			"version": 2,
			"versionNonce": 2063833365,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "额度充足?",
			"fontSize": 15,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "V3lDhoErmLpfabtGXyi4",
			"originalText": "额度充足?",
			"lineHeight": 1.25,
			"baseline": 14.75,
			"autoResize": true,
			"index": "ag",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "额度充足?"
		},
		{
			"id": "y5AePf41gpeI6S57lPLW",
			"type": "arrow",
			"x": 280,
			"y": 1130,
			"width": 150,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1525675307,
			"version": 2,
			"versionNonce": 1533931195,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					150,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "ah",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "YBX-hVKDLHjef-E4SYyG",
			"type": "arrow",
			"x": 620,
			"y": 1130,
			"width": 150,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 748823163,
			"version": 2,
			"versionNonce": 1304014453,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-150,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "ai",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "eeu0veFTI6FaOMOlpbRs",
			"type": "rectangle",
			"x": 160,
			"y": 1350,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#ffe3e3",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1706951437,
			"version": 2,
			"versionNonce": 1188454235,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "SUgvS7eU",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aj",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "SUgvS7eU",
			"type": "text",
			"x": 238,
			"y": 1364.5,
			"width": 84,
			"height": 35,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 170368484,
			"version": 2,
			"versionNonce": 2143688661,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "禁止提交\n提示额度已满",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "eeu0veFTI6FaOMOlpbRs",
			"originalText": "禁止提交\n提示额度已满",
			"lineHeight": 1.25,
			"baseline": 31,
			"autoResize": true,
			"index": "ak",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "禁止提交\n提示额度已满"
		},
		{
			"id": "UlMD6mCV-pSgjw3a7RHU",
			"type": "rectangle",
			"x": 500,
			"y": 1350,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#d3f9d8",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1266502366,
			"version": 2,
			"versionNonce": 1495776251,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "3Ob5Zpnp",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "al",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "3Ob5Zpnp",
			"type": "text",
			"x": 557,
			"y": 1373.25,
			"width": 126,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1485050610,
			"version": 2,
			"versionNonce": 2067682613,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "提交成功，扣减额度",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "UlMD6mCV-pSgjw3a7RHU",
			"originalText": "提交成功，扣减额度",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "am",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "提交成功，扣减额度"
		},
		{
			"id": "1YNlmqmibkASrAYwSAE4",
			"type": "arrow",
			"x": 350,
			"y": 1240,
			"width": 50,
			"height": 142,
			"angle": 0,
			"strokeColor": "#e03131",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1845173299,
			"version": 2,
			"versionNonce": 509904027,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "XcdfkDHN",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					50,
					142
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "an",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "XcdfkDHN",
			"type": "text",
			"x": 368,
			"y": 1302.25,
			"width": 14,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1809329642,
			"version": 2,
			"versionNonce": 1267197589,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "否",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "1YNlmqmibkASrAYwSAE4",
			"originalText": "否",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "ao",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "否"
		},
		{
			"id": "LLpEhgTjdcjv4WXOQEyp",
			"type": "arrow",
			"x": 550,
			"y": 1240,
			"width": 50,
			"height": 142,
			"angle": 0,
			"strokeColor": "#2f9e44",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1607568279,
			"version": 2,
			"versionNonce": 2118072635,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "IqQIAQOk",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-50,
					142
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "ap",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "IqQIAQOk",
			"type": "text",
			"x": 518,
			"y": 1302.25,
			"width": 14,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1979217627,
			"version": 2,
			"versionNonce": 1935166453,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "是",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "LLpEhgTjdcjv4WXOQEyp",
			"originalText": "是",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "aq",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "是"
		},
		{
			"id": "kCgFPGgH1WYD99ZDd4pW",
			"type": "diamond",
			"x": 160,
			"y": 1464,
			"width": 200,
			"height": 120,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#fff3bf",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1953807736,
			"version": 2,
			"versionNonce": 1176132059,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "GqPBOVb3",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "ar",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "GqPBOVb3",
			"type": "text",
			"x": 232,
			"y": 1506.5,
			"width": 56,
			"height": 35,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 559528770,
			"version": 2,
			"versionNonce": 45417813,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "临时增加\n额度?",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "kCgFPGgH1WYD99ZDd4pW",
			"originalText": "临时增加\n额度?",
			"lineHeight": 1.25,
			"baseline": 31,
			"autoResize": true,
			"index": "as",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "临时增加\n额度?"
		},
		{
			"id": "DGE0Otx3PpJCf1z86tE1",
			"type": "arrow",
			"x": 280,
			"y": 1414,
			"width": 20,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 602370621,
			"version": 2,
			"versionNonce": 1929930363,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-20,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "at",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "3jybamI9WRRO8cOKbQoZ",
			"type": "rectangle",
			"x": -100,
			"y": 1489,
			"width": 200,
			"height": 56,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#e9ecef",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 2077201048,
			"version": 2,
			"versionNonce": 1827902133,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "W4zh26us",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "au",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "W4zh26us",
			"type": "text",
			"x": -63,
			"y": 1508.25,
			"width": 126,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1753873284,
			"version": 2,
			"versionNonce": 1003216667,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "后台管理员调整额度",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "3jybamI9WRRO8cOKbQoZ",
			"originalText": "后台管理员调整额度",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "av",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "后台管理员调整额度"
		},
		{
			"id": "q8pZVDyPrBXOQSQ0tduZ",
			"type": "arrow",
			"x": 160,
			"y": 1524,
			"width": 60,
			"height": 7,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 2082745786,
			"version": 2,
			"versionNonce": 515470357,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "f58XuPNA",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-60,
					-7
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "aw",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "f58XuPNA",
			"type": "text",
			"x": 123,
			"y": 1511.75,
			"width": 14,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 851235052,
			"version": 2,
			"versionNonce": 1524057019,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "是",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "q8pZVDyPrBXOQSQ0tduZ",
			"originalText": "是",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "ax",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "是"
		},
		{
			"id": "VKzcnk6rz9YKSPZVAgqN",
			"type": "arrow",
			"x": 0,
			"y": 1489,
			"width": 330,
			"height": 707,
			"angle": 0,
			"strokeColor": "#868e96",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 2060123006,
			"version": 2,
			"versionNonce": 1409034613,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					0,
					-707
				],
				[
					-330,
					-707
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "ay",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "E6LJ7Xlz5d5Fvm-BnHkh",
			"type": "ellipse",
			"x": 190,
			"y": 1634,
			"width": 110,
			"height": 45,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#e9ecef",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 99841812,
			"version": 2,
			"versionNonce": 407704667,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "9BOBl7Gp",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "az",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "9BOBl7Gp",
			"type": "text",
			"x": 229,
			"y": 1646.5,
			"width": 32,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 758765084,
			"version": 2,
			"versionNonce": 771830485,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "结束",
			"fontSize": 16,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "E6LJ7Xlz5d5Fvm-BnHkh",
			"originalText": "结束",
			"lineHeight": 1.25,
			"baseline": 16,
			"autoResize": true,
			"index": "b00",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "结束"
		},
		{
			"id": "B6LpS9-z4D7VHNL8Ybii",
			"type": "arrow",
			"x": 260,
			"y": 1584,
			"width": 15,
			"height": 50,
			"angle": 0,
			"strokeColor": "#868e96",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1775125950,
			"version": 2,
			"versionNonce": 335532283,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "fxXLSi6p",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-15,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "b01",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "fxXLSi6p",
			"type": "text",
			"x": 245.5,
			"y": 1600.25,
			"width": 14,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 560163981,
			"version": 2,
			"versionNonce": 157159477,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "否",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "B6LpS9-z4D7VHNL8Ybii",
			"originalText": "否",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "b02",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "否"
		},
		{
			"id": "S3MdiDiFyaAYQakOn7WA",
			"type": "diamond",
			"x": 500,
			"y": 1464,
			"width": 200,
			"height": 120,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#fff3bf",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1773136901,
			"version": 2,
			"versionNonce": 1332869531,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "91e5oknq",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "b03",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "91e5oknq",
			"type": "text",
			"x": 554.7380065917969,
			"y": 1515.25,
			"width": 90.52398681640625,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 292600676,
			"version": 2,
			"versionNonce": 335036821,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "派工后续状态?",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "S3MdiDiFyaAYQakOn7WA",
			"originalText": "派工后续状态?",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "b04",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "派工后续状态?"
		},
		{
			"id": "0gI852eEJpfFp-mr2xYk",
			"type": "arrow",
			"x": 620,
			"y": 1414,
			"width": 20,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1647297595,
			"version": 2,
			"versionNonce": 2010517051,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-20,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "b05",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "VVddINwmPqT-LzJ9cqL8",
			"type": "ellipse",
			"x": 800,
			"y": 1484,
			"width": 110,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#d3f9d8",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 272594687,
			"version": 2,
			"versionNonce": 587548405,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "WDwffmLk",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "b06",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "WDwffmLk",
			"type": "text",
			"x": 823,
			"y": 1499,
			"width": 64,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1677466193,
			"version": 2,
			"versionNonce": 1880002267,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "流程结束",
			"fontSize": 16,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "VVddINwmPqT-LzJ9cqL8",
			"originalText": "流程结束",
			"lineHeight": 1.25,
			"baseline": 16,
			"autoResize": true,
			"index": "b07",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "流程结束"
		},
		{
			"id": "t-ncyqwFewwYsHTon9Lb",
			"type": "arrow",
			"x": 700,
			"y": 1524,
			"width": 100,
			"height": 15,
			"angle": 0,
			"strokeColor": "#2f9e44",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 53140340,
			"version": 2,
			"versionNonce": 1225433173,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "n5z0VhqO",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					100,
					-15
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"customData": {
				"legacyTextWrap": true
			},
			"index": "b08",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "n5z0VhqO",
			"type": "text",
			"x": 736,
			"y": 1507.75,
			"width": 28,
			"height": 17.5,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1702087986,
			"version": 2,
			"versionNonce": 447609723,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "完成",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "t-ncyqwFewwYsHTon9Lb",
			"originalText": "完成",
			"lineHeight": 1.25,
			"baseline": 13.5,
			"autoResize": true,
			"index": "b09",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "完成"
		},
		{
			"id": "To0uauL4v-BMSbXukc8Y",
			"type": "rectangle",
			"x": 510,
			"y": 1634,
			"width": 240,
			"height": 64,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#fff4e6",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1248637789,
			"version": 2,
			"versionNonce": 735956405,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "TXeSoD1p",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 3
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "b0A",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "TXeSoD1p",
			"type": "text",
			"x": 580.1460113525391,
			"y": 1648.5,
			"width": 99.70797729492188,
			"height": 35,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 2046791211,
			"version": 2,
			"versionNonce": 1677120539,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "取消/作废/驳回\n自动回退额度",
			"fontSize": 14,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "To0uauL4v-BMSbXukc8Y",
			"originalText": "取消/作废/驳回\n自动回退额度",
			"lineHeight": 1.25,
			"baseline": 31,
			"autoResize": true,
			"index": "b0B",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "取消/作废/驳回\n自动回退额度"
		},
		{
			"id": "FUok0uQ2EfQKMLXxt6FV",
			"type": "arrow",
			"x": 600,
			"y": 1584,
			"width": 30,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 450084160,
			"version": 2,
			"versionNonce": 446975765,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					30,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "b0C",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "qSluc2XEDqGpWyKg1-vo",
			"type": "ellipse",
			"x": 570,
			"y": 1748,
			"width": 110,
			"height": 45,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "#e9ecef",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 1801548019,
			"version": 2,
			"versionNonce": 198940859,
			"isDeleted": false,
			"boundElements": [
				{
					"id": "4AqSJ0I5",
					"type": "text"
				}
			],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"customData": {
				"legacyTextWrap": true
			},
			"index": "b0D",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "4AqSJ0I5",
			"type": "text",
			"x": 609,
			"y": 1760.5,
			"width": 32,
			"height": 20,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 1,
			"roughness": 0,
			"opacity": 100,
			"seed": 50809334,
			"version": 2,
			"versionNonce": 1674814581,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": null,
			"groupIds": [],
			"text": "结束",
			"fontSize": 16,
			"fontFamily": 5,
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "qSluc2XEDqGpWyKg1-vo",
			"originalText": "结束",
			"lineHeight": 1.25,
			"baseline": 16,
			"autoResize": true,
			"index": "b0E",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false,
			"rawText": "结束"
		},
		{
			"id": "qi9Od8XhKSoTm1JRz1YI",
			"type": "arrow",
			"x": 630,
			"y": 1698,
			"width": 5,
			"height": 50,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"roughness": 0,
			"opacity": 100,
			"seed": 1462089293,
			"version": 2,
			"versionNonce": 805226843,
			"isDeleted": false,
			"boundElements": [],
			"updated": 1783481076425,
			"link": null,
			"locked": false,
			"roundness": {
				"type": 2
			},
			"groupIds": [],
			"points": [
				[
					0,
					0
				],
				[
					-5,
					50
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"index": "b0F",
			"strokeStyle": "solid",
			"frameId": null,
			"hasTextLink": false
		},
		{
			"id": "4cap8l61",
			"type": "arrow",
			"x": 450.11567305894516,
			"y": 55.49996851216883,
			"width": 0.7836000570857777,
			"height": 42.812772466771214,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"index": "b0G",
			"roundness": {
				"type": 2
			},
			"seed": 137250578,
			"version": 96,
			"versionNonce": 1465051474,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1783502515694,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.7836000570857777,
					42.812772466771214
				]
			],
			"startBinding": {
				"elementId": "SYYZ6WodYEurE3D4vhqB",
				"mode": "orbit",
				"fixedPoint": [
					0.5001,
					1
				]
			},
			"endBinding": {
				"elementId": "p6A00SU3Yvn7pHDz0I4u",
				"mode": "orbit",
				"fixedPoint": [
					0.505,
					0.03177083333333333
				]
			},
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"elbowed": false
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#1e1e1e",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "solid",
		"currentItemStrokeWidthKey": "bold",
		"currentItemStrokeVariability": "constant",
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 5,
		"currentItemFontSize": 20,
		"currentItemTextAlign": "left",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"currentItemArrowType": "round",
		"currentItemFrameRole": null,
		"scrollX": 268.00513182225933,
		"scrollY": 650.3501055477666,
		"zoom": {
			"value": 1.610984
		},
		"currentItemRoundness": "round",
		"gridSize": 20,
		"gridStep": 5,
		"gridModeEnabled": false,
		"gridColor": {
			"Bold": "rgba(217, 217, 217, 0.5)",
			"Regular": "rgba(230, 230, 230, 0.5)"
		},
		"currentStrokeOptions": null,
		"frameRendering": {
			"enabled": true,
			"clip": true,
			"name": true,
			"outline": true,
			"markerName": true,
			"markerEnabled": true
		},
		"objectsSnapModeEnabled": false,
		"activeTool": {
			"type": "selection",
			"customType": null,
			"locked": false,
			"fromSelection": false,
			"lastActiveTool": null
		},
		"disableContextMenu": false,
		"bindingPreference": "enabled",
		"isBindingEnabled": true,
		"isMidpointSnappingEnabled": true,
		"boxSelectionMode": "contain"
	},
	"files": {}
};InitialData.scrollToContent=true;App=()=>{const e=React.useRef(null),t=React.useRef(null),[n,i]=React.useState({width:void 0,height:void 0});return React.useEffect(()=>{i({width:t.current.getBoundingClientRect().width,height:t.current.getBoundingClientRect().height});const e=()=>{i({width:t.current.getBoundingClientRect().width,height:t.current.getBoundingClientRect().height})};return window.addEventListener("resize",e),()=>window.removeEventListener("resize",e)},[t]),React.createElement(React.Fragment,null,React.createElement("div",{className:"excalidraw-wrapper",ref:t},React.createElement(ExcalidrawLib.Excalidraw,{ref:e,width:n.width,height:n.height,initialData:InitialData,viewModeEnabled:!0,zenModeEnabled:!0,gridModeEnabled:!1})))},excalidrawWrapper=document.getElementById("OA派工优化-流程图excalidraw.md1");ReactDOM.render(React.createElement(App),excalidrawWrapper);})();</script>

## 七、提示与异常处理

| 场景        | 处理方式                             |
| --------- | -------------------------------- |
| 当日额度已满    | 工程/售后提示"今日工程/售后额度已满"；电试提示"今日电试额度已满" |
| 跨天时某天额度不足 | 提示"X月X日 工程/售后或电试额度已满"               |
| 取消/作废/驳回派工 | 额度自动回退，无需额外操作                    |
| 额度不足但仍需派工 | 由调度人员临时增加额度后再操作                 |

## 八、已确认事项

| 问题           | 结论                  |
| ------------ | ------------------- |
| 额度调整是否需要审批   | 不需要                 |
| 是否需要操作日志/审计  | 调整记录可追溯（操作人/时间/前后值），无需审批流程 |
| 派工页面是否展示额度   | 日历上需要展示当日剩余/已用额度    |
| 是否需要额度预警通知   | 不需要                 |
| 历史派工是否纳入额度计算 | 不需要，历史数据由调度人员自行调整额度 |
