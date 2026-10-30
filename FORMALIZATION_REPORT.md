# Formalization report — rare subtree patterns

Coverage inventory, tree/excursion correspondence and forest-boundary milestone: 29 October 2026. Tree-interface refactor: 30 October 2026.

## Scope and environment

- **Paper:** Igor Kortchemski and Leonard Vetter, [*Rare subtree patterns in size-conditioned Bienaymé trees: Poisson approximation and declumping*, arXiv:2607.22291v1][P9]. Source line numbers below refer to the locally reviewed TeX file `poisson-declumping-arxiv.tex`; its SHA-256 at inventory time is `3b75c02ec5e8a7e12d32cccddfe463ae79ca596d1b8b239bc0352110b6472595`. Manuscript sources are not redistributed in this repository.
- **Scope:** implement [BGW_ROADMAP_2607_22291.md](BGW_ROADMAP_2607_22291.md), retaining every stated theorem, proposition, lemma and corollary in P9, their required foundations, and the grouped consequences below. Section 7.4, “Perspectives on vertex leaf-height counts” (3389–3496), proposes conjectural Poisson and compound-Poisson extensions; these are **out of scope**.
- **Workspace:** this local BGW library; no Git revision existed when this inventory was created. Record the verified revision when one is available.
- **Pinned environment:** Lean `leanprover/lean4:v4.35.0` in [lean-toolchain](lean-toolchain); Mathlib v4.35.0 at commit `56a005855ea17e0b0958f73ec801ae140b0a1679` in [lakefile.toml](lakefile.toml). The full default `lake build` passed for the foundation milestone below.

The tree/excursion correspondence and forest first-passage results are **proved**. Further deterministic foundations have the partial coverage detailed below. All 35 labeled paper results remain **Unproved**. A completed entry needs an exact proved export or a checked specialization, relevant build and axiom evidence, and source correspondence. A conditional helper does not close the source result. Update this report at coherent milestones rather than for every helper.

The standing setting is critical offspring with $\mu_1\ne1$, stable index $\alpha\in(1,2]$, and conditioning through admissible sizes. Preserve positive-probability conditions, lattice endpoints and each statement's additional assumptions. The source defines microscopicity `(M)` at 215–225, structural condition `(S)` at 239–257, and pair non-clustering `(C)` at 275–295; `(S)` is a sufficient route to `(M)`, not an additional universal premise.

## Required foundations and grouped consequences

These rows track shared foundations and substantive claims outside numbered result environments. They do not introduce separate implementation frameworks.

