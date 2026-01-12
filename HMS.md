# 多层次资金健康自动调节系统 (Health Monitor System)

> **当前状态**：已在 `AlianaProtocol` 与 `HealthController` 合约中实现。
> **核心目标**：通过链上实时监控偿付率与净流量，自动调节“提现限流”与“储备金留存”参数，维持协议长期生存能力。

---

## 1. 系统架构

系统由三个核心组件构成，实现监控、决策与执行的闭环：

1.  **AlianaProtocol (执行层)**
    *   持有资金与用户账本。
    *   内置 `DynamicParams`（动态参数）钩子，在提现时执行限流与费率扣除。
    *   记录每日净流量 (`dailyNetFlow`)。
2.  **HealthController (决策层)**
    *   定义 5 种健康模式 (`HealthMode`)。
    *   存储各模式对应的参数配置。
    *   包含 `maintain()` 函数，根据偿付率 (`SolvencyRatio`) 和净流量 (`NetFlow`) 判定当前模式。
3.  **Keeper / DAO (触发层)**
    *   `Keeper`: 定期调用 `maintain()` 触发状态更新。
    *   `DAO`: 可通过 `POLICY_MAKER_ROLE` 调整模式阈值与参数配置。

---

## 2. 核心监控指标

### 2.1 偿付率 (Solvency Ratio)
衡量当前资金池对潜在负债的覆盖能力。
*   **公式**: `SolvencyRatio = (ContractBalance * 10000) / TotalEstimatedObligation`
*   **基准**: 100% (10000 bps) 表示完全覆盖。

### 2.2 净流量 (Net Flow)
衡量资金的流入流出趋势。
*   **数据源**: `AlianaProtocol.dailyNetFlow(day)`
*   **统计**: 每日 `Deposit - Withdraw` 的净值。

---

## 3. 健康模式与自动调节 (Health Modes)

系统定义了 5 种健康模式，随风险等级升高，逐步收紧提现限制并增加储备金留存。

| 模式 (Mode) | 触发条件 (阈值) | 储备金留存费 (Reserve Fee) | 最小提现额 (Min Withdraw) | 单人日限额 (Daily Limit) | 说明 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **0. Normal** | Solvency ≥ 80% | **2%** | **1 USDT** | **无限制** | 正常运行状态 |
| **1. Watch** | Solvency < 80% | **5%** | **20 USDT** | **800 USDT** | 早期预警，轻微收紧 |
| **2. Throttle** | Solvency < 60% | **10%** | **50 USDT** | **400 USDT** | 抗挤兑，显著限流 |
| **3. Stabilize** | Solvency < 40% | **20%** | **100 USDT** | **100 USDT** | 强力稳态，保生存 |
| **4. Emergency** | Solvency < 20% | **50%** | **500 USDT** | **0 (暂停)** | 熔断保护，暂停提现 |

*注：若发生大额日净流出 (> 10,000 USDT)，即使偿付率高，也会强制降级至 `Watch` 模式预警。*

---

## 4. 调节机制详解

### 4.1 动态参数 (Dynamic Params)
当模式切换时，`HealthController` 会自动调用主协议的 `setDynamicParams` 更新以下参数：

1.  **reserveFeeBps**: 提现时额外扣除并转入 `ReserveVault` 的比例（不影响本金，仅减少当次提现到手金额）。
2.  **minWithdrawAmount**: 提高小额提现门槛，减少碎片化资金流出与 Gas 消耗。
3.  **dailyUserWithdrawLimit**: 限制每个账户每日可提现的最大金额，防止大户挤兑。

### 4.2 模式切换逻辑 (Hysteresis)
*   **升级 (Upgrade)**: 当偿付率跌破阈值，立即切换至高风险模式。
*   **降级 (Downgrade)**: 只有当偿付率回升且净流出改善时，才会由 Keeper 触发回到低风险模式。

---

## 5. 治理与安全

### 5.1 权限管理
*   **KEEPER_ROLE**: 仅能调用 `maintain()` 更新状态，无法修改参数配置。
*   **POLICY_MAKER_ROLE (DAO)**: 可以调整各模式的具体参数（如将 Normal 模式的限额改为 5000 U）或调整触发阈值。

### 5.2 透明度
*   所有模式切换触发 `HealthModeChanged` 事件。
*   所有参数更新触发 `ParamsUpdated` 事件。
*   前端可实时展示当前模式与限制，让用户明确知晓当前协议状态。

---

## 6. 合约交互接口

### 用户/前端查询
*   `protocol.healthMode()`: 获取当前模式 (0-4)。
*   `protocol.dailyUserWithdrawLimit()`: 获取当前个人日限额。
*   `protocol.getRemainingDepositQuota(user)`: 获取剩余入金配额。

### 运维交互
*   `healthController.maintain(obligation)`: Keeper 定期执行（建议每日或资金变动剧烈时）。
