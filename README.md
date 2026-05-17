# Subset Sum Problem — Interactive Teaching Tool

> **BCS 309 — Algorithms I · Spring 2026 · Dr. Arash Kermani**  
> Mohammad Abdullah · Student ID: 20230003944

A single-file interactive lecture on the **Subset Sum Problem** — a decision problem that is NP-complete, yet admits a beautifully structured pseudo-polynomial dynamic programming solution. The tool is built as a self-contained HTML page with no build step and no external dependencies beyond Google Fonts.

**Live demo:** [abdulla1x.github.io/subset-sum](https://abdulla1x.github.io/subset-sum/)

---

## What This Covers

The page is structured as a six-section lecture, followed by an interactive tool and a formal NP-completeness proof:

| # | Section | Key ideas |
|---|---------|-----------|
| 1 | **Problem Definition** | Decision vs. search vs. optimization; multisets; YES/NO certificates |
| 2 | **Worked Example** | Bitmask encoding; full enumeration of all 16 subsets for `S={3,1,4,2}, t=5` |
| 3 | **Why It's Hard** | Exponential growth of the power set; why hardware can't save you |
| 4 | **Brute Force vs. DP** | O(n·2ⁿ) enumeration; DP recurrence, state definition, cell-by-cell trace; pseudo-polynomial clarification |
| 5 | **Interactive Tool** | Animated visualizations with step-by-step pseudocode highlighting |
| 6 | **NP-Completeness** | Polynomial-time verifier (NP membership) + 3-SAT → Subset Sum reduction (NP-hardness); weak vs. strong NP-completeness |

---

## The Algorithms

### Brute Force — O(n · 2ⁿ)

Enumerate every subset using bitmasks from `0` to `2ⁿ − 1`. For each subset, compute its sum and compare against the target. Stop on the first match; report NO after full exhaustion.

```
for mask ← 0 to 2ⁿ − 1:
    sum ← 0
    for i ← 0 to n−1:
        if bit i of mask is set:
            sum ← sum + arr[i]
    if sum == target: return true
return false
```

Correctness: complete and sound. Cost: exponential in `n`. Practical for `n ≤ 25`.

### Dynamic Programming — O(n · t)

Build a boolean table `dp[i][s]` where each cell answers: *"Using only the first `i` elements, can some subset sum to exactly `s`?"*

**Base cases:**
- `dp[0][0] = true` — the empty subset always sums to 0
- `dp[0][s] = false` for all `s > 0`

**Recurrence:**
```
dp[i][s] = dp[i-1][s]                              // exclude aᵢ
         OR (s ≥ a[i] AND dp[i-1][s - a[i]])       // include aᵢ
```

**Answer:** `dp[n][t]`

### Why O(n·t) Is Pseudo-Polynomial, Not Polynomial

Writing `t` in binary takes only `L = ⌈log₂(t)⌉` bits. Since `t = O(2^L)`, the table has `n × 2^L` cells — exponential in the bit-length of `t`. On the adversarial input `S = {1, 2, 4, …, 2^(n−1)}, t = 2ⁿ − 1`, the DP offers no advantage over brute force. This is why Subset Sum remains NP-complete despite having a DP solution.

---

## Interactive Tool

The tool has three modes, selectable via tabs:

**Dynamic Programming** — fills the boolean table one cell at a time, highlighting the current cell, its two dependency cells (exclude = blue, include = purple), and the active pseudocode line. A step explanation panel narrates the include/exclude reasoning for every cell.

**Brute Force** — animates chip-style element highlighting with a bitmask binary display, running sum comparison, and a scrollable history log of all subsets evaluated so far.

**Comparison Mode** — runs both algorithms simultaneously in a side-by-side view with live operation counters, making the O(2ⁿ) vs O(n·t) divergence concrete.

Controls: Step, Play (with adjustable speed), Pause, Reset. Five preset examples cover YES instances, NO instances, multiple witnesses, the pseudo-polynomial counterexample, and a single-element edge case.

A growth curve SVG chart shows the three cost trajectories — brute force O(2ⁿ), DP with small `t`, and DP with adversarial `t = 2ⁿ` — with a live marker placed at the current `n` after pressing Build Steps.

---

## NP-Completeness Proof

### Step 1 — Subset Sum ∈ NP

Certificate: a list of indices identifying the claimed subset. Verifier: check each index is valid, no repeats, compute the sum, accept iff sum `= t`. Time: O(n). Therefore Subset Sum ∈ NP. ∎

### Step 2 — NP-hardness via 3-SAT ≤ₚ Subset Sum

Given a 3-SAT formula with `n` variables and `m` clauses, construct `2n + 2m` integers with `n + m` decimal digit positions:

- **Variable rows** (`vᵢ`, `v'ᵢ`): each pair contributes 1 to the `i`-th variable digit position, plus 1s in clause positions where the literal appears. The target requires exactly 1 at each variable position, so exactly one of each pair must be selected — enforcing a consistent truth assignment.
- **Slack variables** (`sⱼ`, `s'ⱼ`): contribute 1 and 2 respectively to clause position `j`. The target requires exactly 4 at each clause position. Since slacks contribute at most 3, variable rows must contribute at least 1 — meaning at least one literal per clause must be satisfied.

No-carry condition: the maximum digit sum at any position is 6 < 10, so digits never interfere. The reduction runs in O((n+m)²) time. Both directions of the iff hold (proven in the tool). Therefore 3-SAT ≤ₚ Subset Sum, so Subset Sum is NP-hard. ∎

**Conclusion:** Subset Sum ∈ NP and Subset Sum is NP-hard → **Subset Sum is NP-complete.**

Subset Sum is *weakly* NP-complete: it becomes polynomial when `t = O(nᵏ)` for fixed `k` (the DP then runs in genuine polynomial time). Strongly NP-complete problems like 3-SAT have no pseudo-polynomial algorithm unless P = NP.

---

## Assignment 2 — Additional Analysis

The companion assignment (`20230003944_Abdullah_assignment.pdf`) covers three further results not in the HTML tool:

**Meet-in-the-Middle** — O(2^(n/2) · n): split the array in half, enumerate all 2^(n/2) sums for each half, sort one list, then binary-search for complements. Strictly better than brute force, still exponential.

**Tight verifier complexity** — O(N²) in bit-complexity (where N is the total input bit-length), or O(m) under the RAM model.

**3-Partition → Subset Sum reduction** — an alternative hardness proof using a carry-free digit-gadget construction in base R = mB + 1, with 3m² gadgets and target T expressed in base-R. The construction is O(m³) time.

---

## Tech Stack

| Concern | Choice |
|---------|--------|
| Structure | Vanilla HTML5 |
| Styling | CSS custom properties, CSS Grid, CSS animations |
| Logic | Vanilla JavaScript (ES6+), no frameworks |
| Fonts | Google Fonts — Merriweather, JetBrains Mono, Exo 2 |
| Dependencies | None (zero npm, zero bundler, zero CDN scripts) |
| Deployment | GitHub Pages (static, single file) |

Everything — content, styles, and ~2000 lines of algorithm logic — lives in one `subset_sum.html` file. Open it directly in any browser; no server required.

---

## Repository Structure

```
subset-sum/
├── subset_sum.html          # Complete self-contained application
├── README.md                # This file
```

---

## Course Context

**CLOs addressed:**

| CLO | Description | Weight |
|-----|-------------|--------|
| CLO-3 | Dynamic programming algorithm design | /10 |
| CLO-4 | Algorithm paradigms and analysis | /10 |
| CLO-5 | NP-completeness via polynomial-time reduction | /20 |

**References:**
- Cormen, Leiserson, Rivest & Stein, *Introduction to Algorithms*, 4th ed., Chapter 34
- [Wikipedia: Subset Sum Problem](https://en.wikipedia.org/wiki/Subset_sum_problem)
