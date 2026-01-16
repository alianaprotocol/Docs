# 🌟 Aliana Protocol Whitepaper

> **Building a Decentralized Financial Autonomy Based on Mathematical Trust**
>
> | | |
> | :--- | :--- |
> | **Version** | `1.1.0 (Detailed)` |
> | **Release Date** | `2025` |
> | **Core Philosophy** | `Algorithmic Autonomy` `Proof of Contribution` `Health Auto-Adjustment` |

---

## 📖 Table of Contents

- [1. Executive Summary](#1-executive-summary)
- [2. The Protocol (Core Mechanics)](#2-the-protocol-core-mechanics)
    - [2.1 The Investment Model: The 25-Day Golden Cycle](#21-the-investment-model-the-25-day-golden-cycle)
    - [2.2 Dynamic Tier System](#22-dynamic-tier-system)
    - [2.3 Fund Operation Rules](#23-fund-operation-rules)
- [3. Market Incentive Network (Referral Network)](#3-market-incentive-network-referral-network)
    - [3.1 20-Level Unilevel Rewards](#31-20-level-unilevel-rewards)
    - [3.2 Leadership Differential Bonus](#32-leadership-differential-bonus)
- [4. The Moat: Health Auto-Adjustment (HAA) System](#4-the-moat-health-auto-adjustment-haa-system)
    - [4.1 Core Monitoring Metrics](#41-core-monitoring-metrics)
    - [4.2 5-Level Defense State Machine](#42-5-level-defense-state-machine)
- [5. ALI Tokenomics](#5-ali-tokenomics)
    - [5.1 Basic Information](#51-basic-information)
    - [5.2 Output Mechanism: Proof of Contribution (PoC)](#52-output-mechanism-proof-of-contribution-poc)
    - [5.3 veALI Model (Governance & Boost)](#53-veali-model-governance--boost)
- [6. Technical Security & Transparency](#6-technical-security--transparency)
- [7. Conclusion](#7-conclusion)

---

## 1. Executive Summary 📝

**Aliana Protocol** is a next-generation Decentralized Finance (DeFi) protocol operating on the blockchain (**BSC/Arbitrum**). It reconstructs the trust mechanism of traditional finance through smart contracts, addressing three core pain points:

1.  **Black Box Operations** ⬛️
    *   *Problem*: Traditional platforms lack transparency in fund flows.
    *   *Solution*: Aliana operates **entirely on-chain**, making every transaction traceable.

2.  **Short Lifecycle** 📉
    *   *Problem*: Financial schemes often collapse due to panic runs.
    *   *Solution*: Aliana introduces the **Health Auto-Adjustment (HAA)** system, which automatically adjusts parameters like a biological immune system to resist risks.

3.  **Unfair Benefit Distribution** ⚖️
    *   *Problem*: Early participants take all the dividends.
    *   *Solution*: Aliana adopts the **Proof of Contribution (PoC)** mining mechanism, ensuring long-term builders gain governance rights (ALI) through time decay and contribution weighting.

---

## 2. The Protocol (Core Mechanics) ⚙️

Aliana adopts a high-liquidity repayment model featuring **"Daily Principal Release + Profit Stacking"**, significantly reducing capital risk for users.

### 2.1 The Investment Model: The 25-Day Golden Cycle ⏳

All financial orders in the protocol are settled based on a complete cycle of **25 days**.

*   **Base Principal Release Rate**: `4.00% / Day`
    *   *Mechanism*: Regardless of tier, the user's principal is returned linearly to the "Withdrawable Balance" at a rate of 4% per day over 25 days.
    *   *Advantage*: Capital recovery begins on Day 1, without waiting for maturity.

*   **Base Profit Rate**: `1.00% - 2.00% / Day`
    *   *Mechanism*: Additional pure profit is distributed based on the user's tier.

**Yield Composition Formula**:

```text
Daily Claimable Amount = Principal × (4% + Tier Profit Rate)
```

### 2.2 Dynamic Tier System 📊

The protocol encourages large capital users and governance token holders by establishing 5 yield tiers (LV1 - LV5).

**Tier Criteria**:
User tiers are determined by **"Effective Active Amount"**:

```text
Effective Active Amount = Current Active Principal + (vALI Holdings ÷ 10)
```

> 💡 *Note: This means even without adding more USDT, users can upgrade their account tier and enjoy high-net-worth benefits by staking ALI tokens to obtain vALI.*

| Tier | Threshold (Effective Active Amount) | Profit Rate | Principal Release | **Total Daily** | **Total ROI (25 Days)** |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **LV 1** | 0 - 499 USDT | 1.00% | 4.00% | **5.00%** | **125.00%** |
| **LV 2** | ≥ 500 USDT | 1.25% | 4.00% | **5.25%** | **131.25%** |
| **LV 3** | ≥ 1,000 USDT | 1.50% | 4.00% | **5.50%** | **137.50%** |
| **LV 4** | ≥ 2,500 USDT | 1.75% | 4.00% | **5.75%** | **143.75%** |
| **LV 5** | ≥ 5,000 USDT | 2.00% | 4.00% | **6.00%** | **150.00%** |

### 2.3 Fund Operation Rules 🔄

*   **Deposit**: Minimum **1 USDT**. Each deposit generates an independent "Order" with its own 25-day cycle calculation.
*   **Withdraw**: Minimum **1 USDT**. Withdrawals are instant (subject to chain congestion).
*   **Compound**: Reinvesting daily yields directly back into the protocol.
    *   *Advantage 1*: Saves Gas fees on withdrawing and re-depositing.
    *   *Advantage 2*: **Compound mining weight is 1.5x that of deposits**, earning more ALI tokens.

---

## 3. Market Incentive Network (Referral Network) 🤝

Aliana has established a deep, performance-based referral network designed to reward true community builders.

### 3.1 20-Level Unilevel Rewards 🪜

Referral relationships are immutable once bound. Uplines receive a percentage of their downlines' **Deposit/Compound Amounts** as commission.

*   **Levels 1-5**: `1.5%` | `1.25%` | `1.0%` | `1.0%` | `1.0%`
    *   *Unlock Condition*: Personal active investment ≥ 10 USDT.
*   **Levels 6-10**: `1.0%` each
    *   *Unlock Condition*: Have ≥ 2 Qualified Directs (Direct referral with active amount ≥ 100 USDT).
*   **Levels 11-15**: `0.5%` each
    *   *Unlock Condition*: Have ≥ 3 Qualified Directs.
*   **Levels 16-20**: `0.25%` each
    *   *Unlock Condition*: Have ≥ 5 Qualified Directs.

### 3.2 Leadership Differential Bonus 🏆

Additional **Infinite Depth** differential bonuses are provided for leaders who build massive teams.
*   *Mechanism*: Differential based. For example, if you are V3 (1.5%) and your downline is V1 (0.5%), you receive 1.0% (1.5 - 0.5) of their team's new volume.

| Rank | Title | Team Size Req | Team Volume Req | Differential % |
| :--- | :--- | :--- | :--- | :--- |
| **Rank 1** | Inviter | 50 | 2,500 USDT | 0.5% |
| **Rank 2** | Promoter | 100 | 5,000 USDT | 1.0% |
| **Rank 3** | Leader | 200 | 10,000 USDT | 1.5% |
| **Rank 4** | Partner | 400 | 15,000 USDT | 2.0% |
| **Rank 5** | Star | 800 | 25,000 USDT | 3.0% |
| **Rank 6** | Royal Star | 1,500 | 50,000 USDT | 4.0% |
| **Rank 7** | Crown Star | 2,500 | 100,000 USDT | 5.0% |

---

## 4. The Moat: Health Auto-Adjustment (HAA) System 🛡️

This is the core technology distinguishing Aliana from all competitors. The protocol features a built-in "Central Bank" level regulation algorithm.

### 4.1 Core Monitoring Metrics 📊

The system calculates two values in real-time:

1.  **FCR (Funds Coverage Ratio)**:
    ```text
    FCR = Current Contract Balance ÷ Total Principal Payable
    ```

2.  **NFT (Net Flow Trend)**: Net flow trend over the past 24 hours.
    ```text
    NFT = 24h Total Deposits - 24h Total Withdrawals
    ```

### 4.2 5-Level Defense State Machine 🚦

Based on FCR and NFT performance, the protocol automatically switches operation modes:

| State | Trigger Scenario (Example) | Protocol Behavior Change | Impact on User |
| :--- | :--- | :--- | :--- |
| **🟢 Normal** | Abundant funds, net inflow | Standard parameters | Instant withdrawals, smooth experience |
| **🔵 Watch** | Inflow slows down | Minor adjustment to reserve retention | Unnoticeable to users, system accumulates reserves |
| **🟡 Throttle** | Short-term net outflow | **Raise Min Withdrawal** (e.g., 10->50 USDT) | Prevents small, high-frequency panic withdrawals |
| **🟠 Stabilize** | Sustained outflow, level drop | **Pause Dynamic Bonuses** (Referral/Diff) | Prioritizes principal safety, marketing yields suspended |
| **🔴 Emergency** | Extreme market conditions | **Trigger Circuit Breaker** | All outflows paused, awaits DAO vote via TimeLock for rescue |

> *Note: All state switches are driven entirely by on-chain data with no human intervention, preventing "rug pulls".*

---

## 5. ALI Tokenomics 🪙

**ALI** is the carrier of governance and value in the Aliana ecosystem.

### 5.1 Basic Information ℹ️

*   **Token Name**: Aliana Token
*   **Symbol**: `ALI`
*   **Max Supply**: 1,000,000,000 (1 Billion)
*   **Standard**: ERC-20 (Supports ERC20Votes, ERC20Permit)

### 5.2 Output Mechanism: Proof of Contribution (PoC) ⛏️

Users receive free ALI token rewards while participating in Aliana financial products.

*   **Output Formula**:
    ```
    Mint Amount = Transaction Amount × Current Rate × Action Weight
    ```
*   **Action Weight**:
    *   **Deposit**: `100%` Weight
    *   **Compound**: `150%` Weight (Encourages compounding)
*   **Deflationary Model (Epoch Halving)**:
    *   Every **90 Days** constitutes an Epoch.
    *   In each new Epoch, the output rate automatically **decays by 10%**.
    *   *Example*: Initial phase 100 USDT mines 10 ALI; after 90 days, it mines 9 ALI. Early participation yields cheaper chips.

### 5.3 veALI Model (Governance & Boost) 🗳️

Referencing Curve's veToken model, ALI must be staked to unlock its potential.

1.  **Staking Logic**: Users lock ALI (1 week - 4 years) to obtain **vALI**. Longer lock times yield more vALI (Max 4 years = 1:2 ratio).
2.  **Right I - Governance**: 1 vALI = 1 Vote. Used to decide key protocol parameters (e.g., modifying fees, adjusting tier yield rates).
3.  **Right II - Yield Boost**: Held vALI counts towards "Effective Active Amount", directly helping users **upgrade tiers (LV1 -> LV5) for free** in the main protocol without depositing more USDT.

---

## 6. Technical Security & Transparency 🔒

*   **Fully Open Source**: Contract code will be fully public on GitHub, subject to community audit.
*   **TimeLock**: All protocol parameter modifications (e.g., admin actions) must pass a **24-hour** public notice period. This gives the community enough time to react if malicious operations are detected.
*   **Multi-Sig**: Initial management rights are held by a multi-signature wallet, eliminating single points of failure.
*   **Fund Segregation**: The Reserve Vault is physically separated from the Main Pool. Even if the main contract encounters extreme logic errors, the reserves remain safe.

---

## 7. Conclusion 🚀

Aliana Protocol is not just a financial tool; it is a social experiment in **Algorithmic Trust**.

*   For **Investors**: It provides safe returns with high liquidity and daily principal release.
*   For **Promoters**: It offers deep differential rewards and a long-term stable business environment.
*   For **Geeks & Believers**: It provides ALI tokens and DAO governance rights, letting you truly own the platform.

**Join Aliana, Witness the Power of Math.**
