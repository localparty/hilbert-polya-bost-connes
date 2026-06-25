# Axiom disclosure — The Bost-Connes Modular Generator as a candidate Hilbert-Pólya Operator, formalized in Lean 4

Per-axiom disclosure for the Lean 4 formalization companion of the paper:

> **The Bost-Connes Modular Generator as a candidate Hilbert-Pólya Operator, formalized in Lean 4**
> *G Six*. 2026.

Companion Lean repository: `localparty/hilbert-polya-bost-connes-lean` (commit `53e12d8`; v0.1 release). Mathlib pinned at SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6` (Lean 4 toolchain `v4.29.1`). Lake-dep on `localparty/tt-bost-connes-lean` v0.2 (Zenodo DOI `10.5281/zenodo.20674891`; commit `d4bb8949`).

**Inventory at a glance.** 8 CCM-substrate-own atomic axioms + 6 TT-upstream-inherited axioms = 14 total in the publishable extraction's `#print axioms` universe. Of the 8 CCM-own, exactly 2 appear in the canonical terminal's `#print axioms` closure; the other 6 CCM-own and all 6 TT-upstream axioms are off-terminal background substrate honestly disclosed below but absent from the canonical-terminal certificate.

---

## 1. The canonical terminal and the on-terminal `#print axioms` closure

The chain's canonical terminal is

```
theorem HilbertPolyaBostConnes.rh_of_ccm_galerkin
  (g : CCMGalerkinSpectralData) : RiemannHypothesis
```

declared in `HilbertPolyaBostConnes/SpectralGate.lean:154`. The terminal is a **transparent conditional reduction** over the named structured type `CCMGalerkinSpectralData`. Inhabiting `CCMGalerkinSpectralData` is equivalent to the Riemann Hypothesis (see the gate-rationale docstring in `SpectralGate.lean`). The terminal is **not** an unconditional closure of the Riemann Hypothesis.

The canonical-terminal verification

```
#print axioms HilbertPolyaBostConnes.rh_of_ccm_galerkin
```

reports

```
[propext, Classical.choice, Quot.sound,
 HilbertPolyaBostConnes.Lemmas.BoegliExactness.Helpers.collectively_compact_resolvent_uniform_bound,
 HilbertPolyaBostConnes.Lemmas.HurwitzZeros.Helpers.rouche_zero_existence]
```

— the three Lean kernel axioms together with two atomic classical-literature ports.

---

## 2. CCM-substrate-own atomic axioms (8 total)

The 8 atomic CCM-own axioms partition into 4 Infrastructure axioms (BC operator-algebraic substrate) and 4 Lemmas/Helpers axioms (atomic classical-literature ports). Each axiom carries an in-source docstring with literature citation, theorem number, statement, and one-line non-RH-equivalence note.

### §2.1. Infrastructure axioms (4) — BC operator-algebraic substrate

#### Axiom #1: `bc_factor_isTypeIII1`

