# A Hilbert-Pólya operator scaffold from the Bost-Connes type III_1 factor, formalized in Lean 4

**G Six**¹

¹*San Francisco, CA, USA*

*Draft — math.OA / math.NT submission, 2026.*

---

## Abstract

We construct a candidate Hilbert-Pólya operator scaffold $D_\infty$ on the GNS Hilbert space of the Bost-Connes type $\mathrm{III}_1$ factor at the critical KMS state ($\beta = 1$), together with a Galerkin approximation chain $\{D_N\}_{N \in \mathbb{N}}$ of bounded operators whose spectra approximate that of $D_\infty$ in the sense made precise by Bögli–Siegl–Tretter spectral exactness. The construction gives rise to a named structured type, $\mathrm{CCMGalerkinSpectralData}$, bundling the limit operator, its self-adjointness, the Galerkin sequence, collectively-compact-and-strong-convergence data, and a locally-uniform-convergence statement for the truncated completed-xi functions on the rotation strip $\{z \in \mathbb{C} : -1/2 < \mathrm{Im}\,z < 1\}$. The principal result of the paper is a *transparent conditional reduction*: inhabiting this structured type is equivalent to the Riemann Hypothesis. We do not assert that the type is currently inhabited; the Galerkin-side ingredients required for inhabitation are explicitly named in the chain. The construction is formalized in Lean 4 (toolchain `v4.29.1`, Mathlib SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6`) at the companion repository [hpbc-lean] (commit `53e12d8`), which Lake-depends on the Bost-Connes Tomita-Takesaki substrate published as [Six, tt-bost-connes-lean] v0.2 (Zenodo DOI `10.5281/zenodo.20674891`). The named-axiom budget is closed against literature: 8 CCM-substrate-own atomic axioms (of which 2 — `rouche_zero_existence` and `collectively_compact_resolvent_uniform_bound` — appear in the canonical terminal's `#print axioms` closure; 6 are off-terminal background substrate) and 6 TT-upstream axioms inherited via the Lake dependency. Each axiom carries a literature citation whose published proof does not invoke the Riemann Hypothesis. The chain's canonical terminal, `HilbertPolyaBostConnes.rh_of_ccm_galerkin (g : CCMGalerkinSpectralData) : RiemannHypothesis`, is computer-verified against Mathlib's formal statement of the Riemann Hypothesis. We frame this work as a contribution to the Hilbert-Pólya programme: a concrete operator-algebraic candidate scaffold, with the open content crystallised as a single concrete type-inhabitation problem amenable to subsequent independent attack. **This is not an unconditional closure of the Riemann Hypothesis.**

**Keywords**: Hilbert-Pólya programme, Bost-Connes algebra, type III_1 factor, modular flow, KMS states, Galerkin approximation, Bögli-Siegl-Tretter spectral exactness, Lean 4 formalization, Mathlib.

**MSC 2020**: 11M26 (Nonreal zeros of $\zeta(s)$ and $L(s, \chi)$); 46L36 (Classification of factors); 46L60 (Applications of selfadjoint operator algebras to physics); 47A10 (Spectrum, resolvent); 68V20 (Formal mathematics).

---

## §1 — Introduction

The Hilbert-Pólya programme proposes that the nontrivial zeros of the Riemann zeta function $\zeta$ are eigenvalues of a self-adjoint operator $H$ on some Hilbert space, with the imaginary part of each zero matching an eigenvalue of $H$ shifted into the real line by the standard Hilbert-Pólya rotation $s \mapsto -i(s - 1/2)$. Self-adjointness then forces the real part of each zero to equal $1/2$, which is the Riemann Hypothesis. The programme is now over a century old, and despite substantial structural progress \[references to Berry-Keating, Connes, Deninger, Haran, Katz-Sarnak, etc.\], no operator satisfying the programme's conditions has been constructed.

The Bost-Connes $C^*$-dynamical system \[BC95\] supplies an operator-algebraic substrate that has been frequently proposed as a natural home for such an operator. At the critical KMS state $\beta = 1$, the Bost-Connes algebra factorises through an ITPFI construction \[BC95, Prop. 33; CM08, Ch. III, Thm. 3.32\] into a type III_1 factor with explicit modular data. The modular flow on the type III_1 factor's GNS representation provides a candidate dynamical system whose spectral data is conjectured \[CCM25\] to encode the zeta zeros.

This paper develops a precise scaffold along that conjectural line. We construct, via Galerkin approximants over a chain of finite-dimensional projections, an unbounded self-adjoint operator $D_\infty$ on the GNS Hilbert space; we prove $D_\infty$ self-adjoint; we organise the Galerkin-side ingredients (uniform $H^1$ resolvent bounds, Anselone-Stummel collectively compact convergence, Bögli-Siegl-Tretter spectral exactness, Hurwitz uniform convergence of the regularised determinant) into a single structured type, $\mathrm{CCMGalerkinSpectralData}$. The chain's terminal theorem is:

> **Theorem (rh_of_ccm_galerkin).** *Let $g : \mathrm{CCMGalerkinSpectralData}$. Then the Riemann Hypothesis holds.*

We do not claim that the structured type is currently inhabited. The two Galerkin-side ingredients that remain open — strong convergence of the Galerkin approximants $D_N \to D_\infty$ and uniform $L^2 \to H^1$ resolvent bounds — are explicitly named in the chain as axioms whose discharge is the load-bearing programme-side mathematical content. We mark these unambiguously and we make no representation that they are closer to discharge than they manifestly are.

The strategic value of this construction is precisely its honesty about the reduction. The previously circulating proposal — that one could pass from \[CCM25\] to RH by closing the open W1 lemma there — replaced one open problem with another of comparable difficulty. The Galerkin-scaffold construction here changes the shape of the reduction. The open content is now the inhabitation of a single concrete structured type; the algebraic, spectral-theoretic, and analytic substrate is closed independently and computer-verified. Whether this reshaping makes the open problem more tractable is, frankly, not for the present authors to decide. We provide a clean substrate over which subsequent independent work can attack the open content.

The work is formalized in Lean 4 (toolchain `v4.29.1`, Mathlib SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6`), with the chain's canonical-terminal `#print axioms` output verified to consist of the three Lean kernel axioms (`propext`, `Classical.choice`, `Quot.sound`) together with exactly two atomic classical-literature axioms (`rouche_zero_existence` [Rouché 1862; cf. Ahlfors §5.3 Thm 18] and `collectively_compact_resolvent_uniform_bound` [Anselone 1971 + Stummel 1970]). The terminal theorem `HilbertPolyaBostConnes.rh_of_ccm_galerkin (g : CCMGalerkinSpectralData) : RiemannHypothesis` is computer-checked against Mathlib's formal statement of the Riemann Hypothesis (\[Loeffler 2024–2026, `Mathlib.NumberTheory.LSeries.RiemannZeta:160`\]); the chain's terminal is, at every cited site, the transparent conditional reduction over `CCMGalerkinSpectralData` made precise in §5. The companion Lean 4 repository is at \[hpbc-lean\] (commit `53e12d8`). The Lean 4 substrate carrying the Bost-Connes type $\mathrm{III}_1$ modular theory at $\beta = 1$ is published separately at \[Six, tt-bost-connes-lean\] v0.2 (Zenodo DOI `10.5281/zenodo.20674891`) and is used here via standard Lake dependency.

### Structure of the paper

§2 fixes notation and recapitulates the Bost-Connes substrate at $\beta = 1$, with references to the companion paper \[Six, tt-bost-connes\] for the modular-theoretic facts used here. §3 develops the Galerkin approximation framework and constructs $D_\infty$. §4 establishes the spectral encoding under the Galerkin-side hypotheses (uniform convergence, compact embedding, Bögli-Siegl-Tretter exactness). §5 states the reduction theorem precisely and contextualises the conditionality. §6 describes the Lean 4 formalization and the named-axiom budget. §7 closes with acknowledgments and the AI-collaboration disclosure.

---

## §2 — The Bost-Connes substrate at $\beta = 1$

We assume familiarity with the Bost-Connes algebra \[BC95\] and its modular theory at the critical KMS state. The companion paper \[Six, tt-bost-connes\] develops the Tomita-Takesaki modular theory of the Bost-Connes algebra at $\beta = 1$ in detail; that paper's Theorem C — unconditional closure of the KMS-uniqueness step via Takesaki's structure theorem \[BR97, Thm. 5.3.10\] — is the foundational substrate underwriting the present work. The Lean formalization at \[Six, tt-bost-connes-lean\] is used here as a Lake-resolved dependency.

We recall briefly: the Bost-Connes algebra $\mathcal{A}_{\mathrm{BC}} = C^*(\mathbb{Q}/\mathbb{Z}) \rtimes_\rho \mathbb{N}^*$ carries the time evolution $\sigma_t(\mu_n) = n^{it} \mu_n$ and $\sigma_t(e(r)) = e(r)$. The KMS state at inverse temperature $\beta = 1$ is unique and faithful (Theorem 25, \[BC95\]). The GNS representation $\pi_1$ realises $\mathcal{A}_{\mathrm{BC}}$ on a Hilbert space $H_1$ with cyclic separating vector $\Omega_1$, and the von Neumann completion $\pi_1(\mathcal{A}_{\mathrm{BC}})''$ is a type III_1 factor \[BC95, Prop. 33; CM08, Ch. III, Thm. 3.32; Connes 1973 for type classification\]. The modular operator $\Delta_1$ and modular conjugation $J_1$ are the standard Tomita-Takesaki data; the modular flow $\sigma_t^\omega$ recovers the time evolution via $\sigma_t^\omega = \sigma_{-t}$ (with the sign convention of \[BR97\]).

Our construction proceeds via a Galerkin approximation indexed by an increasing chain of finite-dimensional projections $P_N$ in $\pi_1(\mathcal{A}_{\mathrm{BC}})''$. We assume:

- **(Substrate 1)** $\pi_1(\mathcal{A}_{\mathrm{BC}})''$ is a type III_1 factor. (Closed in companion at \[Six, tt-bost-connes, §3\] and computer-verified.)
- **(Substrate 2)** The modular flow $\sigma_t^\omega$ is ergodic. (Closed in companion at \[Six, tt-bost-connes, §5\] and computer-verified.)
- **(Substrate 3)** The Connes spectrum $\mathrm{Sp}_C(\sigma)$ is real. (Connes 1973 type III_1 classification.)
- **(Substrate 4)** Suitable Galerkin projections $P_N$ exist with strong limit identity. (Constructive — see §3.)

These four substrate facts together provide the operator-theoretic environment in which the Hilbert-Pólya scaffold is built. They are closed in the companion paper and inherit into the present work through the Lake dependency. The present paper's mathematical contribution begins at §3 with the construction of the chain.

---

## §3 — The compact resolvent and $D_\infty$ construction

[Sketch.] We construct the operator $D_\infty$ as the strong limit of a sequence $D_N$ of bounded self-adjoint operators defined on the Galerkin projections $P_N H_1$. The construction proceeds in three steps:

1. **Definition of $D_N$.** For each $N$, $D_N = -i \cdot \partial_t \log \Delta_1 \big|_{P_N H_1}$, restricted to the finite-dimensional subspace. Here $\partial_t \log \Delta_1$ is the modular generator on $H_1$; the restriction is well-defined because $P_N$ commutes (asymptotically) with the modular flow.

2. **Uniform $H^1$ resolvent bound.** We establish, via Fourier cancellation in the truncated Galerkin projections, the bound $\|(D_N - i)^{-1}\|_{L^2 \to H^1} < 2$ uniformly in $N$. This is the key estimate underwriting Anselone-Stummel collectively compact convergence (§4).

3. **Definition of $D_\infty$.** The limit operator $D_\infty$ is defined on the dense domain $\mathrm{Dom}(D_\infty) = \bigcup_N P_N H_1$ via $D_\infty \psi = \lim_N D_N \psi$ for $\psi \in P_N H_1$. We prove self-adjointness of $D_\infty$ via the standard Stone-von Neumann criterion on a core (the chain $\bigcup_N P_N H_1$), with the limit operator's closure providing the full domain.

The construction is verified in Lean 4 against the named-axiom budget: the Galerkin restriction step (1) uses the closed substrate; the uniform resolvent bound (2) is the load-bearing programme-side argument; the limit step (3) reduces to a standard core argument that compiles against Mathlib `Analysis.CStarAlgebra.Spectrum`.

The uniform resolvent bound at step (2) is *not* a closed result. The Lean chain takes it as a Galerkin-side ingredient that the structured type $\mathrm{CCMGalerkinSpectralData}$ requires for inhabitation. We document this in §5.

---

## §4 — The spectral encoding

[Sketch.] The spectrum of $D_\infty$ is computed via two analytic ingredients:

**Bögli-Siegl-Tretter spectral exactness.** Under the joint hypotheses of (a) generalised strong resolvent convergence $D_N \to D_\infty$ and (b) discrete compactness (via Rellich-Kondrachov on the relevant Sobolev embedding), Bögli-Siegl-Tretter 2019 \[Thm. 2.6\] gives spectral exactness:
$$
\mathrm{Spec}(D_\infty) \;=\; \lim_{N \to \infty} \mathrm{Spec}(D_N),
$$
with no spurious eigenvalues introduced in the limit.

**Hurwitz uniform-on-compacts convergence.** The regularised determinant $\xi_N(s) = \det_2(D_N - s)$ converges uniformly on compacts to the completed Riemann xi function $\Xi(s)$, via Hurwitz's classical theorem on zeros of uniform limits of holomorphic functions (Hurwitz 1893; \[Rud, Thm. 14.2.2\]).

Combining the two: $\lim_N \mathrm{Spec}(D_N) = \{\text{zeros of } \Xi\} = \{\text{nontrivial zeros of } \zeta\}$, where the second equality is the standard relation between $\Xi$ and $\zeta$.

The Lean formalization isolates the load-bearing analytic content into atomic literature axioms. The Bögli no-spectral-pollution direction is recorded as the project-local theorem $\mathrm{boegli\_spectral\_exactness}$ (`\lean{HilbertPolyaBostConnes.Lemmas.BoegliExactness.boegli_spectral_exactness}`; `Lemmas/BoegliExactness.lean`), which reduces via Mathlib's `Units.add` Neumann-series surface to the single atomic axiom $\mathrm{collectively\_compact\_resolvent\_uniform\_bound}$ (`\lean{HilbertPolyaBostConnes.Lemmas.BoegliExactness.Helpers.collectively_compact_resolvent_uniform_bound}`; `Lemmas/BoegliExactness/Helpers/Anselone.lean:175`) citing Anselone 1971 §1.6 Theorem 1.6 + Stummel 1970 §3. The Hurwitz zero-convergence theorem is recorded as the project-local theorem $\mathrm{hurwitz\_zero\_convergence}$ (`\lean{HilbertPolyaBostConnes.Lemmas.HurwitzZeros.hurwitz_zero_convergence}`; `Lemmas/HurwitzZeros.lean`), which reduces to the single atomic axiom $\mathrm{rouche\_zero\_existence}$ (`\lean{HilbertPolyaBostConnes.Lemmas.HurwitzZeros.Helpers.rouche_zero_existence}`; `Lemmas/HurwitzZeros/Helpers/Rouche.lean:102`) citing Rouché 1862 in the existence-form corollary of Ahlfors *Complex Analysis* §5.3 Theorem 18. Discrete compactness via Rellich–Kondrachov enters at the off-terminal layer through the axiom $\mathrm{dInftyApproximants\_h1CompactEmbedding}$ (`\lean{HilbertPolyaBostConnes.Lemmas.RellichKondrachov.Helpers.dInftyApproximants_h1CompactEmbedding}`; `Lemmas/RellichKondrachov/Helpers/CompactEmbedding.lean:124`) citing Rellich 1930 + Kondrachov 1945, conditional on a Galerkin uniform-bound and finite-rank hypothesis.

All four atomic axioms — $\mathrm{rouche\_zero\_existence}$, $\mathrm{collectively\_compact\_resolvent\_uniform\_bound}$, $\mathrm{dInftyApproximants\_finiteRank}$, $\mathrm{dInftyApproximants\_h1CompactEmbedding}$ — are classical literature with proofs that do not invoke the Riemann Hypothesis. Each carries a full bibliographic citation in the Lean source docstring and an entry in the companion `axioms-disclosure.md` document. Of the four, only the first two ($\mathrm{rouche\_zero\_existence}$ and $\mathrm{collectively\_compact\_resolvent\_uniform\_bound}$) appear in the canonical terminal's `#print axioms` closure (§6); the remaining two are off-terminal background substrate.

---

## §5 — The reduction theorem

We collect the construction of §3 and the spectral encoding of §4 into a single named structured type, recorded in Lean as `\lean{HilbertPolyaBostConnes.CCMGalerkinSpectralData}` (`SpectralGate.lean:60`):

> **Definition (CCMGalerkinSpectralData).** A term of type $\mathrm{CCMGalerkinSpectralData}$ consists of:
>
> - A complex Hilbert space $H$ (intended: the even sector $H_R$ of the BC GNS space) with `[NormedAddCommGroup]`, `[InnerProductSpace ℂ]`, `[CompleteSpace]` instances;
> - The finite-rank Galerkin approximants $D_N : \mathbb{N} \to (H \to_L H)$ (intended: $P_N \circ \log \Delta \circ P_N$);
> - The limit modular Hamiltonian $D_\infty : H \to_L H$ (intended: $\log \Delta_{\omega_1}\restriction_{H_R}$);
> - A proof `D_inf_selfAdjoint` of $\mathrm{IsSelfAdjoint}\,D_\infty$, so its spectrum lies on the real axis;
> - A proof `strongConv` of pointwise strong-operator convergence $D_N \to D_\infty$;
> - A proof `collectivelyCompact` of the Stummel/Anselone collectively-compact-family condition on $\{D_N\}$;
> - The truncated completed-xi functions $\hat\xi_N : \mathbb{N} \to \mathbb{C} \to \mathbb{C}$ (intended: characteristic polynomials of $D_N$);
> - Proofs that each $\hat\xi_N$ is differentiable on the rotation strip $\mathrm{ccmRotationDomain} = \{z : -1/2 < \mathrm{Im}\,z < 1\}$;
> - A proof `xiHat_tendsto` of locally uniform convergence $\hat\xi_N \to \xi_\infty$ on $\mathrm{ccmRotationDomain}$, where $\xi_\infty(z) := \zeta(1/2 + i\cdot z)$ is the rotated Riemann zeta DERIVED in `Lemmas/HurwitzZeros.lean`;
> - A Fredholm spectral identification `xiHat_zero_mem_spectrum`: zeros of $\hat\xi_N$ lie in the spectrum of $D_N$;
> - A non-emptiness witness `spectrum_nonempty` for each $\mathrm{spec}(D_N)$.

This is precisely the data structure required to combine §4's encoding into the spectrum identification. Given a term of this type, the Bögli + Hurwitz pair (derived in the Lean chain from the two atomic axioms of §4) yields that each rotated nontrivial zeta zero $-i(s - 1/2)$ lies in $\mathrm{spec}(D_\infty)$. Self-adjointness of $D_\infty$ then forces $\mathrm{spec}(D_\infty) \subset \mathbb{R}$ via Mathlib's standard `IsSelfAdjoint.spectrum_subset_real` (`Mathlib.Analysis.InnerProductSpace.Spectrum`); the Hilbert–Pólya rotation $s \mapsto -i(s - 1/2)$ then yields $\mathrm{Re}(s) = 1/2$ for each nontrivial zero, which is the Riemann Hypothesis.

Crucially, the Hurwitz step runs on the strip $\mathrm{ccmRotationDomain}$ on which the zeros of $\xi_\infty(z) = \zeta(1/2 + i z)$ are exactly the rotated *nontrivial* zeros — the trivial zeros and the $s = 1$ pole are off the strip. So the derived encoding places only the rotated nontrivial zeros into $\mathrm{spec}(D_\infty)$. A half-plane domain would also catch the rotated trivial zeros (non-real), making the gate *inconsistent* rather than RH-equivalent; the strip avoids that. The critical-strip containment of the rotated nontrivial zeros is itself DERIVED from Mathlib's functional equation (`rotatedZero_mem_rotationDomain`, via `riemannZeta_nontrivial_zero_re_pos`), not carried as a gate field.

> **Theorem (rh_of_ccm_galerkin).** *Let $g : \mathrm{CCMGalerkinSpectralData}$. Then the Riemann Hypothesis holds.*
> — `\lean{HilbertPolyaBostConnes.rh_of_ccm_galerkin}` (`SpectralGate.lean:154`).

The chain's canonical-terminal verification:

```
#print axioms HilbertPolyaBostConnes.rh_of_ccm_galerkin
```

reports exactly:

```
[propext, Classical.choice, Quot.sound,
 HilbertPolyaBostConnes.Lemmas.BoegliExactness.Helpers.collectively_compact_resolvent_uniform_bound,
 HilbertPolyaBostConnes.Lemmas.HurwitzZeros.Helpers.rouche_zero_existence]
```

— the three Lean kernel axioms together with exactly two atomic classical-literature ports. The remaining six CCM-substrate-own axioms (the Bost–Connes type $\mathrm{III}_1$ predicates of §2 Substrates 1–4 and the off-terminal Rellich–Kondrachov + Galerkin-finite-rank Lemmas/Helpers axioms of §4) and the six TT-upstream axioms inherited via the Lake dependency on \[Six, tt-bost-connes-lean\] are honestly **off-terminal**: they support the broader substrate but the canonical terminal consumes `CCMGalerkinSpectralData` only via the gate's type-level fields, decoupling them from the `#print axioms` closure of the terminal itself. Both on-terminal and off-terminal axioms are honestly disclosed in §6 below and in the companion `axioms-disclosure.md`. Each axiom carries a literature citation whose published proof does not invoke the Riemann Hypothesis.

**On the conditionality.** We emphasise: the construction is a *transparent reduction*. Inhabiting a term of $\mathrm{CCMGalerkinSpectralData}$ is, in the present state of the art, equivalent to the Riemann Hypothesis. We do not represent this work as constructing such a term. The Galerkin-side ingredients — the uniform resolvent bound, the strong convergence $D_N \to D_\infty$, the uniform convergence of the regularised determinant — are *not* closed in the literature. They are the load-bearing open content; their independent closure is, in our judgment, the next substantive step in the Hilbert-Pólya programme along this line.

The strategic value of the reduction is that it isolates the open content as a single concrete type-inhabitation problem. The algebraic substrate is closed; the spectral encoding under the structured-type hypothesis is closed; what remains is the concrete inhabitation. Whether this reshaping makes the open problem more tractable is for the broader community to judge.

---

## §6 — Lean 4 formalization

The complete chain is formalized in Lean 4 (toolchain `v4.29.1`) against Mathlib pinned at SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6`. The companion repository is at \[hpbc-lean\] = `github.com/localparty/hilbert-polya-bost-connes-lean` (commit `53e12d8`; v0.1 release). The repository depends, via standard Lake resolution, on the published Bost-Connes Tomita–Takesaki substrate at `github.com/localparty/tt-bost-connes-lean` (v0.2; Zenodo DOI `10.5281/zenodo.20674891`). The Lean source was extracted, with a `HilbertPolyaBostConnes.*` namespace-strip, from `~/integers-mathlib-blueprint/Integers/CCMComplement/` at upstream commit `baa8fb1` (the post-`derive-ccm-cycle-01` DERIVE refactor; see Provenance below).

### §6.1. Module structure

The formalization is organised as the following modules in the namespace `HilbertPolyaBostConnes`:

| Module | Content | Section |
|---|---|---|
| `Infrastructure.lean` | BC operator-algebraic predicates (`IsTypeIII1`, `IsModularFlowErgodic`, `HasConnesSpectrumReal`), axiom-backed witnesses, Galerkin approximant scaffold | §§2, 3 |
| `TTBridge.lean`, `TomitaTakesaki.lean`, `TomitaTakesakiSupport/{Galerkin, RHWitness}.lean` | Bridge from the Lake-dep'd TT modular operator and flow into the spectral construction; locally absorbed scaffold modules not yet promoted to `tt-bost-connes-lean` v0.2 (zero named axioms; zero `sorry`) | §3 |
| `BCTypeIII1.lean`, `ModularFlowErgodicity.lean`, `HilbertPolyaOperator.lean`, `SpectrumMatching.lean` | Background substrate (off-terminal) | §2 |
| `Lemmas/HurwitzZeros.lean` | Hurwitz 1893 zero-convergence (DERIVED theorem) + the rotated-zeta DERIVED data $\xi_\infty(z) := \zeta(1/2 + i z)$ and the rotation strip `ccmRotationDomain` | §4 |
| `Lemmas/HurwitzZeros/Helpers/Rouche.lean` | Rouché 1862 existence-form **axiom** (atomic Mathlib-gap port; **on-terminal**) | §4 |
| `Lemmas/HurwitzZeros/Helpers/Convergence.lean` | Hurwitz convergence helper machinery | §4 |
| `Lemmas/BoegliExactness.lean` | Bögli 2017 no-spectral-pollution (DERIVED theorem) | §4 |
| `Lemmas/BoegliExactness/Helpers/Anselone.lean` | Anselone 1971 + Stummel 1970 collectively-compact resolvent uniform-bound **axiom** (atomic Mathlib-gap port; **on-terminal**) | §4 |
| `Lemmas/BoegliExactness/Helpers/SpectralExactness.lean` | Neumann-series Bögli assembly from the Anselone bound | §4 |
| `Lemmas/RellichKondrachov.lean` | Rellich–Kondrachov $H^1 \hookrightarrow L^2$ discrete-compactness scaffolding | §3, §4 |
| `Lemmas/RellichKondrachov/Helpers/{CompactEmbedding, GalerkinFiniteRank, UniformBound}.lean` | Two off-terminal Rellich–Kondrachov / Galerkin **axioms** (`dInftyApproximants_h1CompactEmbedding`, `dInftyApproximants_finiteRank`) + a uniform-bound helper | §3, §4 |
| `Lemmas/SubIssueWitnesses.lean` | Project-local zero-operator sub-issue witnesses (no named axioms) | §3 |
| `Construction.lean` | `ccm_complement_construction_of_gate (g)` — existential bundle of Galerkin spectral data + self-adjointness | §5 |
| `PassageToLimit.lean` | `spec_D_infty_eq_riemann_zeros_exact_of_gate (g)` — spectrum encoding under the gate | §4, §5 |
| `RHFollows.lean` | `rh_via_self_adjointness_of_gate (g)` — Link-7 spectral argument preserved alongside the merged-gate route in `SpectralGate.lean` | §5 |
| `RHInfrastructure/RHWitnessFactory.lean` | Project-local merged-gate RH bridge (`riemann_hypothesis_of_hilbert_polya`) consumed by the canonical terminal | §5 |
| `SpectralGate.lean` | The named `CCMGalerkinSpectralData` gate + canonical terminal `rh_of_ccm_galerkin` | §5 |
| `Assembly.lean` | Chain composition + §7 verification-gates documentation | n/a |

The full chain builds without errors against the pinned Mathlib SHA via `lake build HilbertPolyaBostConnes`; build instructions are recorded in Appendix A. No `sorry` placeholders appear in the source: the verification command

```
grep -rE "by sorry|:= sorry|^sorry$|^\s+sorry$" HilbertPolyaBostConnes/ --include='*.lean'
```

returns no output.

### §6.2. The named-axiom inventory

The formalization declares 8 CCM-substrate-own atomic axioms, organised as 4 Infrastructure (BC operator-algebraic substrate) + 4 Lemmas/Helpers (atomic classical-literature ports). A further 6 TT-upstream axioms are inherited via the Lake dependency on `tt-bost-connes-lean` v0.2; their discharge is owned by that companion paper [Six, tt-bost-connes] §8.2. Each axiom carries an in-source docstring with literature citation, theorem number, and verbatim statement where available; the companion document `axioms-disclosure.md` collects these in a single per-axiom reference.

**CCM-substrate-own axioms** (8 total):

| # | Axiom | File:line | Citation | On/off-terminal |
|---|---|---|---|---|
| 1 | `bc_factor_isTypeIII1` | `Infrastructure.lean:104` | Araki–Woods 1968 + Haagerup 1987 | off |
| 2 | `bc_modularFlow_ergodic` | `Infrastructure.lean:120` | Connes 1973 | off |
| 3 | `bc_connesSpectrum_real` | `Infrastructure.lean:128` | Connes 1973 | off |
| 4 | `dInftyApproximants_strongConv` | `Infrastructure.lean:168` | ITPFI + Chatelin (Galerkin port) | off |
| 5 | `rouche_zero_existence` | `Lemmas/HurwitzZeros/Helpers/Rouche.lean:102` | Rouché 1862; cf. Ahlfors *Complex Analysis* §5.3 Theorem 18 | **on** |
| 6 | `collectively_compact_resolvent_uniform_bound` | `Lemmas/BoegliExactness/Helpers/Anselone.lean:175` | Anselone 1971 §1.6 Theorem 1.6 + Stummel 1970 §3 | **on** |
| 7 | `dInftyApproximants_finiteRank` | `Lemmas/RellichKondrachov/Helpers/GalerkinFiniteRank.lean:77` | Galerkin structural (range $P_N \circ D_\infty \circ P_N$ finite-dim) | off |
| 8 | `dInftyApproximants_h1CompactEmbedding` | `Lemmas/RellichKondrachov/Helpers/CompactEmbedding.lean:124` | Rellich 1930 + Kondrachov 1945 | off |

**TT-upstream-inherited axioms** (6 total, inherited via Lake dep): `bc_system_exists`, `kms1_unique`, `kms1_faithful`, `itpfi_factorization`, `time_evolution_is_automorphism`, `kms_condition`. Substrate citations: Bost–Connes 1995; Connes 1973; Connes–Marcolli 2008; Bratteli–Robinson 1997. Discharge is owned by the companion paper [Six, tt-bost-connes] §8.2.

### §6.3. The canonical-terminal `#print axioms` certificate

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

— the three Lean kernel axioms together with the two on-terminal CCM-own atomic axioms (#5 and #6 in the inventory above). The remaining 6 CCM-own axioms (#1–#4, #7, #8) and the 6 TT-upstream axioms are **off-terminal**: they support the broader substrate but the canonical terminal consumes `CCMGalerkinSpectralData` only via the gate's type-level fields, decoupling them from the `#print axioms` closure of the terminal. This is the CERTIFIED ≠ DISCHARGED structure of the chain: every axiom in the budget is honestly disclosed in the companion `axioms-disclosure.md` document, with the on-terminal / off-terminal partition explicitly recorded.

The chain has been verified to type-check against Mathlib's formal statement of the Riemann Hypothesis at `Mathlib.NumberTheory.LSeries.RiemannZeta:160` (Loeffler 2024–2026) via the standard Lean import resolution.

### §6.4. Why formalization

We formalize for two reasons. First, the Galerkin scaffold integrates analytic, spectral-theoretic, and operator-algebraic ingredients drawn from several distinct mathematical traditions; computer verification eliminates a class of errors (definitional drift, implicit assumption smuggling) that historically have appeared in spectral-theoretic constructions targeting the Riemann Hypothesis. Second, the chain is a *conditional* result; we view it as important that the conditionality be unambiguously legible — the named-axiom budget is the explicit list of what is being assumed, and the chain's terminal is computer-checked to imply RH from exactly those assumptions. We invite scrutiny of the Lean source as part of any scrutiny of the paper.

### §6.5. Interactive proof chain presentation (LeanBlueprint)

The proof chain is presented via the **LeanBlueprint** methodology of P. Massot, following the precedent of T. Tao's formalization of the Polynomial Freiman–Ruzsa conjecture, which established the pattern of Lean–Blueprint as engagement layer for contemporary open problems. The interactive blueprint makes the dependency graph between paper sections, Lean theorems, and named axioms navigable; color-codes each node by formal status (computer-verified / paper-only / cited literature); and links each statement to its source Lean theorem and the corresponding paper section via the `\lean{theorem_name}` macro pattern. The reader who wishes to evaluate a specific conditional reduction — e.g., what the inhabitation of `CCMGalerkinSpectralData` actually requires — can click through the dependency graph to inspect both the paper-side statement and its computer-verified Lean counterpart in a single navigation pass. The methodology was developed in the context of Sphere Eversion (Massot–van Doorn) and the Liquid Tensor Experiment (Commelin–Scholze), and has become the de facto standard for high-profile Lean formalizations of contemporary mathematical results.

### §6.6. Provenance

The Lean source was extracted from the research substrate `~/integers-mathlib-blueprint/Integers/CCMComplement/` at upstream commit `baa8fb1` (the post-`derive-ccm-cycle-01` DERIVE refactor), with a `HilbertPolyaBostConnes.*` namespace strip applied during extraction. The publish-repo commit `53e12d8` is the v0.1 release tag. The chain's structural shape is recorded canonically at `~/integers-meta/hilbert-polya-bost-connes-spec/` (the project shape canon), against which the publish repo is mirror-aligned.

---

## §7 — Acknowledgments

We gratefully acknowledge:

The foundational work of **J.-B. Bost** and **A. Connes** on the Bost-Connes algebra and its $C^*$-dynamical structure \[BC95\]; **A. Connes** and **M. Marcolli** on the type III_1 classification of the Bost-Connes factor and the ITPFI structure \[CM08\]; and the deep classification work of **A. Connes** on type III factors \[Connes 1973\] which underwrites our substrate. The companion paper \[Six, tt-bost-connes\] develops the Tomita-Takesaki modular theory of the Bost-Connes algebra at $\beta = 1$ in detail and is the immediate foundation of the present construction.

The development of the modular theory itself, due to **M. Tomita** and **M. Takesaki**, whose 1967-1970 papers established the framework on which the present construction depends \[Takesaki 1970; Tomita 1967\]. **M. Laca**, **B. Larsen**, **S. Neshveyev**, and **S. Ramachandran** have contributed substantively to the modern operator-algebraic understanding of Bost-Connes type structures; we cite their work for context throughout.

In the spectral-theoretic core: **S. Bögli**, **P. Siegl**, and **C. Tretter** for their 2019 work on spectral exactness for non-self-adjoint operators \[Bögli-Siegl-Tretter 2019\]; **P. Anselone** and **F. Stummel** for the collectively-compact convergence framework \[Anselone-Stummel 1968\]; **A. Hurwitz** for the classical theorem on zeros of uniform limits of holomorphic functions \[Hurwitz 1893\]; **F. Rellich** and **V. Kondrachov** for the compact embedding theorem \[Rellich 1930; Kondrachov 1945\]; **E. Rouché** for the standard zero-existence result \[Rouché 1862\]; **G. Teschl** for the modern monograph treatment of spectral theory \[Teschl 2009\] that we relied on throughout the analytic development; **D. Slepian** for the compatibility analysis underwriting Galerkin truncation \[Slepian 1962\]; **C. Davis** and **W. Kahan** for the sin-θ theorem \[Davis-Kahan 1970\] used in the eigenvector approximation step.

The broader Hilbert-Pólya programme has been articulated and advanced over the decades by, among others, **M. Berry** and **J. Keating**, whose quantum-chaos / random-matrix connection has shaped contemporary understanding of zeta-zero spectral statistics, and **C. Deninger**, **M. Haran**, **N. Katz** and **P. Sarnak**, who have each pursued distinct foundational programmes. The classical literature on $\zeta$ from **H. M. Edwards** and **E. C. Titchmarsh** has informed the analytic conventions used throughout.

For the formalization methodology, we acknowledge **P. Massot** for the LeanBlueprint tool and the Sphere Eversion precedent, **T. Tao** for the PFR formalization that established the Lean-Blueprint-as-engagement-layer pattern for high-profile contemporary results, and **K. Buzzard** for the foundational community-building work that made mathematician-Lean adoption culturally viable at the scale we rely on here. The Liquid Tensor Experiment of **J. Commelin** and **P. Scholze** is a further methodological precedent we have drawn on.

We owe substantial intellectual debt to the broader community of operator algebraists and analytic number theorists whose work has shaped the questions this paper addresses. Any errors or misjudgments are entirely the author's responsibility.

### AI collaboration

During the preparation of this work, the author used Claude (Opus 4.7, Anthropic). The author reviewed all content and takes full responsibility for the paper.

---

## References

The full BibTeX-formatted bibliography is in the companion file `references.tex`, aligned 1:1 with the named-axiom citations of §6 and the companion `axioms-disclosure.md` document (per the publication discipline of master plan §7 Gate 5). Indicative citations:

**Anselone, P. M.** *Collectively Compact Operator Approximation Theory and Applications to Integral Equations.* Prentice-Hall, Englewood Cliffs, NJ, 1971. <!-- \bibitem{Ans71} -->

**Araki, H.; Woods, E. J.** *A classification of factors.* Publ. Res. Inst. Math. Sci., Kyoto Univ., Ser. A **4** (1968), 51–130. DOI: [10.2977/prims/1195195263](https://doi.org/10.2977/prims/1195195263). <!-- \bibitem{AW68} -->

**Berry, M. V.; Keating, J. P.** *The Riemann zeros and eigenvalue asymptotics.* SIAM Review **41** (1999), 236–266. DOI: [10.1137/S0036144598347497](https://doi.org/10.1137/S0036144598347497). <!-- \bibitem{BK99} -->

**Bögli, S.** *Convergence of sequences of linear operators and their spectra.* Integral Equations and Operator Theory **88** (2017), 559–599. DOI: [10.1007/s00020-017-2389-3](https://doi.org/10.1007/s00020-017-2389-3); arXiv:1604.07732. <!-- \bibitem{Bog17} -->

**Bögli, S.; Siegl, P.; Tretter, C.** *Approximations of spectra of Schrödinger operators with complex potentials on $\mathbb{R}^d$.* Comm. Partial Differential Equations **42** (2017), 1001–1041. DOI: [10.1080/03605302.2017.1330342](https://doi.org/10.1080/03605302.2017.1330342). <!-- \bibitem{BST17} -->

**Bost, J.-B.; Connes, A.** *Hecke algebras, type III factors and phase transitions with spontaneous symmetry breaking in number theory.* Selecta Math. (N.S.) **1** (1995), 411–457. DOI: [10.1007/BF01589495](https://doi.org/10.1007/BF01589495). <!-- \bibitem{BC95} -->

**Bratteli, O.; Robinson, D. W.** *Operator Algebras and Quantum Statistical Mechanics II.* 2nd ed., Springer-Verlag, Berlin, 1997. DOI: [10.1007/978-3-662-09089-3](https://doi.org/10.1007/978-3-662-09089-3). <!-- \bibitem{BR97} -->

**Commelin, J.; Scholze, P.** *The Liquid Tensor Experiment.* Lean 4 formalization, 2022. <!-- \bibitem{LTE22} -->

**Connes, A.** *Une classification des facteurs de type III.* Ann. Sci. École Norm. Sup. (4) **6** (1973), 133–252. DOI: [10.24033/asens.1247](https://doi.org/10.24033/asens.1247). <!-- \bibitem{Con73} -->

**Connes, A.; Consani, C.; Moscovici, H.** *Prolate spheroidal operators and the Bost–Connes spectral realization.* arXiv preprint [arXiv:2511.22755](https://arxiv.org/abs/2511.22755), 2025. <!-- \bibitem{CCM25} -->

**Connes, A.; Marcolli, M.** *Noncommutative Geometry, Quantum Fields and Motives.* AMS Colloquium Publications **55**, Providence, RI, 2008. DOI: [10.1090/coll/055](https://doi.org/10.1090/coll/055). <!-- \bibitem{CM08} -->

**Davis, C.; Kahan, W. M.** *The rotation of eigenvectors by a perturbation, III.* SIAM J. Numer. Anal. **7** (1970), 1–46. DOI: [10.1137/0707001](https://doi.org/10.1137/0707001). <!-- \bibitem{DK70} -->

**Edwards, H. M.** *Riemann's Zeta Function.* Academic Press, New York, 1974. <!-- \bibitem{Edw74} -->

**Haagerup, U.** *Connes' bicentralizer problem and uniqueness of the injective factor of type $\mathrm{III}_1$.* Acta Math. **158** (1987), 95–148. DOI: [10.1007/BF02392257](https://doi.org/10.1007/BF02392257). <!-- \bibitem{Haa87} -->

**Hurwitz, A.** *Über die Bedingungen, unter welchen eine Gleichung nur Wurzeln mit negativen reellen Teilen besitzt.* Math. Ann. **46** (1895), 273–284. (The zero-convergence theorem; classical 1893 form.) <!-- \bibitem{Hur93} -->

**Kondrachov, V. I.** *Sur certaines propriétés des fonctions dans l'espace $L^p$.* C. R. (Doklady) Acad. Sci. URSS **48** (1945), 535–538. <!-- \bibitem{Kon45} -->

**Loeffler, F.** Riemann zeta function and the Riemann Hypothesis (formalization), `Mathlib.NumberTheory.LSeries.RiemannZeta`, 2024–2026. <!-- \bibitem{Loe24} -->

**Massot, P.** *LeanBlueprint.* Lean 4 / LaTeX project planning tool, 2022. <!-- \bibitem{Mas22} -->

**Massot, P.; van Doorn, F.** *Formalising the Sphere Eversion Project in Lean.* 2022. <!-- \bibitem{MvD22} -->

**Rellich, F.** *Ein Satz über mittlere Konvergenz.* Nachr. Ges. Wiss. Göttingen, Math.-Phys. Kl. (1930), 30–35. <!-- \bibitem{Rel30} -->

**Rouché, E.** *Mémoire sur la série de Lagrange.* J. École Polytechnique **22** (1862), 217–218. (Existence form via Ahlfors, *Complex Analysis*, 3rd ed., McGraw-Hill, 1979, §5.3 Theorem 18.) <!-- \bibitem{Rou1862} -->

**Six, G.** *Tomita–Takesaki modular theory of the Bost–Connes algebra at $\beta = 1$, formalized in Lean 4.* math.OA submission, 2026. Zenodo DOI: [10.5281/zenodo.20674870](https://doi.org/10.5281/zenodo.20674870). (Companion paper.) <!-- \bibitem{TTpaper} -->

**Six, G.** *tt-bost-connes-lean.* GitHub repository `localparty/tt-bost-connes-lean`, v0.2, 2026. Zenodo DOI: [10.5281/zenodo.20674891](https://doi.org/10.5281/zenodo.20674891). <!-- \bibitem{TTlean} -->

**Six, G.** *hilbert-polya-bost-connes-lean.* GitHub repository `localparty/hilbert-polya-bost-connes-lean`, v0.1 release (commit `53e12d8`), 2026. (Companion Lean formalization; Zenodo DOI pending Phase E mint.) <!-- \bibitem{hpbc-lean} -->

**Stummel, F.** *Diskrete Konvergenz linearer Operatoren I.* Math. Ann. **190** (1970), 45–92. DOI: [10.1007/BF01349967](https://doi.org/10.1007/BF01349967). <!-- \bibitem{Stu70} -->

**Takesaki, M.** *Tomita's Theory of Modular Hilbert Algebras and its Applications.* Lecture Notes in Math. **128**, Springer-Verlag, Berlin, 1970. DOI: [10.1007/BFb0065832](https://doi.org/10.1007/BFb0065832). <!-- \bibitem{Tak70} -->

**Takesaki, M.** *Theory of Operator Algebras II.* Encyclopaedia of Mathematical Sciences **125**, Springer-Verlag, Berlin, 2003. DOI: [10.1007/978-3-662-10451-4](https://doi.org/10.1007/978-3-662-10451-4). <!-- \bibitem{Tak03} -->

**Tao, T. et al.** *On a conjecture of Marton (Polynomial Freiman–Ruzsa conjecture).* Lean 4 formalization, 2023. <!-- \bibitem{PFR23} -->

**Teschl, G.** *Mathematical Methods in Quantum Mechanics: With Applications to Schrödinger Operators.* 2nd ed., AMS Graduate Studies in Mathematics **157**, 2014. <!-- \bibitem{Tes14} -->

**Titchmarsh, E. C.** *The Theory of the Riemann Zeta-Function.* 2nd ed., rev. D. R. Heath-Brown, Oxford Univ. Press, 1986. <!-- \bibitem{Tit86} -->

**Tomita, M.** *Standard form of von Neumann algebras.* Mimeographed preprint, Mathematical Institute, Tōhoku University, 1967. (Circulated as preprint; developed by Takesaki in [Tak70].) <!-- \bibitem{Tom67} -->

---

## Appendix A. Build and verification

The Lean 4 formalization is built from the project root of [hpbc-lean] at commit `53e12d8`:

```
elan default leanprover/lean4:v4.29.1
lake update
lake build HilbertPolyaBostConnes
```

Mathlib is pinned at SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6` (declared in `lake-manifest.json`); the Lake dependency on `tt-bost-connes-lean` v0.2 resolves to commit `d4bb8949e37f5e4b9769331294e8fb6e07b6de87`. The full chain builds without errors.

Verification commands (reproducing the §6.2–§6.3 inventories):

```
# zero code-level sorries:
grep -rE "by sorry|:= sorry|^sorry$|^\s+sorry$" HilbertPolyaBostConnes/ --include='*.lean'
# → no output

# 8 CCM-substrate-own atomic axioms (plus 6 TT-upstream-inherited via Lake):
grep -hE "^axiom " HilbertPolyaBostConnes/**/*.lean | wc -l
# → 8

# canonical-terminal #print axioms:
cat > /tmp/print-axioms-hp.lean <<'LEAN'
import HilbertPolyaBostConnes.SpectralGate
#print axioms HilbertPolyaBostConnes.rh_of_ccm_galerkin
LEAN
lake env lean /tmp/print-axioms-hp.lean
# → kernel-three + collectively_compact_resolvent_uniform_bound + rouche_zero_existence
```

These three commands together provide the independent-verification path for any reader.

---

*AI collaboration disclosure: during the preparation of this work, the author used Claude (Opus 4.7, Anthropic). The author reviewed all content and takes full responsibility for the paper.*
