# hilbert-polya-bost-connes

**The Bost-Connes Modular Generator as a candidate Hilbert-Pólya Operator, formalized in Lean 4**

*G Six. math.OA / math.NT submission, 2026.*

<!-- DOI badges are placeholders until Zenodo mint at publication. -->
<!-- [![Paper DOI](https://zenodo.org/badge/DOI/PENDING_PAPER_DOI.svg)](https://doi.org/PENDING_PAPER_DOI) -->
<!-- [![Lean companion DOI](https://img.shields.io/badge/Lean%20companion-PENDING__LEAN__DOI-blue)](https://doi.org/PENDING_LEAN_DOI) -->
[![TT-substrate DOI](https://img.shields.io/badge/TT--substrate-10.5281%2Fzenodo.20674891-blue)](https://doi.org/10.5281/zenodo.20674891)
[![License: CC-BY-4.0](https://img.shields.io/badge/License-CC--BY--4.0-lightgrey.svg)](LICENSE)

This repository hosts the paper and references to its Lean 4 companion formalization. The paper studies the modular generator $D_\infty = -i \cdot \partial_t \log \Delta$ of the Bost-Connes type $\mathrm{III}_1$ factor at the critical KMS state ($\beta = 1$), acting on the GNS Hilbert space, as a candidate Hilbert-Pólya operator, together with a Galerkin approximation chain whose spectra approximate that of $D_\infty$ in the Bögli–Siegl–Tretter sense; the construction is formalized in Lean 4 against a pinned Mathlib revision.

## Honest framing

The chain's canonical terminal is

```
HilbertPolyaBostConnes.rh_of_ccm_galerkin
  (g : CCMGalerkinSpectralData) : RiemannHypothesis
```

— **"the Riemann Hypothesis follows from inhabiting `CCMGalerkinSpectralData`"**. Inhabiting `CCMGalerkinSpectralData` is equivalent to the Riemann Hypothesis (see §5 of the preprint). **No term of `CCMGalerkinSpectralData` is constructed in this work.** The Galerkin-side ingredients required for inhabitation are explicitly named in the gate's type-level fields.

**This is not an unconditional closure of the Riemann Hypothesis.** It is a transparent conditional reduction + operator-algebraic scaffold + computer-verified type-level encoding of the reduction.

## Repository contents

| Path | Contents |
|---|---|
| `preprint.md` | The paper (markdown source; §§1–7 + References + Appendix A). |
| `axioms-disclosure.md` | Per-axiom disclosure document. 8 CCM-substrate-own atomic axioms (2 on-terminal + 6 off-terminal) plus 6 TT-upstream-inherited axioms via Lake dependency. Each with literature citation and non-RH-equivalence note. |
| `references.tex` | BibTeX-formatted bibliography; 1:1 aligned with the named-axiom citations of `axioms-disclosure.md`. |
| `LICENSE` | CC-BY-4.0. |

## Companion Lean 4 formalization

The Lean 4 companion is at [`localparty/hilbert-polya-bost-connes-lean`](https://github.com/localparty/hilbert-polya-bost-connes-lean) at commit `53e12d8` (v0.1 release).

**Build environment** (per the Lean companion's `README.md`):

- Lean 4 toolchain `v4.29.1`
- Mathlib SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6`
- Lake dependency on [`localparty/tt-bost-connes-lean`](https://github.com/localparty/tt-bost-connes-lean) v0.2 (commit `d4bb8949`; Zenodo DOI [10.5281/zenodo.20674891](https://doi.org/10.5281/zenodo.20674891))

**Verification** (independent reproducer; cf. Appendix A of the preprint):

```
elan default leanprover/lean4:v4.29.1
lake update
lake build HilbertPolyaBostConnes
```

The chain builds without errors. Zero `sorry` count:

```
grep -rE "by sorry|:= sorry|^sorry$|^\s+sorry$" HilbertPolyaBostConnes/ --include='*.lean'
# → no output
```

Canonical-terminal certificate:

```
cat > /tmp/print-axioms-hp.lean <<'LEAN'
import HilbertPolyaBostConnes.SpectralGate
#print axioms HilbertPolyaBostConnes.rh_of_ccm_galerkin
LEAN
lake env lean /tmp/print-axioms-hp.lean
# → kernel-three + collectively_compact_resolvent_uniform_bound + rouche_zero_existence
```

## Named-axiom budget at a glance

8 CCM-substrate-own atomic axioms (4 Infrastructure BC operator-algebraic substrate + 4 Lemmas/Helpers classical literature ports) + 6 TT-upstream axioms (inherited via Lake-dep on `tt-bost-connes-lean` v0.2 = 14 total. Full inventory with citations, on/off-terminal partition, and non-RH-equivalence notes in `axioms-disclosure.md`.

**On-terminal CCM-own axioms** (in the canonical terminal's `#print axioms` closure):

| Axiom | Citation |
|---|---|
| `rouche_zero_existence` | Rouché 1862 (existence form; cf. Ahlfors *Complex Analysis* §5.3 Theorem 18) |
| `collectively_compact_resolvent_uniform_bound` | Anselone 1971 §1.6 Theorem 1.6 + Stummel 1970 §3 |

**Off-terminal CCM-own axioms** (background substrate, honestly disclosed but absent from the canonical terminal's `#print axioms` output due to the gate's type-level field decoupling):

| Axiom | Citation |
|---|---|
| `bc_factor_isTypeIII1` | Araki–Woods 1968 + Haagerup 1987 |
| `bc_modularFlow_ergodic` | Connes 1973 |
| `bc_connesSpectrum_real` | Connes 1973 |
| `dInftyApproximants_strongConv` | ITPFI substrate (Connes–Marcolli 2008 Ch. III) |
| `dInftyApproximants_finiteRank` | BC modular Galerkin setup (Connes–Marcolli 2008 Ch. III) |
| `dInftyApproximants_h1CompactEmbedding` | Rellich 1930 + Kondrachov 1945 |

**TT-upstream-inherited axioms** (via Lake-dep on `tt-bost-connes-lean` v0.2; discharge owned by the companion paper [Six, tt-bost-connes] §8.2): `bc_system_exists`, `kms1_unique`, `kms1_faithful`, `itpfi_factorization`, `time_evolution_is_automorphism`, `kms_condition`.

No axiom in the budget invokes the Riemann Hypothesis in its published proof.

## Citation

```bibtex
@misc{Six2026hpbc,
  author = {Six, G.},
  title  = {The {B}ost--{C}onnes {M}odular {G}enerator as a candidate
            {H}ilbert--{P}{\'o}lya {O}perator, formalized in {L}ean~4},
  year   = {2026},
  note   = {math.OA / math.NT submission. Companion Lean 4 formalization at
            \url{https://github.com/localparty/hilbert-polya-bost-connes-lean}
            (commit 53e12d8, v0.1); Zenodo DOIs pending publication.}
}
```

## License

CC-BY-4.0. See `LICENSE`.

## AI collaboration disclosure

During the preparation of this work, the author used Claude (Opus 4.7, Anthropic). The author reviewed all content and takes full responsibility for the paper.
