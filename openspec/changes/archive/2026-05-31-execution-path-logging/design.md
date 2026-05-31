## Context

当前核心日志文档 5 章，§3 为 grep 日志清单，§5 为经验式排查指南。codegraph 提供完整调用链数据，可以做到从代码推导日志表现而非猜测。

## Goals / Non-Goals

**Goals:**
- 每条核心能力调用链的每步日志追踪
- 异常传播路径→日志表现映射
- 日志覆盖率量化
- 故障排查从经验→代码推导

**Non-Goals:**
- 不改变非核心模块的日志处理
- 不影响手动模式下的日志生成
- 不分析第三方 SDK 内部日志

## Decisions

### D1: 执行路径日志追踪 (§3)

基于 codegraph 的 entry→core→leaf 调用链，逐步骤提取日志：

```markdown
### createOrder — 执行路径日志追踪

| 步骤 | 方法 | 日志存在 | 级别 | 日志内容 | 文件:行号 |
|------|------|:---:|------|---------|----------|
| 1 | submit() | ✅ | INFO | "Order submitted" | OrderActivity.kt:56 |
| 2 | createOrder() | ✅ | DEBUG | "Creating order: {id}" | OrderVM.kt:34 |
| 3 | create() | ✅ | INFO | "Order #{id} begin" | OrderService.java:67 |
| 4 | validateStock() | ❌ | — | [日志缺失] | OrderValidator.kt:12 |
| ... | ... | ... | ... | ... | ... |

**日志覆盖率**: 5/7 = 71%
```

### D2: 错误传播与异常日志 (§4)

```markdown
### createOrder — 错误传播分析

| 步骤 | 方法 | 可能异常 | 捕获? | 日志表现 | 传播方式 |
|------|------|---------|:---:|---------|---------|
| 4 | validateStock() | StockNotFoundException | ✅ | [WARN] "Stock: {SKU}" | return false |
| 5 | calculatePrice() | ArithmeticException | ❌ | [无日志] | throws↑ crash |
| 6 | saveToDB() | SQLException | ✅ | [ERROR] "DB: {msg}" | wrap→OrderEx |
```

### D3: 故障排查决策树 (§8)

从代码推导，每步的可能失败模式映射到日志特征：

```markdown
### 故障: [ERROR] "DB write failed" @ OrderDao.kt:67

可能原因:
1. 连接超时 → 验证 ConnectionTimeout 日志 → 检查连接池配置
2. 主键冲突 → 验证 orderId 是否重复 → 检查幂等逻辑
3. 磁盘满 → 检查系统日志 df -h → 清理磁盘
```

### D4: 章节重组

| 新编号 | 内容 | 来源 |
|--------|------|------|
| §1 | 修订历史 | 不变 |
| §2 | 核心能力定义+调用链 | 原§2 扩 |
| §3 ★ | 执行路径日志追踪 | NEW |
| §4 ★ | 错误传播与异常日志 | NEW |
| §5 ★ | 日志覆盖评估 | NEW |
| §6 | 核心功能日志清单 | 原§3 增"步骤编号"列 |
| §7 | 日志级别说明与排查过滤 | 原§4 |
| §8 ★ | 故障排查决策树 | 原§5 改为代码推导 |