| Target | Source | Lean export | Coverage |
| --- | --- | --- | --- |
| Finite plane trees and forests: addresses/children, root and parent, fringe subtrees, size, leaves, generation sizes, degree sum, fixed-size finiteness and leaf grafting | Roadmap §§1–2; P9 464–473, 676–695, 2497–2510 | `BGW.PlaneTree.mem_vertices_iff`, `length_vertices`, `nodup_vertices`, `generationSize_zero`, `sum_degrees_add_one`, `finite_size_eq`, `graft_cut`, `size_graft_of_isLeaf` | Partial: implemented tree/address/grafting foundation; further generation, ancestry and leaf APIs as needed |
| DFS tree/excursion equivalence and reconstruction | Roadmap §2; P9 681–695, 716–728 | `BGW.PlaneTree.isTreeExcursion_iff_existsUnique`, `ofIncrements?_increments`, `ofIncrements?_eq_some_iff`, `exists_ofIncrements?_eq_some`, `increments_injective`; natural-degree counterparts in `Decode.lean` | Proved: explicit excursion conditions, executable decoder and both inverse identities |
| Forest concatenation and first passage at component boundaries | Roadmap §2; P9 walk coding at 716–728 | `BGW.PlaneForest.increments_append`, `sum_take_increments_gt`, `take_increments_size_take`, `sum_take_increments_size_take`, `sum_take_increments_gt_before_size_take` | Proved: the end of the first $r$ components is the first visit to $-r$; empty-forest cases included |
| Fringe blocks and cyclic first-hitting-time decoding | Roadmap §§2, 4; P9 `eq:fringe`, 691–695 | — | Unproved |
| Cycle lemma, forest Kemperman/Dwass and the uniform cyclic-shift bridge representation | Roadmap §2; P9 `eq:shifts`, 716–728; forest estimate proof 1136–1180 | — | Unproved |
| BGW finite-tree mass, critical extinction/normalization, root and finite-generation branching, size conditioning and stopped-walk law | Roadmap §3; P9 699–728, 1433–1444, 3066–3127 | — | Unproved |
| Regular variation, Potter/sum estimates, stable density/local limit inputs with lattice versions, short-block walk tightness, probability and total-variation transfers | Roadmap §6; P9 toolbox 837–882 and use at 1752–1776 | — | Unproved |
| Fixed-pattern fringe-count expectation as a specialization of the uniform-fringe identity | Roadmap §4; P9 `cor:cyclicshift`, 777–824 | — | Unproved |
| Exact declumped root-event formula and elementary probability bounds | `eq:parent_formula`, 2217–2235 | — | Unproved |
| Complete $r$-ary pattern probabilities and skeleton/leaf counts | 2464–2473, 2625–2642 | — | Unproved |
| Root leaf-height recursion, exact-height tail differences, and conditioned-generation formulas used for structural microscopicity | 2953–3006, 3010–3025, 3066–3127 | — | Unproved |
| Maximum threshold implications and first-order leaf-height limits | `eq:threshold_rary`, 2849–2868; `eq:threshold_lh`, 3337–3368; 574–581 | — | Unproved |
| Leaf-height boundary case $1-\kappa\mu_\kappa=0$: offspring support $\{0,\kappa\}$ and equality with the complete $\kappa$-ary maximum on supported trees | 3276–3296 | — | Unproved |

The analytic-input row records dependency obligations. It makes no claim that these results are absent from the pinned Mathlib revision. Reuse or prove the precise inputs needed; a conditional local-limit assumption alone does not establish the manuscript's offspring-law theorem.

## Implemented deterministic foundations

All exports below are available through [BGW.lean](BGW.lean). Tree operations belong to `BGW.PlaneTree`, forest operations to `BGW.PlaneForest`, and `IsTreeExcursion` to `BGW`.

