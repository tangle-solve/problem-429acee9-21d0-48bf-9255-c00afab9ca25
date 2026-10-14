<!-- solve-problem:429acee9-21d0-48bf-9255-c00afab9ca25 -->
# Discrete fees with heterogeneous transaction sizes

A [Tangle Solve problem](https://solve.tangle.tools/problems/429acee9-21d0-48bf-9255-c00afab9ca25).

## What is the problem?

**Setting.** A transaction fee mechanism (TFM) is the protocol by which a block producer (miner/validator, assumed strategic and possibly colluding with users) selects which of the submitted transactions to include in a block of bounded size, and how much each included user pays. Users submit bids; the mechanism is a pair (allocation rule, payment rule). The desiderata, from the TFM literature (Roughgarden 2021), are: **user incentive compatibility (UIC)** — truthful bidding is optimal for each user; **miner incentive compatibility (MIC)** — the miner cannot profit by deviating (e.g., adding fake transactions, omitting bids); and **collusion resistance (c-t-IC / side-contract-proofness)** — no coalition of the miner plus up to c users can profit via side payments and strategic bidding.

**The open problem.** Existing constructions that achieve collusion resistance in some form — notably Gafni and Yaish (2024), who circumvent continuous-domain impossibility results by restricting bids to a discrete grid — assume a single transaction type occupying one unit of block capacity. The target is to extend discrete-fee collusion-resistant mechanisms to:

1. **Heterogeneous transaction sizes** — transactions occupy arbitrary (or multiple-of-a-unit) sizes, and the block is a knapsack;
2. **Dependencies** — transactions with precedence constraints (e.g., a spend depends on its funding transaction);
3. **Strategic splitting** — users who can decompose one logical transaction into several small ones (or merge), so size is partly a strategic choice, breaking any per-unit-size reasoning.

A precise formalization: design (or rule out) a mechanism (x, p) over bid vectors where each bid carries a size sᵢ, allocation must be feasible under ∑ sᵢ ≤ B and dependency constraints, and the mechanism is UIC + MIC + c-IC against deviations that include size manipulation, with discrete bid values.

## Why does it matter?

Real blockchains (Bitcoin, Ethereum) have exactly this structure: variable-size transactions, dependency graphs (UTXO chains, smart-contract calls), and users who genuinely can split transfers into multiple transactions. Existing collusion-resistant fee mechanisms are therefore not deployable as-is; a solution would close the gap between the 2022–2024 theory results and deployed systems. It would also connect TFMs to classical combinatorial procurement with false-name bids (Yokoo's line), where a user profits by bidding under multiple identities — the same phenomenon as transaction splitting.

## What's already known?

- **Continuous-domain impossibilities:** Roughgarden (2021) showed that the Bitcoin-style "pay-your-bid, kth-price" rules fail strong incentive properties; several works (Chung & Shi 2021; Ferreira et al.; Shi et al. on UTFMs) proved that no nontrivial TFM is simultaneously UIC and c-IC in the standard continuous model with side contracts — the motivation for discretization.
- **Discrete escape:** Gafni and Yaish (2024, arXiv:2210.07793) show that restricting bids to a discrete grid (or using Bayesian priors) restores collusion resistance, via mechanisms where the discretization makes profitable deviations impossible or unprofitable at the margin. (Verify the exact guarantee level — UIC/MIC/2-IC variants — before building on it.)
- **Knapsack/size aspects:** Roughgarden's TFM formalism already allows sizes; his "EIP-1559-like" burning mechanisms achieve weak assumptions with unit or divisible sizes, but the strict collusion-resistance-with-burning question and discrete mechanisms with sizes are open.
- **False-name resistance:** Yokoo et al.'s combinatorial auction results show false-name-proofness is generically very costly (only "maximal" allocation rules survive) — a warning for splitting-resistance.

Nothing known combines all three: discreteness + sizes + dependencies.

## What would count as solving it?

Any of:
1. **Positive:** A mechanism with discrete bids, provably UIC + MIC + c-IC (for some c ≥ 1) with heterogeneous sizes, with a quantified revenue/welfare bound. Dependencies and splitting-resistance may be treated as separate theorems.
2. **Negative:** An impossibility theorem: no discrete-bid mechanism can be UIC + c-IC with heterogeneous sizes (e.g., a splitting-based attack on any discrete mechanism), which would sharpen the Gafni–Yaish escape route.
3. **Partial but crisp:** Impossibility with splitting; possibility with sizes and dependencies but honest sizes; or a reduction showing splitting-resistance ⟹ false-name-proof combinatorial procurement, transferring known bounds.

A referee wants: formal model, exact incentive definitions used (these vary across papers — state yours), and full proofs or explicit attack constructions.

## How to start

1. Read Roughgarden's *"Transaction Fee Mechanism Design"* (2020/2021) §§ on the TFM model, UIC/MIC/c-IC definitions, and size-general results — the vocabulary everything else uses.
2. Read Gafni–Yaish (arXiv:2210.07793) carefully: understand *exactly why* discretization defeats side-contract deviations, and test whether that argument survives when a colluder can split one size-s transaction into k size-s/k pieces (each with a discrete bid).
3. Formalize the splitting attack attempt on their mechanism first — it likely either works (→ impossibility paper) or reveals the invariant needed (→ construction).
4. For dependencies, model them as precedence-feasible allocation and check whether myopic greedy-by-bid-density rules admit insertion attacks (miner adds a fake parent to extract more).

**Main obstacle:** incentive proofs for discrete mechanisms are tight margin arguments; adding sizes makes the allocation rule combinatorial (knapsack), and almost all knapsack rules have manipulation-sensitive ties/boundaries.

## Key references

- T. Roughgarden, *"Transaction Fee Mechanism Design for the Ethereum Blockchain: An Economic Analysis of EIP-1559"*, EC 2021 / arXiv:2012.00854 — defines the UIC/MIC/c-IC framework and analyzes fee burning; sizes appear in the model. (verify link)
- Y. Gafni, A. Yaish, *"Discrete & Bayesian Transaction Fee Mechanisms"*, arXiv:2210.07793 (2024) — the key prior work: discrete/bid-domain restrictions that circumvent continuous impossibilities for collusion resistance. (verify final venue; possibly a later conference version)
- H.-K. Chung, E. Shi, *"Foundations of Transaction Fee Mechanism Design"*, EC 2023 — unified impossibility results for strict incentive compatibility in TFMs. (verify)
- M. Yokoo, Y. Sakurai, S. Matsubara, *"The Effect of False-name Bids in Combinatorial Auctions"*, Games and Economic Behavior 2004 — false-name-proofness foundations, directly relevant to transaction splitting. (verify)

## Warnings

- Definitions of "collusion resistance" differ across papers (side contracts with vs. without renegotiation, weak vs. strong c-IC); pick one and note it — many "contradictions" between papers are definitional.
- Discreteness arguments are fragile: they usually hinge on the minimum bid increment exceeding any deviation gain. Sizes create new deviation margins (change size, split, merge), so the discretization must be over *value density*, not just value — check this.
- Do not assume EIP-1559's burned-fee logic transfers: burning helps MIC but is not known to give c-IC with strategic sizes.
- The census record is thin; verify that no 2024–2026 paper has already solved the heterogeneous-size extension before investing heavily.

---
*`BCGT-000279` · flagged for review*

## Acceptance guidance

Any of:
1. **Positive:** A mechanism with discrete bids, provably UIC + MIC + c-IC (for some c ≥ 1) with heterogeneous sizes, with a quantified revenue/welfare bound. Dependencies and splitting-resistance may be treated as separate theorems.
2. **Negative:** An impossibility theorem: no discrete-bid mechanism can be UIC + c-IC with heterogeneous sizes (e.g., a splitting-based attack on any discrete mechanism), which would sharpen the Gafni–Yaish escape route.
3. **Partial but crisp:** Impossibility with splitting; possibility with sizes and dependencies but honest sizes; or a reduction showing splitting-resistance ⟹ false-name-proof combinatorial procurement, transferring known bounds.

A referee wants: formal model, exact incentive definitions used (these vary across papers — state yours), and full proofs or explicit attack constructions.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/429acee9-21d0-48bf-9255-c00afab9ca25?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000279.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000279)
