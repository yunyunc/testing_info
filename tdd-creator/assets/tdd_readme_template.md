# <测试名称>

## 概述

- UUT：
- 类型：规则 / 组件 / API / 回归 / 基准
- 版本：v0.1.0
- 日期：2026-03-13

## 背景与动机

- 问题描述：
- 创建原因：
- 关联规则 / 模块 / Issue：

## Code Fact Sheet

- 【事实】入口点：
- 【事实】关键调用链：
- 【事实】阈值 / 容差 / 默认值：
- 【推断】主要失败模式：
- 【建议】测试 seam / 数据策略：

## 测试目标

- 

## Test Matrix

| ID | Level | Axis | 类型 | 目的 | 输入 | 断言点(>=2) | CI节奏 | 失败信号 |
|----|-------|------|------|------|------|-------------|--------|----------|
| M01 | Unit | Correctness | 主干 |  |  |  | PR |  |

## 10 类虚构输入

| Case | 类别 | 具体输入 | 断言1 | 断言2 | 覆盖风险 |
|------|------|----------|-------|-------|----------|
| 01 | empty |  |  |  |  |
| 02 | minimal_valid |  |  |  |  |
| 03 | nominal_typical |  |  |  |  |
| 04 | duplicate |  |  |  |  |
| 05 | near_threshold_low |  |  |  |  |
| 06 | near_threshold_high |  |  |  |  |
| 07 | degenerate |  |  |  |  |
| 08 | noisy |  |  |  |  |
| 09 | large_scale |  |  |  |  |
| 10 | invalid |  |  |  |  |

## Determinism & Tolerance

- 随机性：固定 `seed = 42`
- 排序稳定性：
- 浮点容差：
- 失败信号：

## 数据说明

- `tests/tdd/<name>/data/synth/`
- `tests/tdd/<name>/data/real/`
- `tests/tdd/<name>/data/golden/`

## 相关代码

- 被测模块：
- 测试代码：
- CMake 注册：

## CI 建议

- PR：
- Nightly：
- Weekly：

## 历史记录

- 2026-03-13: 创建。