- [Tree.lean](BGW/Tree.lean) defines `PlaneTree` as `WType (fun n : ℕ => Fin n)` and gives an exact list-of-children interface (`children_node`, `node_children`). `induction_on` and `casesOn` are the default `induction` and `cases` principles, each with a single `node ts` case. `fold` is bottom-up recursion over children, with `fold_node`; `size`, `vertices` and `degrees` are defined through it and remain evaluable by `decide`. The `WType` encoding is not used outside this file. The module also gives positive vertex size, re-rooted `fringe?` lookup, and explicit vertex and leaf predicates. Encodability, hence countability, is transferred from Mathlib's `WType` instance. No offspring-law assumptions enter these definitions.
- [Vertices.lean](BGW/Tree/Vertices.lean) enumerates the zero-based addresses in DFS order. `mem_vertices_iff`, `length_vertices` and `nodup_vertices` prove that it lists exactly the vertices, once each, including distinct occurrences of equal subtrees. Depth is address length, the root is `[]`, and `generationSize_zero` verifies the root generation. Prefix ancestry includes equality; the parent of a nonempty address is its `dropLast`.
- [Fringe.lean](BGW/Tree/Fringe.lean) proves composition of fringe lookup, prefix closure and gapless child indices at every vertex. It proves replacement, cut/restoration and size identities, including `size_graft_of_isLeaf`: the inserted tree's root is identified with the original leaf. Replacement at an invalid address is proved to leave the tree unchanged. These are deterministic prerequisites, **not** P9's probabilistic `lem:cut_graft`.
- [Finite.lean](BGW/Tree/Finite.lean) proves `finite_size_le` and `finite_size_eq` directly using bounded-length lists of smaller trees, independently of the excursion bijection.
- [DFS.lean](BGW/Tree/DFS.lean) identifies the recursive degree word with outdegrees at the actual DFS addresses. It proves the degree sum, length equal to size, integer increments at least $-1$, total sum $-1$, and nonnegative sums of all strict prefixes. `BGW.PlaneForest` supplies total size, concatenated increments and terminal sum equal to minus the number of components, including the empty forest.
- [Decode.lean](BGW/Tree/Decode.lean) reconstructs natural degree words from right to left, making the first available trees the ordered children of the next root. `PlaneForest.ofDegrees?_eq_some_iff` and `PlaneTree.ofDegrees?_eq_some_iff` prove both encoding/decoding directions for forests and single trees. Tree parsing rejects nonsingleton forests. `length_add_sum_of_ofDegrees?_eq_some` identifies the number of roots with the degree deficit.
- [Excursion.lean](BGW/Tree/Excursion.lean) defines the explicit integer condition `IsTreeExcursion`: increments at least $-1$, total sum $-1$, and nonnegative strict-prefix sums. `ofIncrements?` checks the lower bound before converting to natural degrees. `exists_ofIncrements?_eq_some` proves successful decoding from these conditions; `ofIncrements?_increments` and `ofIncrements?_eq_some_iff` give the inverse identities. `isTreeExcursion_iff_existsUnique` establishes the full correspondence with a unique finite plane tree. Its suffix-validity helper is proved from the excursion assumptions, not added as a premise of the result.
- [Forest.lean](BGW/Tree/Forest.lean) proves that a forest path remains strictly above minus its component count until its terminal time. For every $r$ at most the component count, the prefix ending at the total size of the first $r$ trees has sum $-r$; all shorter prefixes have larger sums. Thus first passage recovers each component boundary, without introducing a separate stopping-time framework.

The source correspondence is P9's rooted ordered tree/fringe convention (189–190, 682–695), its walk coding (716–728), and its cut/graft convention (2497–2503). Zero-based child addresses are an implementation choice: the explicit one-based numbering in P9 is its DFS enumeration $u_1,\ldots,u_n$. The address membership, cardinality and vertex-degree bridges above establish this implementation's intended interpretation. The deterministic inverse coding is proved by a direct parser and a suffix-sum argument; this does not establish the paper's random-walk distribution identities. The next target is P9's fringe-block/cyclic decoding identity `eq:fringe`, followed by the cycle lemma.

Verification uses the local `lean-workflow`, `lean-project-architecture` and `lean-statement-audit` skills. The independent reviewer `stable_trees` reconstructed the relevant source conventions and passed the definitions and theorem statements; the final full build resolved every elaboration condition. The verification record below distinguishes those checks from publication checks.

## Paper result correspondence

Every labeled theorem, proposition, lemma and corollary in the local source has its own row. The target descriptions summarize the content; the source statement and standing assumptions determine the exact Lean type.

