<!-- solve-problem:ed9f1f86-0a78-43ea-8e49-45c611b96c94 -->
# High-priority shill resistance under contingency

A [Tangle Solve problem](https://solve.tangle.tools/problems/ed9f1f86-0a78-43ea-8e49-45c611b96c94).

## What is the problem?

Setting: a transaction fee mechanism (TFM) for a blockchain in which execution is parallelized via a *limit scheduler* — transactions declare resource limits (compute, memory, storage), and the block producer schedules transactions subject to resource capacity constraints. Users submit priority fees; ordering within the block depends on priority fee per unit (or some analogous score). "Contingency" refers to uncertain execution: a transaction's actual resource consumption is random (heavy-tailed), discovered only at execution time.

The *shill/padding attack*: the proposer (or a bidder) submits shill transactions with inflated resource limits ("padding"), consuming block capacity to shift the scarcity profile and inflate the effective priority-fee cutoff for real transactions, thereby extracting more revenue or manipulating ordering. Shill resistance means the TFM's equilibria are unaffected by such self-dealing (the proposer cannot profit from bidding against honest users via sybils — cf. burning/strict-subsidy rules in the TFM literature à la Roughgarden).

The open problem: characterize tight tradeoffs among (i) **risk allocation** — who bears the risk of contingency (proposer vs. users paying for limits vs. burned fees), (ii) **burning** — fees destroyed rather than paid to the proposer, which is the standard tool deterring shills, and (iii) **responsiveness** — how quickly/order-faithfully the scheduler adapts priority fees to realized demand — when priority-fee distributions are **heavy-tailed**, in a **parameterized** regime with a **limit scheduler** (padding caps finite). Deliverables: upper/lower bounds on burning needed for shill resistance as a function of the scheduler's padding limit and the tail index; tightness (matching constructions).

*Ambiguity note:* the census is terse; the exact model in "Perils of Parallelism" (verify) defines the parameterized OW-TFM conditions this builds on. Formalizing precisely is part of the task.

## Why does it matter?

High-priority congestion (MEV-heavy blocks, NFT mints, sequencer ordering) magnifies the payoff of padding: a small capacity distortion moves the marginal clearing fee a lot when the fee distribution has mass in the tail. A tight tradeoff theorem would tell L1/L2 designers (Solana-style parallel runtimes, Ethereum L2s with multidimensional fees) exactly how much fee burning suffices, instead of ad-hoc parameter choices. If it stays open, deployed schedulers either over-burn (hurting users) or remain manipulable by sequencers shilling to reorder or tax high-value transactions.

## What's already known?

- The base result is the (fictional-flagged; verify content) **"Perils of Parallelism"** (Wadhwa, Yaish, Zhang, Nayak, 2026), which gives *parameterized* shill-resistance conditions for an "OW-TFM" class under parallel execution; the census says the present problem is explicitly "beyond" its parameterization — i.e., its conditions cover light-tailed or bounded cases and degrade unboundedly under heavy tails.
- Classical TFM theory: EIP-1559-style burning yields incentive compatibility and approximate shill resistance in single-resource, deterministic-execution models (Roughgarden 2020–21; Chung, Shi, etc.). These do not model multiresource limits or contingency.
- Known folk results: with heavy-tailed valuations, order-statistics-based cutoffs have infinite or unbounded variance, so any burning rule calibrated to expected revenue fails in the tail; standard "strict subsidy" arguments break because a proposer's padding cost is a *capacity* cost, not a fee cost.
- Nothing is known about tightness: no matching lower bound showing heavy tails force more burning, and no construction achieving responsiveness with bounded burn.

## What would count as solving it?

1. **Model formalization:** a precise TFM with limit scheduler, stochastic resource consumption with regularly varying tails (index α), burning rule b(·), and a shill-resistance definition (propader's padded-block profit ≤ honest-block profit, in expectation or w.h.p.).
2. **Upper bound:** a mechanism/parameter regime proving shill resistance with burning budget B(α, padding limit L), quantifying the tradeoff with responsiveness (e.g., ordering distortion ≤ ε).
3. **Lower bound:** a proof that any responsive, shill-resistant mechanism under tail index α and padding limit L requires burning ≥ B′(α, L), with B′ matching B up to constants.
4. Optionally: parameterized-complexity-style hardness, or an impossibility showing no mechanism achieves all three desiderata simultaneously in some regime.

## How to start

1. Read *Perils of Parallelism* (arXiv:2604.04193) Sections on the OW-TFM shill-resistance conditions; extract the exact parameterization this problem must extend (verify it exists and note its gaps).
2. Write down the simplest nontrivial instance: one resource, two-transaction blocks, proposer can pad one shill with limit L; solve for the burning threshold exactly under Pareto(α) priority fees. This reveals the order statistics driving the tradeoff.
3. Use extreme-value theory: the marginal order statistic of heavy-tailed fees dominates the burning requirement; connect to regular variation and the Fisher–Tippett–Gnedenko theorem for tail-index-dependent bounds.
4. Main obstacle: shill *capacity costs* interact with realized (contingent) consumption, so the attack's cost is itself random — handle via stop-loss/trimmed expectations before attempting tightness.

## Key references

- Wadhwa, S., Yaish, G., Zhang, S., Nayak, K. (2026). *Perils of Parallelism*. arXiv:2604.04193 — establishes parameterized shill-resistance conditions for parallel-execution TFMs (the baseline this problem extends). [Link](https://arxiv.org/abs/2604.04193) *(verify — citation taken from census record; confirm authors/details)*.
- Roughgarden, T. (2021). "Transaction Fee Mechanism Design." *EC/JACM* — foundational TFM incentive-compatibility and shill-resistance framework via fee burning.
- Chung, H.-Chun, Shi, E. (2023). "Foundations of Transaction Fee Mechanism Design." — formal sybil-proofness notions and impossibility results for burning-based TFMs.
- (Optional) Resnick, S., *Heavy-Tail Phenomena* — technical toolkit for regular variation and tail order statistics.

## Warnings

- The "Perils of Parallelism" reference comes from the census record and could not be independently verified here; confirm it exists and read its actual model before building on its "OW-TFM" terminology.
- Do not import the deterministic-execution shill-resistance proofs verbatim: contingency makes the proposer's attack cost state-dependent, which is where standard arguments silently fail.
- "Responsiveness" is undefined in the census; you must define it (candidate: sensitivity of ordering to fee changes) and check the referee accepts the definition.
- Watch for trivial resolutions: burning everything (100%) trivially achieves shill resistance — the content is entirely in the tradeoff tightness.

---
*`BCGT-000291` · flagged for review*

## Acceptance guidance

1. **Model formalization:** a precise TFM with limit scheduler, stochastic resource consumption with regularly varying tails (index α), burning rule b(·), and a shill-resistance definition (propader's padded-block profit ≤ honest-block profit, in expectation or w.h.p.).
2. **Upper bound:** a mechanism/parameter regime proving shill resistance with burning budget B(α, padding limit L), quantifying the tradeoff with responsiveness (e.g., ordering distortion ≤ ε).
3. **Lower bound:** a proof that any responsive, shill-resistant mechanism under tail index α and padding limit L requires burning ≥ B′(α, L), with B′ matching B up to constants.
4. Optionally: parameterized-complexity-style hardness, or an impossibility showing no mechanism achieves all three desiderata simultaneously in some regime.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/ed9f1f86-0a78-43ea-8e49-45c611b96c94?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000291.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000291)
