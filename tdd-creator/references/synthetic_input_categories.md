# DFM 场景下的 10 类虚构输入

每次使用此 skill，都要把下面 10 类输入实例化为具体 DFM 数据。不要只写名字，要写出具体 JSON 字段、构造参数、阈值和值域。

## 1. empty

目的：

- 验证空特征、空 JSON、空上下文、空 shape 不崩溃

DFM 例子：

- `std::vector<std::shared_ptr<Feature>>{}`
- `{ "parts": [] }`
- 未加载 STEP 的 `DFMContext`

断言方向：

- `EXPECT_NO_THROW`
- 返回空违例、特定错误码或空报告

## 2. minimal_valid

目的：

- 验证刚好满足前置条件的最小输入

DFM 例子：

- 单个 `ThroughHoleFeature`
- 单个 pocket subgroup
- 最小可解析 `Feature.json`

断言方向：

- 成功返回
- 关键字段值精确匹配

## 3. nominal_typical

目的：

- 覆盖日常最常见零件或特征组合

DFM 例子：

- 5 到 20 个 feature 的 `Feature.json`
- 1 个 `Model.stp` + 1 个标准配置组合

断言方向：

- 违例数稳定
- 输出字段完整

## 4. duplicate/repeat

目的：

- 防止重复特征、重复 ID、重复调用带来重复违例或状态污染

DFM 例子：

- 两个相同 `feature.id`
- 重复 `shapeIDs`
- 同一 `DFMContext` 重复 `dfm_analyze`

断言方向：

- 结果不重复膨胀
- 重复执行前后结果一致或按设计可重置

## 5. near_threshold_low (-eps)

目的：

- 覆盖阈值下界分支

DFM 例子：

- 小孔径：`diameter = 0.49` 或 `2.99`
- 壁厚：`thickness = threshold - 1e-6`
- 角度：`89.999`

断言方向：

- 走低于阈值分支
- 严重度、提示、测量值正确

## 6. near_threshold_high (+eps)

目的：

- 覆盖阈值上界分支

DFM 例子：

- 小孔径：`diameter = 0.50`、`0.51`、`3.00`、`3.01`
- 壁厚：`threshold + 1e-6`
- 角度：`90.001`

断言方向：

- 走高于或等于阈值分支
- 与下界用例产生不同结果

## 7. degenerate

目的：

- 验证退化几何不会导致 NaN、崩溃或 UB

DFM 例子：

- 半径为 0 的孔
- 深度为 0 的 pocket
- 轴向长度接近 0 的向量
- 空 `affected_face_ids`

断言方向：

- 不崩溃
- 返回空结果、错误码或显式退化标记

## 8. noisy

目的：

- 验证数值扰动下的鲁棒性和确定性

DFM 例子：

- `Axis` 加 `±1e-6` 扰动
- 尺寸值从 `10.0` 变成 `10.000001`
- 固定 `seed = 42` 的坐标扰动

断言方向：

- 分类不跳变
- 相同 seed 得到相同结果

## 9. large_scale

目的：

- 验证规模增长时的性能和资源行为

DFM 例子：

- 100 到 10k 个 feature
- 多 pocket / 多 hole 组合
- 批量重复加载同一 STEP 以覆盖缓存

断言方向：

- 时间低于基线
- 结果规模与输入规模一致

## 10. invalid

目的：

- 验证非法输入被拒绝，不产生未定义行为

DFM 例子：

- 缺失 `parts`
- `value = "NaN"` / `"Inf"`
- 未知 feature type
- 不存在的配置路径
- `nullptr` 上下文和空路径

断言方向：

- 返回错误码或抛出异常
- 错误信息可定位

## 使用规则

1. 把 10 类全部落成具体输入，不要缺项。
2. 每组至少 2 条断言，且断言类型尽量不同。
3. 优先用 table-driven 风格组织。
4. 浮点比较显式写容差。
5. 随机扰动固定 `seed = 42`。
