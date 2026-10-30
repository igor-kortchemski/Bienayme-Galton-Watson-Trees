# Bienaymé–Galton–Watson Trees in Lean

A Lean 4 library for finite plane trees and their Łukasiewicz encodings, towards
the formalization of Bienaymé–Galton–Watson theory.

**Status: library in development.** The API may evolve, including changes to
definitions, declaration names and module organization. Backward compatibility
is not guaranteed; downstream projects should pin a specific Git commit.

The current library provides deterministic finite-tree foundations. BGW
probability laws, conditioning and the probabilistic results of the target
manuscript remain to be formalized. The [formalization report](FORMALIZATION_REPORT.md)
records the proved results and outstanding targets.

## Available foundations

- Finite rooted ordered trees with a list-of-children interface, structural
  induction and executable recursion.
- Zero-based vertex addresses, DFS enumeration, fringe subtrees, cut and graft
  operations, and finiteness of fixed-size tree classes.
- DFS degree words and Łukasiewicz increments, with the tree/excursion
  correspondence and executable reconstruction proved in both directions.
- Ordered forests, concatenated encodings and first passage at component
  boundaries.

Trees reuse Mathlib's `WType` internally in [BGW/Tree.lean](BGW/Tree.lean).
The public interface exposes ordered lists of children: `induction` and `cases`
produce a single `node ts` case, and `PlaneTree.fold` defines bottom-up recursion.
The root address is `[]`, child indices start at zero, size counts vertices, and
a fringe subtree is re-rooted at its selected vertex.

Everything is available through `import BGW`:

```lean
module

import BGW

open BGW.PlaneTree

example : (node [leaf, leaf]).size = 3 := by
  simp

example : (node [leaf, leaf]).increments = [1, -1, -1] := by
  simp [leaf]

example : (ofIncrements? [1, -1, -1]).map size = some 3 := by
  decide
```

## Building

Install [Elan](https://github.com/leanprover/elan), then run the following commands
from the repository root. Elan selects the Lean version from `lean-toolchain`;
the cache command downloads precompiled Mathlib dependencies.

```sh
lake exe cache get
lake build
```

During development, rebuild with `lake build`, or check an individual file with:

```sh
lake env lean BGW.lean
```

For interactive editing, open the repository in VS Code with the Lean 4 extension.

The pinned environment is Lean **4.35.0** and Mathlib **v4.35.0**, at commit
`56a005855ea17e0b0958f73ec801ae140b0a1679`. Keep
[lean-toolchain](lean-toolchain), [lakefile.toml](lakefile.toml) and
[lake-manifest.json](lake-manifest.json) together to reproduce these versions.
[GitHub Actions](.github/workflows/lean.yml) runs the build on pushes and pull
requests, using the Mathlib cache.

## Library and documentation

| File | Contents |
| --- | --- |
| [BGW.lean](BGW.lean) | Public entry point |
| [Tree](BGW/Tree.lean) | Plane trees, list interface and basic operations |
| [Vertices](BGW/Tree/Vertices.lean) | Vertex addresses and DFS enumeration |
| [Fringe](BGW/Tree/Fringe.lean) | Fringe subtrees, replacement, cutting and grafting |
| [Finite](BGW/Tree/Finite.lean) | Finiteness of bounded-size and fixed-size classes |
| [DFS](BGW/Tree/DFS.lean) | Degree words and tree/forest increments |
| [Decode](BGW/Tree/Decode.lean) | Executable degree-word reconstruction |
| [Excursion](BGW/Tree/Excursion.lean) | Łukasiewicz excursion characterization and inverse identities |
| [Forest](BGW/Tree/Forest.lean) | First passage at forest component boundaries |
| [Formalization report](FORMALIZATION_REPORT.md) | Current coverage and verification record |
| [Targeted roadmap](BGW_ROADMAP_2607_22291.md) | Plan for rare subtree patterns and Poisson approximation |
| [Broader roadmap](BGW_FORMALIZATION_ROADMAP.md) | Proposed scope across several BGW topics |

The roadmaps describe planned work; the report identifies what is implemented.
References link to public manuscripts. Local copies of papers and development
tools are excluded from the repository.

This independent project is intended to support a possible future integration
into [Tau Ceti](https://github.com/TauCetiProject/TauCeti), subject to its roadmap
and review process.

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE).
