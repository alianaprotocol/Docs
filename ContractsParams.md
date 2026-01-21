# Aliana 全合约参数手册

## AlianaProtocol
- 文件: [AlianaProtocol.sol](file:///Users/takkuentan/Desktop/aliana/contracts/AlianaProtocol.sol)
- 常量参数（设计目标配置，当前合约版本将在后续升级中对齐）:
  - INVESTMENT_CYCLE_DAYS = 50 — 投资结算周期（天）
  - MIN_DEPOSIT_AMOUNT = 10 ether — 用户单笔最小存入 USDT（18 位精度）
  - MAX_DEPOSIT_AMOUNT = 1000 ether — 用户单笔最大存入 USDT（18 位精度）
  - MIN_WITHDRAW_AMOUNT = 1 ether — 用户单笔最小提现 USDT（18 位精度）
  - REFERRER_MINIMUM_DEPOSIT = 10 ether — 成为推荐人所需最低入金（资格阈值）
  - ADMIN_FEE_PERCENT = 500 — 管理费率（BPS，500=5%）
  - BASE_DAILY_RETURN_PERCENT = 200 — 基础日收益率（BPS，200=2%）
  - PERCENT_DIVISOR = 10000 — 百分比基点分母（BPS 标准）
  - ONE_DAY_IN_SECONDS = 1 days — 一天的秒数（时间计算基准）
  - MAX_USER_POSITIONS = 100 — 单用户最大持仓数量上限
  - MAX_REFERRAL_DEPTH = 100 — 推荐关系最大层级深度
  - TIER_DAILY_ROI_PERCENTS = [100,125,150,175,200] — 分层日收益率（BPS）
  - TIER_DEPOSIT_THRESHOLDS = [0,500e18,1000e18,2500e18,5000e18] — 分层入金阈值（USDT，e18）
  - LEADERSHIP_BONUS_PERCENTS = [50,100,150,200,300,400,500] — 领导力奖励比例（BPS）
  - LEADERSHIP_TEAM_SIZE_REQUIREMENTS = [50,100,200,400,800,1500,2500] — 团队人数要求（人）
  - LEADERSHIP_TEAM_VOLUME_REQUIREMENTS = [2500e18,5000e18,10000e18,15000e18,25000e18,50000e18,100000e18] — 团队累计入金要求（USDT，e18）
  - MIN_DIRECTS_LEVEL_6_10 = 2 — 6–10 级领导奖励所需直推人数
  - MIN_DIRECTS_LEVEL_11_15 = 3 — 11–15 级领导奖励所需直推人数
  - MIN_DIRECTS_LEVEL_16_20 = 5 — 16–20 级领导奖励所需直推人数
  - MAX_LEADERSHIP_DEPTH = 100 — 领导力奖励最大计算深度
  - MAX_LEADERSHIP_SHARE_BPS = 500 — 领导力奖励占比上限（BPS，500=5%）
- 地址与外部依赖:
  - USDT_TOKEN (immutable) — 协议使用的稳定币地址（不可变）
  - TREASURY_WALLET (constant) — 财政/金库钱包地址（常量）
  - miningController (configurable) — 挖矿与奖励控制器，可配置
  - veAliana (configurable) — 投票锁仓（vALI）合约地址，可配置
  - healthController (configurable) — 健康/风险控制器，可配置
  - reserveVault (configurable) — 准备金金库地址，可配置
- 动态健康参数:
  - adminFeeBps (默认: 500) — 管理费率（BPS）
  - reserveFeeBps (默认: 0) — 准备金保留费率（BPS），影响注入比例
  - minWithdrawAmount (默认: 1e18) — 最小提现额（USDT，e18）
  - dailyUserWithdrawLimit (默认: 0) — 用户日提现上限（USDT，0 表示无限制）
  - healthMode (默认: 0) — 当前健康模式枚举值
- 设置入口: [setDynamicParams](file:///Users/takkuentan/Desktop/aliana/contracts/AlianaProtocol.sol#L227-L234), [setHealthMode](file:///Users/takkuentan/Desktop/aliana/contracts/AlianaProtocol.sol#L236-L239)
- 统计与映射:
  - dailyNetFlow(day) → int256 — 协议每日净流量（入金-出金）
  - userDailyWithdraws(user, day) → uint256 — 用户每日提现累计（USDT）
  - userProfiles / userRewards / userWithdrawalStats — 用户配置、收益与提现统计映射
- 金库注入接口:
  - injectFund(amount) — 接收 ReserveVault 注入 USDT 并更新资金池

## HealthController
- 文件: [HealthController.sol](file:///Users/takkuentan/Desktop/aliana/contracts/controllers/HealthController.sol)
- 角色常量:
  - KEEPER_ROLE — 定时维护执行权限（机器人/运营）
  - POLICY_MAKER_ROLE — 风险策略与参数调整权限
- 公开参数:
  - protocol (immutable) — 主协议地址（不可变）
  - reserveVault (immutable) — 准备金库地址（不可变）
  - stableToken (immutable) — USDT 地址（不可变）
  - launchTimestamp — 协议启动时间戳（模式计算基准）
  - currentMode — 当前健康模式（影响动态参数）
  - thresholds — 模式切换触发阈值设置（策略门槛）
  - modeParams(mode) — 不同模式下的 AlianaProtocol 参数配置
- 默认模式参数:
  - Normal — 常态：低保留费、无限日提现
    - 数值：reserveFeeBps=200，minWithdrawAmount=1e18，dailyUserWithdrawLimit=0
  - Watch — 观察：提高保留费、设置适中提现门槛与上限
    - 数值：reserveFeeBps=500，minWithdrawAmount=20e18，dailyUserWithdrawLimit=800e18
  - Throttle — 限流：高保留费、较高门槛与较低上限
    - 数值：reserveFeeBps=1000，minWithdrawAmount=50e18，dailyUserWithdrawLimit=400e18
  - Stabilize — 稳定：更高保留费、严格门槛与较小上限
    - 数值：reserveFeeBps=2000，minWithdrawAmount=100e18，dailyUserWithdrawLimit=100e18
  - Emergency — 紧急：极高保留费、极高门槛、几乎禁止提现
    - 数值：reserveFeeBps=5000，minWithdrawAmount=500e18，dailyUserWithdrawLimit=1
- 维护入口:
  - maintain(totalEstimatedObligation) — 评估负债并按需切换模式/触发注入
  - setModeParams(mode, params) — 设置指定模式的参数集
  - setThresholds(thresholds) — 设置各模式触发阈值
  - setLaunchTimestamp(ts) — 设置启动时间基准

## MiningController
- 文件: [MiningController.sol](file:///Users/takkuentan/Desktop/aliana/contracts/controllers/MiningController.sol)
- 角色常量:
  - PROTOCOL_ROLE — 仅主协议可调用通知接口
- 公开参数与常量:
  - aliToken (immutable) — ALI 代币地址（不可变）
  - EPOCH_DURATION = 30 days — 产出周期时长
  - DECAY_BPS = 300 — 每周期产出衰减率（BPS，3%）
  - BASE_RATE = 1e16 — 基础产出率（每 1 USDT 产出 0.01 ALI）
  - currentEpoch — 当前周期序号
  - epochStartTime — 当前周期开始时间戳
  - currentRate — 当前实际产出率（考虑衰减）
  - DEPOSIT_WEIGHT = 100 — 存款事件权重（1.0x）
  - COMPOUND_WEIGHT = 125 — 复投事件权重（1.25x）
  - WEIGHT_DIVISOR = 100 — 权重分母（标准化）
- 通知入口:
  - notifyDeposit(user, amount) — 记录存款事件并计算奖励
  - notifyCompound(user, amount) — 记录复投事件并计算奖励

## ReserveVault
- 文件: [ReserveVault.sol](file:///Users/takkuentan/Desktop/aliana/contracts/core/ReserveVault.sol)
- 角色常量:
  - MANAGER_ROLE — 金库操作管理权限（仅授权执行）
- 公开参数:
  - protocol (immutable) — 主协议地址（不可变，唯一注入目标）
  - stableToken (immutable) — USDT 地址（不可变，唯一币种）
- 资金流接口:
  - deposit(amount) — 接收 USDT 入金到金库
  - injectToProtocol(amount) — 仅向主协议转账并调用 injectFund（唯一出金路径）

## AlianaToken (ALI)
- 文件: [AlianaToken.sol](file:///Users/takkuentan/Desktop/aliana/contracts/token/AlianaToken.sol)
- 角色常量:
  - MINTER_ROLE — 允许铸造 ALI 的角色
- 公开参数/构造:
  - 初始总量 — 10 亿 ALI（按 decimals 放大）一次性铸造到初始持有人
- 接口:
  - mint(to, amount) — 为地址铸造 ALI（仅 MINTER_ROLE）

## VeAliana (vALI)
- 文件: [VeAliana.sol](file:///Users/takkuentan/Desktop/aliana/contracts/token/VeAliana.sol)
- 公开参数与常量:
  - aliToken (immutable) — 关联的 ALI 代币地址（不可变）
  - MIN_LOCK_DURATION = 1 weeks — 最短锁仓时间
  - MAX_LOCK_DURATION = 4 * 365 days — 最长锁仓时间
  - SCALE = 1e18 — 计算缩放因子
  - locks(user) — 用户锁仓信息（金额/到期/对应 vALI）
- 计算接口:
  - getBoostAmount(user) — 治理加权：vALI 余额的十分之一
- 交互:
  - deposit(amount, lockDuration) — 锁仓 ALI 并获得 vALI
  - withdraw() — 到期解锁并提取 ALI

## AlianaGovernor
- 文件: [AlianaGovernor.sol](file:///Users/takkuentan/Desktop/aliana/contracts/governance/AlianaGovernor.sol)
- 核心治理参数（构造参数决定）:
  - votingDelay = 1 days — 提案创建到可投票的延迟
  - votingPeriod = 1 weeks — 投票持续时间
  - proposalThreshold = 100000e18 — 发起提案所需 vALI（e18）
  - quorumFraction = 4 — 法定人数比例（百分比）
  - timelock — 关联的时间锁控制器地址

## AlianaTimelock
- 文件: [AlianaTimelock.sol](file:///Users/takkuentan/Desktop/aliana/contracts/governance/AlianaTimelock.sol)
- 构造参数:
  - minDelay — 执行延迟（确保治理更改有缓冲期）
  - proposers[] — 具有提出队列权限的账户集合
  - executors[] — 具有执行队列事务权限的账户集合
  - admin — 初始管理员（可移交至治理）

---

## 按使用场景的关键参数视图

> 说明：本节从“怎么用”的角度，将高频使用和高风险参数按场景归类，并为每个参数标注：
> - 风险：高/中/低（对资金安全、用户体验的影响）
> - 可变性：常量（部署锁死）/构造期固定/链上可调

### 场景 1：用户存款、提现与推荐

- 存款门槛与限制（影响用户进入门槛）
  - AlianaProtocol.MIN_DEPOSIT_AMOUNT — [风险：中][可变性：常量]
  - AlianaProtocol.MAX_DEPOSIT_AMOUNT — [风险：中][可变性：常量]
  - AlianaProtocol.MAX_USER_POSITIONS — [风险：中][可变性：常量]

- 提现门槛与限制（直接影响用户流动性体验）
  - AlianaProtocol.MIN_WITHDRAW_AMOUNT — [风险：高][可变性：常量]
  - AlianaProtocol.minWithdrawAmount（动态）— [风险：高][可变性：链上可调，经 HealthController]
  - AlianaProtocol.dailyUserWithdrawLimit — [风险：高][可变性：链上可调，经 HealthController]

- 推荐与团队参数（影响拉新激励结构）
  - AlianaProtocol.REFERRER_MINIMUM_DEPOSIT — [风险：中][可变性：常量]
  - AlianaProtocol.MAX_REFERRAL_DEPTH — [风险：中][可变性：常量]
  - AlianaProtocol.MIN_DIRECTS_LEVEL_6_10/11_15/16_20 — [风险：中][可变性：常量]
  - AlianaProtocol.LEADERSHIP_* 数组（奖励比例、团队人数、团队体量）— [风险：中][可变性：常量]

> 使用建议：
> - 产品设计阶段重点关注本组参数，决定用户门槛和裂变强度。
> - 运营变化（如活动）优先通过动态费率/限额调节，而不是改动这些常量。

### 场景 2：收益率、挖矿与代币产出

- 协议基础收益率（USDT 端）
  - AlianaProtocol.BASE_DAILY_RETURN_PERCENT — [风险：高][可变性：常量]
  - AlianaProtocol.TIER_DAILY_ROI_PERCENTS — [风险：高][可变性：常量]
  - AlianaProtocol.TIER_DEPOSIT_THRESHOLDS — [风险：中][可变性：常量]

- 挖矿/代币产出曲线（ALI 端）
  - MiningController.BASE_RATE — [风险：高][可变性：构造期固定]
  - MiningController.DECAY_BPS — [风险：中][可变性：构造期固定]
  - MiningController.EPOCH_DURATION — [风险：中][可变性：构造期固定]
  - MiningController.DEPOSIT_WEIGHT / COMPOUND_WEIGHT — [风险：中][可变性：构造期固定]

> 使用建议：
> - 设计经济模型时，一次性敲定本组参数，部署后不再调整，避免预期管理风险。
> - 需要临时调节收益时，优先使用 HealthController 相关动态参数，而不是重新部署主协议。

#### 推荐发行窗口与参数方案（PoC 挖矿）

- 设计目标（基于总量 10 亿 ALI）
  - 挖矿额度：**6 亿 ALI**（约 60% 通过 PoC 挖出）
  - 主发行窗口：**前 6 年释放约 80–85% 挖矿额度**（≈ 4.8–5.1 亿）
  - 完整发行窗口：**10 年左右累计释放约 95% 挖矿额度**（≈ 5.7 亿）
  - 10 年之后：保留小幅长尾产出，主要用于维持活跃度，对总量影响可忽略

- 对应参数建议（在现有实现基础上）
  - EPOCH_DURATION：30 days（保持不变，方便按月理解）
  - DECAY_BPS：300（每 30 天衰减 3%）
  - BASE_RATE：1e16（初始 0.01 ALI / 1 USDT，可视业务体量微调）
  - 行为权重：DEPOSIT_WEIGHT=100，COMPOUND_WEIGHT=125（复投略优于单次入金）
  - 挖矿总上限（建议新增变量）：miningCap = 600_000_000e18

- 目标发行节奏（以 6 亿 ALI 挖矿额度为参考）
  - 第 0–2 年：≈ 35%（约 2.1 亿 ALI）
  - 第 2–4 年：≈ 25%（约 1.5 亿 ALI）
  - 第 4–6 年：≈ 20%（约 1.2 亿 ALI）
  - 第 6–10 年：≈ 15%（约 0.9 亿 ALI）
  - 第 10 年以后：≈ 5%（约 0.3 亿 ALI，作为长尾缓慢释放）

> 实现要点：
> - 在 MiningController 中增加 `miningCap` 与 `totalMined`，在 `_mintReward` 中确保 `totalMined + finalReward <= miningCap`。
> - 若未来需调整 BASE_RATE/DECAY_BPS，需重新评估上述年度分布，但整体“6 年主窗口、10 年完整窗口”的结构可保持不变。

### 场景 3：风险控制与健康模式

- 动态健康参数（主协议侧）
  - AlianaProtocol.adminFeeBps — [风险：高][可变性：链上可调，经 HealthController]
  - AlianaProtocol.reserveFeeBps — [风险：高][可变性：链上可调，经 HealthController]
  - AlianaProtocol.minWithdrawAmount — [风险：高][可变性：链上可调]
  - AlianaProtocol.dailyUserWithdrawLimit — [风险：高][可变性：链上可调]
  - AlianaProtocol.healthMode — [风险：中][可变性：链上可调]

- HealthController 模式与阈值
  - HealthController.currentMode — [风险：高][可变性：链上可调，经角色]
  - HealthController.thresholds（各模式触发点）— [风险：高][可变性：链上可调]
  - HealthController.modeParams(mode) — [风险：高][可变性：链上可调]
  - HealthController.launchTimestamp — [风险：低][可变性：链上可调]

> 使用建议：
> - 将本组参数视为“风控面板”，仅授予 POLICY_MAKER_ROLE/治理合约。
> - 任何大幅调整前，需要对用户提现体验和资金流向做模拟评估。

### 场景 4：资金金库与注入主协议

- 资金存储与唯一资金流
  - ReserveVault.stableToken — [风险：高][可变性：构造期固定]
  - ReserveVault.protocol — [风险：高][可变性：构造期固定]
  - ReserveVault.deposit(amount) — [风险：中][可变性：函数行为在代码中固定]
  - ReserveVault.injectToProtocol(amount) — [风险：高][可变性：函数行为在代码中固定]

- 主协议注入入口
  - AlianaProtocol.injectFund(amount) — [风险：高][可变性：函数行为在代码中固定]

> 使用建议：
> - stableToken 与 protocol 在部署时一次性确认，视为“强绑定关系”，不通过治理修改。
> - 对金库出入金进行链上监控，可直接关注 ReserveVault 与 AlianaProtocol 之间的转账与事件。

### 场景 5：治理、投票与时间锁

- 治理投票参数
  - AlianaGovernor.votingDelay — [风险：中][可变性：构造期固定]
  - AlianaGovernor.votingPeriod — [风险：中][可变性：构造期固定]
  - AlianaGovernor.proposalThreshold — [风险：高][可变性：构造期固定]
  - AlianaGovernor.quorumFraction — [风险：高][可变性：构造期固定]

- 时间锁与执行安全
  - AlianaTimelock.minDelay — [风险：高][可变性：构造期固定]
  - AlianaTimelock.proposers[] — [风险：高][可变性：链上可调或移交]
  - AlianaTimelock.executors[] — [风险：高][可变性：链上可调或移交]
  - AlianaTimelock.admin — [风险：高][可变性：可移交，最终推荐移交给治理]

> 使用建议：
> - 上线主网前慎重选择治理与时间锁参数，确保既能响应调整需求，又不会让单方快速修改关键参数。
> - 实际运维时，建议通过治理升级流程来变更 proposer/executor，而不是私下多签直接操作。

### 场景 6：代币与锁仓治理权

- ALI 代币
  - AlianaToken.MINTER_ROLE — [风险：高][可变性：链上可调（角色可变更）]
  - 初始总量（构造时铸造到 initialHolder）— [风险：高][可变性：构造期固定]

- vALI 锁仓治理
  - VeAliana.MIN_LOCK_DURATION / MAX_LOCK_DURATION — [风险：中][可变性：构造期固定]
  - VeAliana.getBoostAmount — [风险：中][可变性：函数逻辑固定]
  - VeAliana.locks(user) — [风险：中][可变性：随用户操作动态变化]

> 使用建议：
> - 严格管理 MINTER_ROLE 的持有者（通常应为治理合约或多签）。
> - 通过宣传 MIN_LOCK_DURATION / MAX_LOCK_DURATION 和 boost 机制，引导长期锁仓提高治理参与度。
