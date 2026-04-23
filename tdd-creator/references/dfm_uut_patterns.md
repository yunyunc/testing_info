# DFM UUT 模式

## 1. 规则 / 组件

适用对象：

- `src/core/rules/*`
- `src/core/geometry/*`
- `src/core/features/*`

关注点：

- 阈值边界
- 类型过滤
- 空输入 / null / 配置缺失
- `Violation` 字段完整性
- 几何依赖规则的 shape / face id 稳定性

建议输出：

- `tests/unit/test_<name>.cpp`
- `tdd/<domain>/<name>/README.md`
- `tests/tdd/<name>/data/`

## 2. API

适用对象：

- `include/dfm_api.h`
- `src/api/dfm_api.cpp`
- `python/dfm_bindings.cpp`

关注点：

- 空上下文 / 空路径 / 非法参数
- 文件加载错误码
- 分析前后状态转换
- 多上下文隔离
- 并行 / 缓存开关一致性

建议输出：

- `tests/unit/test_<name>.cpp`
- `tdd/api/<name>/README.md`
- `tests/tdd/<name>/data/`

## 3. 回归流水线

适用对象：

- STEP + Feature + Config 到 `DFMReport`
- Golden 输出稳定性

关注点：

- JSON 结构稳定
- `summary` 计数守恒
- Golden 摘要变化
- 多次运行确定性
- 受控失败路径

建议输出：

- `tests/regression/test_<name>.cpp`
- `tdd/regression/<name>/README.md`
- `tests/tdd/<name>/data/`

## 4. 基准 / 稳定性

适用对象：

- `benchmarks/*`
- 并行规则执行
- 缓存命中路径
- Sanitizer / Fuzz 入口

关注点：

- 时间基线
- 内存或缓存行为
- 线程数、命中率、吞吐
- 崩溃与 UB

建议输出：

- `benchmarks/bench_<name>.cpp`
- `tdd/benchmarks/<name>/README.md`
- `tests/tdd/<name>/data/`
