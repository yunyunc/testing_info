---
name: tdd-creator
description: Generate or revise DFM-specific TDD assets for the C++ DFM project at C:\\Users\\Administrator\\Desktop\\DFM. Use when Codex needs to create test matrices, gtest code, TDD documentation, synthetic or real datasets, golden summaries, benchmark or sanitizer plans, or CI guidance for DFM rules, geometry analyzers, feature loaders, C API, Python bindings, or end-to-end pipeline regression cases.
---

# tdd-creator

为 DFM 项目生成可落地的 TDD 资产，不产出脱离项目现实的通用模板。

## 先接受这些事实

- DFM 仓库使用 `gtest`，不是 `doctest`
- 构建入口是 `tests/CMakeLists.txt`，通过 `gtest_discover_tests` 注册
- 常见输入是 `data/Model.stp`、`data/Feature.json`
- 常见配置是 `config/rules.json`、`config/tool_library.json`、`config/machine_profile.json`
- 规则说明文档放在 `tdd/`
- 规则与组件测试代码放在 `tests/unit/`
- 流水线回归测试代码放在 `tests/regression/`
- 性能基准放在 `benchmarks/`
- 自包含测试数据优先放在 `tests/tdd/<name>/data/`

如需项目事实细节，读取 `references/dfm_project_profile.md`。

## 工作流

### 1. 先分类 UUT

先判断被测对象属于哪一类，再选模板：

- 规则/组件：`src/core/rules/*`、`src/core/geometry/*`、`src/core/features/*`
- API：`src/api/*`、`include/dfm_api.h`、`python/dfm_bindings.cpp`
- 回归流水线：完整 DFM 分析、报告生成、Golden 对比
- 性能/稳定性：`benchmarks/*`、并行/缓存/Sanitizer/Fuzz 入口

### 2. 只读侦察，产出 Code Fact Sheet

只写【事实】，不要脑补：

- 入口函数、关键调用链、依赖配置
- 关键数据结构、阈值、容差、默认值
- 现有测试位置、测试过滤方式、数据路径
- 是否已有中间态观测点；若没有，标记需要 test seam

输出时强制区分：

- 【事实】来自仓库代码或文档
- 【推断】基于事实得出的结论
- 【建议】下一步测试策略或代码改动

### 3. 先给 Test Matrix，再写测试

必须输出表格：

| ID | Level | Axis | 类型 | 目的 | 输入来源 | 断言点(>=2) | CI节奏 | 失败信号 |
|----|-------|------|------|------|----------|-------------|--------|----------|

最低要求：

- `Unit/Correctness` >= 5
- `Unit/Robustness` >= 5，其中 near-threshold >= 2
- `Invariant/Property` >= 1
- `Metamorphic` >= 1
- `Integration/Dataflow` >= 1
- `Golden/Real` >= 1
- `E2E` >= 2，至少 1 成功 + 1 受控失败
- `Safety` >= 1，说明 ASan/UBSan 或等价方案入口
- `Perf` >= 1，给出可执行基线

### 4. 实例化 10 类虚构输入

读取 `references/synthetic_input_categories.md`，把 10 类输入全部落成具体 DFM 数据。

硬规则：

- 每组都有具体输入，不允许 TODO
- 每组至少 2 条有效断言
- 优先 table-driven
- 随机扰动固定 `seed = 42`
- 浮点比较写明 `EXPECT_NEAR` 容差

### 5. 选择输出位置

按 UUT 类型落盘，不要把所有东西堆进同一路径。

#### 规则/组件

- 测试代码：`tests/unit/test_<name>.cpp`
- TDD 文档：`tdd/<domain>/<name>/README.md`
- 数据：`tests/tdd/<name>/data/{synth,real,golden}`

#### API

- 测试代码：`tests/unit/test_<name>.cpp`
- TDD 文档：`tdd/api/<name>/README.md`
- 数据：`tests/tdd/<name>/data/{synth,real,golden}`

#### 回归流水线

- 测试代码：`tests/regression/test_<name>.cpp`
- TDD 文档：`tdd/regression/<name>/README.md`
- 数据：`tests/tdd/<name>/data/{synth,real,golden}`

#### 性能

- 基准代码：`benchmarks/bench_<name>.cpp`
- TDD 文档：`tdd/benchmarks/<name>/README.md`
- 数据：`tests/tdd/<name>/data/{real,golden}`

如需目录草图，读取 `assets/dir_template.txt`。

### 6. 选模板，不要重写样板

- 规则/组件模板：`assets/test_template.cpp`
- API 模板：`assets/api_test_template.cpp`
- 流水线回归模板：`assets/pipeline_regression_template.cpp`
- README 模板：`assets/tdd_readme_template.md`
- CMake 追加片段：`assets/cmake_snippet.txt`

### 7. DFM 特有断言

优先断言这些稳定字段：

- `rule_id`
- `rule_name`
- `severity`
- `measured_value`
- `threshold`
- `affected_face_ids`
- `suggestions`
- `summary.total_violations`
- `summary.red_count`
- `summary.yellow_count`

对于 API，再补：

- 错误码
- 空指针/空路径行为
- 多上下文隔离
- 并行/缓存开关一致性

对于回归，再补：

- 输出 JSON 结构
- Golden 摘要指标
- 确定性
- 报告中违例计数守恒

### 8. 高风险模块必须写 seam 和稳定性计划

对解析、拓扑、布尔、几何采样、遮挡、底切、STEP 读取等高风险模块：

- 指定 `TraceSink`、`DebugSnapshot` 或结构化日志中的一种
- 给出 Sanitizer 入口
- 给出 Fuzz/corpus 建议入口
- 说明真实零件或 Golden 摘要的来源

### 9. CI 建议必须贴合 DFM

- PR：新增 `gtest` 单测、关键 invariant/metamorphic、小规模数据流测试
- Nightly：真实数据、Golden、Sanitizer、完整流水线
- Weekly：基准回归、Fuzz、多平台构建

## 资源导航

- `references/dfm_project_profile.md`：DFM 项目目录、输入输出、构建和测试事实
- `references/synthetic_input_categories.md`：DFM 语境下的 10 类虚构输入
- `references/dfm_uut_patterns.md`：按 UUT 分类的测试关注点和输出位置