| Source label | Lines | Target content | Lean export | Coverage |
| --- | --- | --- | --- | --- |
| `thm:PoissonApproximation` | 305–329 | `(M)+(C)`: bounded-mean total-variation Poisson approximation; divergent-mean normalized law of large numbers | — | Unproved |
| `thm:PoissonApproximation2` | 396–442 | Raw `(S)`, declumped `(C)`, $c_B>0$: Poisson approximation with mean $c_Bn\pi_n$; divergent-mean declumped count tends to infinity | — | Unproved |
| `thm:max_string` | 475–501 | Largest complete $r$-ary non-fringe subtree: unary lattice limit; $r\ge2$ two-point and interior one-point concentration | — | Unproved |
| `thm:max_lh` | 533–558 | Maximum leaf-height: unary lattice limit and non-unary two-point/interior one-point concentration | — | Unproved |
| `lem:RW` | 760–764 | Truncated marked-tree count has the cyclic-indicator sum law under the walk bridge | — | Unproved |
| `cor:cyclicshift` | 777–824 | Exact uniform-vertex fringe probability using the remainder-walk density | — | Unproved |
| `cor:Poisson_fringe` | 924–958 | Marks supported on size $k_n\to\infty$, $k_n=o(n)$: Poisson approximation and divergent-mean law of large numbers | — | Unproved |
| `lem:M` | 1015–1026 | Strong unconditioned marked-size tail criterion implies `(M)` | — | Unproved |
| `lem:critM` | 1055–1079 | Subpolynomial expected skeleton size gives `(S)` at a polynomial cutoff | — | Unproved |
| `lem:forest_estimates` | 1119–1134 | Forest-size tail and density-weighted tail bounds | — | Unproved |
| `lem:consequenceM` | 1182–1194 | `(S)` gives marked-size tail and weighted remainder bounds | — | Unproved |
| `cor:SM` | 1256–1276 | `(S)⇒(M)`, expected-count asymptotics and negligible conditioned root marking in the bounded-mean regime | — | Unproved |
| `cor:Non-fringe Poisson` | 1387–1422 | Aperiodic raw-pattern corollary under skeleton/leaf size bounds and the stated uniform overlap bound | — | Unproved |
| `prop:largeoutdegree` | 1458–1486 | Counts of prescribed growing outdegree: Poisson approximation, Poisson distributional limit and law of large numbers | — | Unproved |
| `prop:AGG` | 1600–1614 | Chen–Stein bound with the precise test-function form of the third error term | — | Unproved |
| `lem:error_est` | 1633–1638 | Uniform small-block bridge-removal bound near compatible central endpoints | — | Unproved |
| `cor:pip` | 1651–1658 | Conditioned truncated marking probability is bounded by a constant times the unconditioned mark probability | — | Unproved |
| `lem:truncate` | 1673–1688 | Bounded-mean truncation error and Poisson-parameter replacement vanish under `(M)` | — | Unproved |
| `lem:pnqn` | 1732–1739 | Truncated marking probabilities are asymptotic; marked short-block endpoint tails are negligible | — | Unproved |
| `prop:b1` | 1797–1798 | First Chen–Stein error vanishes in the bounded-mean regime | — | Unproved |
| `lem:boundb2` | 1815–1818 | Pair-overlap Chen–Stein error vanishes using `(C)` | — | Unproved |
| `prop:b3bis` | 1862–1864 | Remaining conditioned-dependence error vanishes; retain subsection assumptions, including `(M)` | — | Unproved |
| `lem:1` | 1872–1876 | Endpoint-perturbation estimate for the bounded test function; retain subsection assumptions | — | Unproved |
| `lem:extreme_means` | 2006–2014 | Truncated count vanishes when its mean vanishes; `(M)+(C)` give its divergent-mean normalized law of large numbers | — | Unproved |
| `lem:root_declumping` | 2197–2215 | Declumped probability bounds and rate transfer; raw `(S)` implies declumped `(M)` | — | Unproved |
| `lem:declump_to_raw` | 2343–2357 | Root propagation and negligible root marking transfer declumped Poisson zero-event probabilities to raw counts | — | Unproved |
| `cor:declump_zero` | 2403–2414 | Raw zero-event asymptotic under the declumping theorem and propagation hypotheses | — | Unproved |
| `lem:ineqs` | 2426–2437 | First-moment upper bound and divergent-witness lower bound for maxima | — | Unproved |
| `lem:cut_graft` | 2512–2546 | Cut-and-graft bound on simultaneous nested events with positive-probability graft constraints | — | Unproved |
| `lem:S_rary` | 2644–2653 | Complete $r$-ary marks satisfy `(S)` and mean asymptotics; divergent mean also gives raw `(C)` | — | Unproved |
| `lem:C_rary` | 2762–2766 | Declumped complete $r$-ary marks satisfy `(C)` | — | Unproved |
| `lem:ell_asymp` | 2937–2951 | Unconditioned root leaf-height tail asymptotics, including the positive constants $C_\mu$ and $D_\mu$ | — | Unproved |
| `lem:S_lh` | 3053–3064 | Leaf-height marks satisfy `(S)` and expected-count asymptotics at the stated subpolynomial scales | — | Unproved |
| `lem:lh_conditional_mark` | 3148–3156 | Joint declumped-mark/leaf-height probability bound | — | Unproved |
| `lem:C_lh` | 3183–3188 | Declumped leaf-height marks satisfy `(C)` when $1-\kappa\mu_\kappa>0$ | — | Unproved |