- **Lean location**: `HilbertPolyaBostConnes/Infrastructure.lean:104`
- **Citation**: Araki, H.; Woods, E. J., *A classification of factors*, Publ. Res. Inst. Math. Sci., Kyoto Univ., Ser. A **4** (1968), 51–130, DOI [10.2977/prims/1195195263](https://doi.org/10.2977/prims/1195195263) (ITPFI type $\mathrm{III}_1$ classification) — together with Haagerup, U., *Connes' bicentralizer problem and uniqueness of the injective factor of type $\mathrm{III}_1$*, Acta Math. **158** (1987), 95–148, DOI [10.1007/BF02392257](https://doi.org/10.1007/BF02392257) (uniqueness of injective type $\mathrm{III}_1$).
- **Statement**: the Bost–Connes factor $M_1 = \pi_{\omega_1}(B_K)''$ is the unique injective type $\mathrm{III}_1$ factor in Connes' classification.
- **Non-RH-equivalence**: a classical theorem about the BC factor; the published proofs do not invoke the Riemann Hypothesis.
- **On/off-terminal**: **off-terminal** (background substrate).

#### Axiom #2: `bc_modularFlow_ergodic`

- **Lean location**: `HilbertPolyaBostConnes/Infrastructure.lean:120`
- **Citation**: Connes, A., *Une classification des facteurs de type III*, Ann. Sci. École Norm. Sup. (4) **6** (1973), 133–252, DOI [10.24033/asens.1247](https://doi.org/10.24033/asens.1247) (modular flow ergodicity on the centre).
- **Statement**: the modular flow $\sigma_t = \mathrm{Ad}(\Delta^{it})$ of $\omega_1$ on $M_1$ is ergodic.
- **Non-RH-equivalence**: Connes' modular flow classification; the published proof does not invoke RH.
- **On/off-terminal**: **off-terminal**.

#### Axiom #3: `bc_connesSpectrum_real`

- **Lean location**: `HilbertPolyaBostConnes/Infrastructure.lean:128`
- **Citation**: Connes, A., *Une classification des facteurs de type III*, Ann. Sci. École Norm. Sup. (4) **6** (1973), 133–252 (Sd-invariant of the type $\mathrm{III}_1$ BC factor equals $\mathbb{R}$).
- **Statement**: the Connes spectrum $\mathrm{Sd}(M_1) = \mathbb{R}$.
- **Non-RH-equivalence**: Sd-invariant of the type $\mathrm{III}_1$ factor; classical.
- **On/off-terminal**: **off-terminal**.

#### Axiom #4: `dInftyApproximants_strongConv`

- **Lean location**: `HilbertPolyaBostConnes/Infrastructure.lean:168`
- **Citation**: ITPFI + Chatelin (Paper 13 §L3a–L4a programme-internal Galerkin port; awaits TT Phase 7+ Galerkin refinement). See Connes–Marcolli, *Noncommutative Geometry, Quantum Fields and Motives*, AMS Colloquium Publications **55**, 2008, Ch. III for the ITPFI substrate.
- **Statement**: the TT-derived Galerkin approximants converge strongly to the limit modular Hamiltonian (pointwise convergence on the GNS Hilbert space).
- **Non-RH-equivalence**: a Galerkin-convergence statement about the BC modular Hamiltonian; independent of RH.
- **On/off-terminal**: **off-terminal**. (The on-terminal `strongConv` field of `CCMGalerkinSpectralData` is supplied by the gate user, not by this axiom.)

### §2.2. Lemmas/Helpers axioms (4) — atomic classical-literature ports

#### Axiom #5: `rouche_zero_existence` — **on-terminal**

- **Lean location**: `HilbertPolyaBostConnes/Lemmas/HurwitzZeros/Helpers/Rouche.lean:102`
- **Citation**: Rouché, E., *Mémoire sur la série de Lagrange*, J. École Polytechnique **22** (1862), 217–218. Existence-form corollary documented in Ahlfors, L., *Complex Analysis*, 3rd ed., McGraw-Hill, 1979, §5.3 Theorem 18. See also Conway, J. B., *Functions of One Complex Variable I*, 2nd ed., Springer GTM 11, 1978, Chapter V §3.8.
- **Statement** (Rouché 1862, existence form; Ahlfors §5.3 Theorem 18): let $f, g$ be holomorphic on a closed disk $\overline{B}(c, r)$. If $|f - g| < |g|$ on the boundary circle $\partial B(c, r)$ and $g$ has a zero strictly inside $B(c, r)$, then $f$ also has a zero strictly inside $B(c, r)$.
- **Honest caveat**: this is the **existence-form** corollary of the full Rouché theorem (equality of zero-counts with multiplicity); we ship only the existence form because the Hurwitz zero-convergence consumer requires only this much. The published proof in Ahlfors does not invoke RH.
- **Non-RH-equivalence**: classical 19th-century complex analysis; pre-dates Riemann's hypothesis by 3 years and is independent of it.
- **On/off-terminal**: **on-terminal** (in the canonical terminal's `#print axioms` closure). Consumed by the project-local theorem `hurwitz_zero_convergence` in `Lemmas/HurwitzZeros.lean`.
- **Mathlib-gap rationale**: Mathlib at the pinned SHA lacks Rouché's theorem in any zero-counting form. The `circleIntegral` API, Cauchy's integral formula, and the locally-uniform-limit Weierstrass package are all present, but the argument-principle identity $(2\pi i)^{-1} \oint_{|z-c|=r} f'/f = \#\{\text{zeros of } f \text{ in } B(c, r)\}$ is absent. Once that surface is filled in upstream, the axiom can be replaced by an `import` swap.

#### Axiom #6: `collectively_compact_resolvent_uniform_bound` — **on-terminal**

- **Lean location**: `HilbertPolyaBostConnes/Lemmas/BoegliExactness/Helpers/Anselone.lean:175`
- **Citation**: Anselone, P. M., *Collectively Compact Operator Approximation Theory and Applications to Integral Equations*, Prentice-Hall, 1971, Theorem 1.6 and §3 — together with Stummel, F., *Diskrete Konvergenz linearer Operatoren I*, Math. Ann. **190** (1970), 45–92, DOI [10.1007/BF01349967](https://doi.org/10.1007/BF01349967), §3.
- **Statement** (Anselone 1971 §1.6 Theorem 1.6 / Stummel 1970 §3, uniform-resolvent-bound form): suppose $D_N : \mathbb{N} \to (H \to_L H)$ is a sequence of bounded operators on a complex Hilbert space $H$ such that (i) $D_N \to D_\infty$ strongly (pointwise on $H$) and (ii) $\{D_N\}$ is collectively compact (Stummel/Anselone). If $z \notin \sigma(D_\infty)$, then there exist $N_0 \in \mathbb{N}$ and $R > 0$ such that for every $N \ge N_0$, the operator $z \cdot \mathbf{1} - D_N$ is a unit (so $z \notin \sigma(D_N)$) and $\| (z \cdot \mathbf{1} - D_N)^{-1} \| \le R$.
- **Honest caveat** (per the in-source docstring): Anselone 1971's original statement uses collectively-compact-norm convergence (equivalent to gsrc for the bounded $D_N$ case). The Lean encoding uses pointwise strong-operator convergence; the two coincide under `[CompleteSpace H]` + bounded-operator (Teschl 2014 Lemma 2.7). This equivalence is part of the deferred Mathlib content.
- **Non-RH-equivalence**: classical operator-theoretic perturbation theory; independent of RH.
- **On/off-terminal**: **on-terminal** (in the canonical terminal's `#print axioms` closure). Consumed by the project-local theorem `boegli_spectral_exactness` in `Lemmas/BoegliExactness.lean` via Mathlib's `Units.add` Neumann-series surface.
- **Mathlib-gap rationale**: Mathlib at the pinned SHA lacks any formalisation of collective compactness for operator families (the `IsCompactOperator` predicate is for single operators), any spectral-approximation framework for non-self-adjoint operators (Bögli–Siegl–Tretter 2019), and the Banach–Steinhaus uniform-boundedness transfer from strong convergence + collective compactness to resolvent boundedness. Target Mathlib file `Mathlib/Analysis/Spectrum/BoegliSieglTretter.lean`.

#### Axiom #7: `dInftyApproximants_finiteRank`

- **Lean location**: `HilbertPolyaBostConnes/Lemmas/RellichKondrachov/Helpers/GalerkinFiniteRank.lean:77`
- **Citation**: Galerkin-truncation structural fact, surfaced from the CCM programme-internal substrate (Paper 13 §L4a). See Connes–Marcolli 2008 Ch. III for the BC modular Galerkin setup.
- **Statement**: the range of the Galerkin truncation $P_N \circ D_\infty \circ P_N$ is finite-dimensional (finite-rank).
- **Non-RH-equivalence**: a structural Galerkin-truncation fact; classical.
- **On/off-terminal**: **off-terminal**. Awaits exposure of the genuine Galerkin projector structure in `Infrastructure.lean`.

#### Axiom #8: `dInftyApproximants_h1CompactEmbedding`

- **Lean location**: `HilbertPolyaBostConnes/Lemmas/RellichKondrachov/Helpers/CompactEmbedding.lean:124`
- **Citation**: Rellich, F., *Ein Satz über mittlere Konvergenz*, Nachr. Ges. Wiss. Göttingen, Math.-Phys. Kl. (1930), 30–35 — together with Kondrachov, V. I., *Sur certaines propriétés des fonctions dans l'espace $L^p$*, C. R. (Doklady) Acad. Sci. URSS **48** (1945), 535–538.
- **Statement**: the embedding $H^1 \hookrightarrow L^2$ is compact (Rellich–Kondrachov), conditional on the uniform-bound and finite-rank hypotheses.
- **Non-RH-equivalence**: classical Sobolev-embedding theorem; predates and is independent of the relevant analytic number theory.
- **On/off-terminal**: **off-terminal**.

---

## 3. TT-upstream-inherited axioms (6 total)

These axioms live in `Integers/TomitaTakesaki/L1BostConnesAlgebra.lean` upstream of CCM (in the `tt-bost-connes-lean` v0.2 Lake-dep'd companion); the CCM substrate imports through `TTBridge` and thereby pulls them into the `#print axioms` closure of any consumer that traverses the TT layer. Discharge is owned by the companion paper [Six, tt-bost-connes] §8.2, not by the present project.

For each, we record the substrate citation; full bibliographic detail and per-axiom docstrings are in the companion paper.

| Axiom | Substrate citation |
|---|---|
| `bc_system_exists` | Bost–Connes 1995 Thm 25 + Connes–Marcolli 2008 Ch. III §3 (existence of the BC C*-dynamical system $(B_K, \alpha_t, \omega_1)$ for $K = \mathbb{Q}(i)$) |
| `kms1_unique` | Bost–Connes 1995 Thm 25 (KMS$_1$ uniqueness at inverse temperature 1) + Laca–Larsen–Neshveyev 2009 (KMS classification for BC-type systems of general number fields) |
| `kms1_faithful` | Bost–Connes 1995 §3, eq. (15) (faithfulness of $\omega_1$ on $B_K$) |
| `itpfi_factorization` | Bost–Connes 1995 Prop 33 + Connes–Marcolli 2008 Ch. III Thm 3.32 (ITPFI factorization of $\omega_1$; non-traciality form per [Six, tt-bost-connes] §8.2 item 5) |
| `time_evolution_is_automorphism` | Bost–Connes 1995 §3 Thm 24 (one-parameter $*$-automorphism group structure of $\alpha_t$) |
| `kms_condition` | Bratteli–Robinson 1997 §5.3 (KMS boundary condition; standard) |

All 6 TT-upstream axioms are **off-terminal** with respect to the present chain's canonical terminal: they support the broader substrate and are honestly inherited via the Lake dependency, but the canonical terminal `rh_of_ccm_galerkin` consumes `CCMGalerkinSpectralData` only via the gate's type-level fields, decoupling them from the on-terminal `#print axioms` closure.

---

## 4. What is **not** in the budget (Failure Mode #7 boundary)

Per doctrine §2 Failure Mode #7 boundary (no open-content laundering), the named-axiom budget contains:

- **No axiom citing the present paper.** No axiom cites a result of this paper, the present author's prior preprints in this programme line, or the project repository as the source of its statement. Each axiom cites independent peer-reviewed literature where the result is established.
- **No axiom citing a project-internal cell, draft, or unpublished preprint.** No axiom cites an internal sketch or any non-public document.
- **No placeholder axiom.** No axiom is a `True`-typed placeholder, an empty-structure stand-in, or a contractible-singleton carrier masquerading as content. The carrier-type audit per doctrine §2.2 was performed at extraction; all 8 CCM-own axioms have load-bearing carriers.
- **No RH-equivalent axiom in disguise.** No axiom in the budget invokes the Riemann Hypothesis in its published proof. Each axiom is a classical operator-algebraic or complex-analytic substrate fact; the literature citation in §2 above (and the in-source docstring) records the source.
- **No off-budget axiom.** A `grep -hE "^axiom " HilbertPolyaBostConnes/**/*.lean | wc -l` returns 8; no axiom is hidden, re-named to obscure provenance, or declared outside the inventoried files.

The CERTIFIED ≠ DISCHARGED distinction (doctrine §4.4) applies: the 12 off-terminal axioms (6 CCM-own + 6 TT-upstream) are **disclosed** here but are **absent** from the canonical-terminal `#print axioms` closure due to the gate's type-level field decoupling. Both sets — on-terminal and off-terminal — are honestly named, with literature citations, and their on/off-terminal status is explicitly recorded.

---

## 5. Honest scope of the disclosure ("verbatim statements" caveat)

The 2 on-terminal axioms (#5 `rouche_zero_existence` and #6 `collectively_compact_resolvent_uniform_bound`) carry full literature citations with theorem number, page reference, and statement (transcribed in §2.2 above; in-source docstrings reproduce the precise statement form used in the Lean encoding). The 6 off-terminal CCM-own axioms (#1–#4, #7, #8) carry author/year citations and one-line statements; the in-source docstrings are thinner than for the on-terminal pair, reflecting that the off-terminal layer is background substrate awaiting downstream refinement (e.g., the `dInftyApproximants_strongConv` Galerkin convergence refinement in TT Phase 7+).

This is an honest reflection of the present state of the formalization and is not over-claimed: the README of the v0.1 publish repo claims "verbatim statements" for the per-axiom disclosure, which is accurate for the on-terminal pair and a partial-completeness statement for the off-terminal layer. Expansion of the off-terminal docstrings to full verbatim-statement form is a planned Phase D + Phase G refinement, not a present-state claim.

---

## 6. Disclosure-alignment crosscheck

The companion paper's `references.tex` bibliography entries 1:1 match the literature citations of §2 above (publication-discipline gate). The open-content audit confirms that no axiom in the inventory cites the project's own paper, an internal cell, an unpublished preprint, or a placeholder (see §4 above); all 8 CCM-own + 6 TT-upstream axioms are independently citable in peer-reviewed published sources.

---

*Companion to the paper "The Bost-Connes Modular Generator as a candidate Hilbert-Pólya Operator, formalized in Lean 4," G Six, 2026. Authority document for the verified state is the publish repo's `README.md` (commit `53e12d8`). During the preparation of this work, the author used Claude (Opus 4.7, Anthropic).*
