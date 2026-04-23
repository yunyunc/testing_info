# DFM 项目画像

## 仓库事实

- 仓库路径：`C:\Users\Administrator\Desktop\DFM`
- 语言：C++17
- 构建：CMake + vcpkg
- 单元测试框架：`gtest`
- 基准框架：Google Benchmark

## 主要目录

| 路径 | 作用 |
|------|------|
| `src/core/rules/` | 17 条 DFM 规则 |
| `src/core/geometry/` | STEP、圆角、壁厚、底切等几何分析器 |
| `src/core/features/` | 特征加载与增强 |
| `src/api/` | C API 实现 |
| `python/` | Python 绑定 |
| `tests/unit/` | 单元测试 |
| `tests/regression/` | 回归测试 |
| `benchmarks/` | 性能基准 |
| `tdd/` | 规则级 TDD 文档 |
| `tests/tdd/` | 自包含测试数据的优先落点 |

## 常见输入输出

### 输入

- `data/Model.stp`
- `data/Feature.json`
- `config/rules.json`
- `config/tool_library.json`
- `config/machine_profile.json`

### 输出

- `report.json`
- `DFMReport::ToJSONString()` 的 JSON 字符串

## 测试注册事实

- `tests/CMakeLists.txt` 中 `UNIT_TEST_SOURCES` 手工列出单测源文件
- `tests/CMakeLists.txt` 中 `REGRESSION_TEST_SOURCES` 手工列出回归源文件
- `gtest_discover_tests(dfm_unit_tests)` / `gtest_discover_tests(dfm_regression_tests)` 负责注册到 CTest
- `DFM_TEST_DATA_DIR` 通过 `target_compile_definitions` 注入

## 推荐输出映射

| UUT 类型 | 测试代码 | 文档 | 数据 |
|----------|----------|------|------|
| 规则/组件 | `tests/unit/test_<name>.cpp` | `tdd/<domain>/<name>/README.md` | `tests/tdd/<name>/data/` |
| API | `tests/unit/test_<name>.cpp` | `tdd/api/<name>/README.md` | `tests/tdd/<name>/data/` |
| 回归 | `tests/regression/test_<name>.cpp` | `tdd/regression/<name>/README.md` | `tests/tdd/<name>/data/` |
| 基准 | `benchmarks/bench_<name>.cpp` | `tdd/benchmarks/<name>/README.md` | `tests/tdd/<name>/data/` |

## 常用命令

```powershell
cmake -B build -S .
cmake --build build --config Release
ctest --test-dir build -C Release --output-on-failure
.\build\Release\dfm_unit_tests.exe --gtest_filter="*SmallDiameter*"
```