## Correspondence and outstanding checks

The implemented correspondence is limited to the deterministic foundation above; no labeled paper result is completed. In particular, the second Poisson theorem states divergence of the declumped count, not a law of large numbers normalized by $c_Bn\pi_n$. Non-fringe occurrence preserves exact internal outdegrees, and leaf-height means distance to the nearest descendant leaf.

Two source points remain to address when their proofs are implemented:

- **Periodic bridge endpoints:** the main results cover periodic laws through admissible sizes (855–882), whereas proofs use aperiodic notation. In `lem:1` (1873), the reference endpoint defining $h_n(0)$ may be incompatible with the lattice. Use and justify a compatible reference in the periodic argument; no formal repair has yet been checked.
- **Complete $r$-ary lower bound:** line 2854 cites `cor:Non-fringe Poisson(ii)`, whose uniform-overlap and aperiodicity assumptions are stronger than the preceding results supply. The established `(M)+(C)` route points directly to `thm:PoissonApproximation(ii)`. Check and record that application when formalizing; the conclusion is not currently certified.

## Verification record

- Tree-interface refactor, 30 October 2026: `PlaneTree` became a definition instead of an abbreviation, `fold`, `fold_node`, `casesOn`, `casesOn_node` and the `Encodable` instance were added, and `size`, `vertices` and `degrees` were redefined through `fold`. Their `_node` equations are unchanged, as are all other exported statements apart from the binder names of `induction_on` (now `motive` and `node`). No source-facing statement changed, so no new statement review was required. After the refactor, `lake build` passed (831 jobs, no warnings) and the checks below were rerun except where stated.
- `lake build`: **passed**, all eight foundation modules and the public `BGW` entry point, 831 jobs, no warnings in the final build. Dependency pins were unchanged.
- Independent source/statement and proof-route review: **passed** for the new decoder, explicit excursion equivalence, and forest-boundary results; previous foundation reviews remain applicable. The reviewer checked that the suffix invariant is discharged and that conversion preserves the increment lower bound.
- Batched axiom checks: **passed** for all 107 public definitions and theorems in the eight modules (102 before the refactor), collected over every `BGW` constant. Their combined axiom closure contains only `propext`, `Classical.choice` and `Quot.sound`; no `sorryAx`, custom axiom or native-evaluation axiom appears. The project sources contain no `sorry`, `axiom`, `native_decide`, `bv_decide` or `unsafe` declarations or uses.
- Downstream `import BGW` probe: **passed**, including all three README examples, generic natural/integer roundtrips, the exact unique-tree equivalence, and reconstruction of an asymmetric tree's degree word and vertex addresses. Empty words, unfinished words, multiple roots, increments below $-1$, and premature hits of $-1$ were rejected as expected. Empty-forest parsing and explicit component-boundary prefix sums also passed. The temporary probe exited successfully without warnings and was removed after recording these results. After the refactor, a new probe rechecked the three README examples, `decide` evaluation of `size`, `vertices`, `degrees` and `generationSize` on explicit trees, the default `induction` and `cases` on `PlaneTree` (including a data-valued `cases`), and the `Encodable` and `Countable` instances; the other checks of the earlier probe were not rerun.
- Comparator and Palomar submission verification have not been run; this is local library development.

Before whole-paper completion, recheck the source for omitted substantive claims and reconcile all grouped rows and exclusions.

[P9]: https://arxiv.org/abs/2607.22291v1
