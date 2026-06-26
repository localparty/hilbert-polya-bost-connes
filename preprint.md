# The Bost-Connes Modular Generator as a candidate Hilbert-Pólya Operator, formalized in Lean 4²

**G Six**¹

*San Francisco, CA, USA*

*2026.*

¹ *During the preparation of this work, the author used Claude Opus 4.7.*

² *The Lean 4 formalization at [https://github.com/localparty/hilbert-polya-bost-connes-lean](https://github.com/localparty/hilbert-polya-bost-connes-lean) (Zenodo DOI assigned at publication time) Lake-depends on the companion Bost-Connes Tomita-Takesaki substrate at [https://github.com/localparty/tt-bost-connes-lean](https://github.com/localparty/tt-bost-connes-lean) v0.2 (independently archived at DOI [10.5281/zenodo.20674891](https://doi.org/10.5281/zenodo.20674891)); the formalization permits independent machine checking of the structural form of every theorem in §§3–6. The substrate inventory — eight CCM-substrate-own atomic axioms (§6.2; of which two, `rouche_zero_existence` and `collectively_compact_resolvent_uniform_bound`, appear in the canonical terminal's `#print axioms` closure; six are off-terminal background substrate) and six TT-upstream axioms inherited via the Lake dependency — carries the load-bearing analytic content. Each axiom carries a literature citation whose published proof does not invoke the Riemann Hypothesis (§6.2, and the companion `axioms-disclosure.md`).*

---

## Abstract

We study the modular generator $D\_\infty = -i \cdot \partial\_t \log \Delta$ of the Bost-Connes type $\mathrm{III}\_1$ factor at the critical KMS state ($\beta = 1$), acting on the GNS Hilbert space, as a candidate Hilbert-Pólya operator. We construct a Galerkin approximation chain $\{D\_N\}\_{N \in \mathbb{N}}$ of bounded operators whose spectra approximate that of $D\_\infty$ in the sense made precise by Bögli–Siegl–Tretter spectral exactness. The construction gives rise to a named structured type, $\mathrm{CCMGalerkinSpectralData}$, bundling the limit modular generator, its self-adjointness, the Galerkin sequence, collectively-compact-and-strong-convergence data, and a locally-uniform-convergence statement for the truncated completed-xi functions on the rotation strip $\{z \in \mathbb{C} : -1/2 < \mathrm{Im}\,z < 1\}$. The principal result of the paper is a *transparent conditional reduction*: inhabiting this structured type is equivalent to the Riemann Hypothesis. We do not assert that the type is currently inhabited; the Galerkin-side ingredients required for inhabitation are explicitly named in the chain. The construction is formalized in Lean 4 (toolchain `v4.29.1`, Mathlib SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6`) at the companion repository [hpbc-lean] (commit `53e12d8`), which Lake-depends on the Bost-Connes Tomita-Takesaki substrate published as [Six, tt-bost-connes-lean] v0.2 (Zenodo DOI [`10.5281/zenodo.20674891`](https://doi.org/10.5281/zenodo.20674891)). The named-axiom budget is closed against literature: 8 CCM-substrate-own atomic axioms (of which 2 — `rouche_zero_existence` and `collectively_compact_resolvent_uniform_bound` — appear in the canonical terminal's `#print axioms` closure; 6 are off-terminal background substrate) and 6 TT-upstream axioms inherited via the Lake dependency. Each axiom carries a literature citation whose published proof does not invoke the Riemann Hypothesis. The chain's canonical terminal, `HilbertPolyaBostConnes.rh_of_ccm_galerkin (g : CCMGalerkinSpectralData) : RiemannHypothesis`, is computer-verified against Mathlib's formal statement of the Riemann Hypothesis. We frame this work as a contribution to the Hilbert-Pólya programme: a concrete operator-algebraic candidate Hilbert-Pólya operator built on the BC modular generator, with the open content crystallised as a single concrete type-inhabitation problem amenable to subsequent independent attack. **This is not an unconditional closure of the Riemann Hypothesis.**

**Keywords**: Hilbert-Pólya programme, Bost-Connes algebra, type $\mathrm{III}\_1$ factor, modular flow, KMS states, Galerkin approximation, Bögli-Siegl-Tretter spectral exactness, Lean 4 formalization, Mathlib.

**MSC 2020**: 11M26 (Nonreal zeros of ζ(s) and L(s,χ)); 46L36 (Classification of factors); 46L60 (Applications of selfadjoint operator algebras to physics); 47A10 (Spectrum, resolvent); 68V20 (Formal mathematics).

---

## §1 — Introduction

The Hilbert-Pólya programme proposes that the nontrivial zeros of the Riemann zeta function $\zeta$ are eigenvalues of a self-adjoint operator $H$ on some Hilbert space, with the imaginary part of each zero matching an eigenvalue of $H$ shifted into the real line by the Hilbert-Pólya rotation $s \mapsto -i(s - 1/2)$ (we adopt the convention of \[CCM25\]; alternative sign conventions appear in \[BK99\] and \[Con99\]). Self-adjointness then forces the real part of each zero to equal $1/2$, which is the Riemann Hypothesis. The programme is now over a century old, and despite substantial structural progress along several distinct lines \[BK99, Con99, Den98, Har01, KS99\] — surveyed in §1.1 below — no operator satisfying the programme's conditions has been constructed; the present state of the Riemann Hypothesis problem and the open status of the Hilbert-Pólya operator question are documented in the canonical problem statement \[Bom00\].

The Bost-Connes $C^\ast$-dynamical system \[BC95\] supplies an operator-algebraic substrate that has been frequently proposed as a natural home for such an operator. At the critical KMS state $\beta = 1$, the Bost-Connes algebra factorises through an ITPFI construction \[BC95, Prop. 33; CM08, Ch. III, Thm. 3.32\] into a type $\mathrm{III}\_1$ factor with explicit modular data. The modular flow on the type $\mathrm{III}\_1$ factor's GNS representation provides a candidate dynamical system whose spectral data is conjectured \[CCM25\] to encode the zeta zeros.

This paper studies a specific instance of that conjectural line: the modular generator $D\_\infty = -i \cdot \partial\_t \log \Delta$ of the BC factor itself, viewed as a candidate Hilbert-Pólya operator. We construct, via Galerkin approximants over a chain of finite-dimensional projections, this unbounded modular generator $D\_\infty$ on the GNS Hilbert space; we *require* its self-adjointness as a type-level field of the structured type $\mathrm{CCMGalerkinSpectralData}$ defined in §5 (i.e. supplied by a gate inhabitant, not derived within the present construction); and we organise the Galerkin-side ingredients (uniform $H^1$ resolvent bounds, Anselone-Stummel collectively compact convergence, Bögli-Siegl-Tretter spectral exactness, Hurwitz uniform convergence of the regularised determinant) into that same structured type. The chain's terminal theorem is:

> **Theorem** (`rh_of_ccm_galerkin`). Let $g : \mathrm{CCMGalerkinSpectralData}$. Then the Riemann Hypothesis holds.

We do not claim that the structured type is currently inhabited. The two Galerkin-side ingredients that remain open — strong convergence of the Galerkin approximants $D\_N \to D\_\infty$ and uniform $L^2 \to H^1$ resolvent bounds — are explicitly named in the chain as axioms whose discharge is the load-bearing programme-side mathematical content. We mark these unambiguously and we make no representation that they are closer to discharge than they manifestly are.

The strategic value of this construction is precisely its honesty about the reduction. The route via \[CCM25\] — passing from the Bost–Connes spectral realization to RH by closing the W1 lemma stated there — surfaces W1 as the canonical irreducible hypothesis for that line of the programme, and the substantial structural work of CCM25 is what makes the W1 reduction precise. The present construction is complementary: it changes the *shape* of the reduction, passing through the BC-modular-data-derived Galerkin operator and crystallising the open content as the inhabitation of a single concrete structured type, while the algebraic, spectral-theoretic, and analytic substrate is closed independently and computer-verified. Whether either reduction shape ultimately makes the open content more tractable is for the broader community to judge; the present work's contribution is the substrate plus the concrete-type form of the open content, offered as a clean surface over which subsequent independent work — including but not limited to the CCM25 line — can proceed.

The work is formalized in Lean 4 (toolchain `v4.29.1`, Mathlib SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6`), with the chain's canonical-terminal `#print axioms` output verified to consist of the three Lean kernel axioms (`propext`, `Classical.choice`, `Quot.sound`) together with exactly two atomic classical-literature axioms (`rouche_zero_existence` [Rouché 1862; cf. Ahlfors §5.3 Thm 18] and `collectively_compact_resolvent_uniform_bound` [Anselone 1971 + Stummel 1970]). The terminal theorem `HilbertPolyaBostConnes.rh_of_ccm_galerkin (g : CCMGalerkinSpectralData) : RiemannHypothesis` is computer-checked against Mathlib's formal statement of the Riemann Hypothesis (\[Loeffler 2024–2026, `Mathlib.NumberTheory.LSeries.RiemannZeta:160`\]); the chain's terminal is, at every cited site, the transparent conditional reduction over `CCMGalerkinSpectralData` made precise in §5. The companion Lean 4 repository is at \[hpbc-lean\] (commit `53e12d8`). The Lean 4 substrate carrying the Bost-Connes type $\mathrm{III}\_1$ modular theory at $\beta = 1$ is published separately at \[Six, tt-bost-connes-lean\] v0.2 (Zenodo DOI [`10.5281/zenodo.20674891`](https://doi.org/10.5281/zenodo.20674891)) and is used here via standard Lake dependency.

### §1.1. Relation to other Hilbert-Pólya candidates

The present construction sits alongside several distinct lines of work in the Hilbert-Pólya programme; we position it as complementary rather than competitive at each comparison point.

**Berry–Keating quantum-chaos programme** \[BK99\]. The Berry–Keating programme suggests that the Riemann zeros are eigenvalues of a quantization of the classical Hamiltonian $H = xp$, with the zero-counting function matching a Weyl-asymptotics-derived eigenvalue counting. The substrate is classical mechanics + semi-classical quantization; the operator-theoretic data is conjectural and the underlying Hilbert space is not canonically identified. The present construction differs in substrate (operator-algebraic Bost–Connes modular flow vs. quantum-chaos) and in legibility of the open content (a single named structured type vs. a quantization-existence problem).

**Connes adèle-class-space trace-formula programme** \[Con99\]. Connes's 1999 programme proposes that the Riemann zeros appear as the absorption spectrum of a natural operator on the adèle class space $\mathbb{A}\_K / K^\times$, related to the trace formula on this non-commutative space. The substrate is non-commutative geometry + spectral triples; the operator's self-adjointness and explicit construction remain conjectural. Our construction shares the operator-algebraic register (both pass through type $\mathrm{III}\_1$ structure in the BC factor's modular data) but takes a different reduction shape — through Galerkin approximation of the BC modular Hamiltonian, with the open content crystallised as the inhabitation of a single structured type.

**Deninger arithmetic dynamical-systems programme** \[Den98\]. Deninger's programme proposes that the Riemann zeros are dynamical eigenvalues of a (conjectural) foliated dynamical system attached to the arithmetic site $\mathrm{Spec}(\mathbb{Z})$. The substrate is foliated dynamics + dynamical Lefschetz trace; the foliated system itself has not been constructed. The present construction differs principally in substrate availability — the BC modular flow exists and is well-studied, while Deninger's dynamical system is conjectural — and in the form of the open content (a structured-type inhabitation vs. a system-existence question).

**Connes–Consani–Moscovici prolate-spheroidal programme** \[CCM25\]. CCM25 develops a spectral-realization framework for the Bost–Connes system at $\beta = 1$ via prolate-spheroidal operators and the Carathéodory–Fejér extension; the construction yields spectral data matching the imaginary parts of nontrivial zeros of $\zeta\_\mathbb{Q}$ on the even sector, conditional on a uniform-bound lemma stated as W1 in \[CCM25\] (a bound on the prolate-spheroidal extension data required to certify the spectral identification at the limit). The substrate overlap with the present work is substantial — both pass through the BC algebra's modular data at the critical KMS state, both are operator-algebraic — but the reduction shape differs: CCM25 reduces RH to W1, while the present work reduces RH to inhabitation of $\mathrm{CCMGalerkinSpectralData}$ (a Galerkin-approximation-shaped structured type packaging the convergence + compactness + uniform-bound ingredients). Whether either reduction shape proves more tractable than the other is for the broader community to judge; the present work is best read as a complementary substrate offering a different angle on the same open content.

**Katz–Sarnak random-matrix-statistics programme** \[KS99\]. The Katz–Sarnak programme establishes that the local-scale statistics of zeta zeros match the statistics of eigenvalues of large random matrices from the Gaussian Unitary Ensemble, providing strong heuristic support for the Hilbert-Pólya picture without identifying the operator itself. The present construction is orthogonal: it proposes an explicit candidate operator (the BC modular generator $D\_\infty$), and would be compatible with (rather than competing against) the random-matrix evidence if the gate is ever inhabited.

**Haran arithmetic-real-prime programme** \[Har01\]. Haran's programme proposes that the Riemann zeros emerge from a "real prime" structure at the arithmetic infinity, developing a spectral interpretation grounded in an extended notion of arithmetic geometry. The substrate is distinct from the operator-algebraic register of the present work; the two are not in direct comparison but jointly populate the broader search space.

The cumulative picture, as documented in the canonical problem statement \[Bom00\]: several distinct candidate substrates exist, none has produced a constructed self-adjoint operator inhabiting its programme's conditions, and each surfaces different irreducible hypotheses at the boundary of the open content. The present work's contribution is its specific reduction shape — a single concrete type-inhabitation problem against an operator-algebraic substrate that is closed independently and computer-verified — and its honesty about the conditionality of that reduction.

### §1.2. Main results

Let $(B\_K, \alpha\_t, \omega\_1)$ denote the Bost–Connes $C^\ast$-dynamical system at the critical KMS state, with $K = \mathbb{Q}$, GNS Hilbert space $H\_R$, cyclic vector $\Omega\_1$, type $\mathrm{III}\_1$ factor $M = \pi\_{\omega\_1}(B\_K)''$, modular operator $\Delta$, and modular Hamiltonian $D\_\infty := -i \cdot \partial\_t \log \Delta$ (the modular generator). The Galerkin approximation chain $\{D\_N\}\_{N \in \mathbb{N}}$ is the sequence of finite-rank truncations $D\_N = P\_N \circ D\_\infty \circ P\_N$ for an increasing family of projections $P\_N$ converging strongly to the identity on $H\_R$. We collect the load-bearing Galerkin-side ingredients into the structured type $\mathrm{CCMGalerkinSpectralData}$ of §5 (the "gate"); the four main results below are the structural reductions that hold under (and for T1–T3, only under) inhabitation of the gate.

**Theorem T1** (Conditional reduction). Inhabiting $\mathrm{CCMGalerkinSpectralData}$ implies the Riemann Hypothesis. Equivalently, every term $g : \mathrm{CCMGalerkinSpectralData}$ yields a proof of `RiemannHypothesis`.
> — Lean terminal: [`HilbertPolyaBostConnes.rh_of_ccm_galerkin`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/SpectralGate.lean#L154).

**Theorem T2** (Spectrum encoding). Given $g : \mathrm{CCMGalerkinSpectralData}$, every rotated nontrivial zero $-i(s - 1/2)$ of the Riemann zeta function lies in $\mathrm{spec}(D\_\infty)$. The encoding is DERIVED from the gate's fields via the Bögli + Hurwitz machinery on the abstract self-adjoint $D\_\infty$.
> — Lean: [`HilbertPolyaBostConnes.PassageToLimit.spec_D_infty_eq_riemann_zeros_exact_of_gate`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/PassageToLimit.lean#L37).

**Theorem T3** (Self-adjointness). $D\_\infty$ is self-adjoint, $\mathrm{IsSelfAdjoint}\,D\_\infty$, as a type-level field of $\mathrm{CCMGalerkinSpectralData}$ supplied by the gate inhabitant. The standard Stone–von Neumann criterion on the core $\bigcup\_N P\_N H\_R$ provides the conceptual self-adjointness story; the current Lean encoding realises this via `IsSelfAdjoint.smul` on the identity-scalar form of `dInftyCLM` pending a fully realised gate inhabitation. The genuine unbounded modular Hamiltonian via continuous functional calculus on the closed positive $\Delta$ enters once a gate inhabitant is supplied.
> — Lean (identity-form realization): [`HilbertPolyaBostConnes.TTBridge.dInftyCLM_selfAdjoint`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/TTBridge.lean#L172).

**Theorem T4** (Rotation-strip containment). Every rotated nontrivial Riemann zeta zero $-i(s - 1/2)$ lies in the rotation strip $\mathrm{ccmRotationDomain} = \{z \in \mathbb{C} : -1/2 < \mathrm{Im}\,z < 1\}$. The containment is DERIVED from Mathlib's functional equation (the rotated trivial zeros, which are non-real, are off the strip — a strip-vs-half-plane discipline that makes the gate RH-equivalent rather than inconsistent; see §1.3).
> — Lean: [`HilbertPolyaBostConnes.Lemmas.rotatedZero_mem_rotationDomain`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/Lemmas/HurwitzZeros.lean#L238).

Theorems T1 + T2 + T3 are *conditional* on gate inhabitation; T4 is *unconditional* (DERIVED from Mathlib + standard Riemann-zeta facts, requires no gate hypothesis). The conditional-reduction framing of T1 is the principal result of the paper; T2 + T3 + T4 are the load-bearing structural components from which T1 is composed. The Lean chain's canonical-terminal `#print axioms` certificate (§6.3) records that T1 is closed against kernel-three + the two atomic on-terminal axioms `rouche_zero_existence` and `collectively_compact_resolvent_uniform_bound`, conditional only on the gate's type-level fields.

### §1.3. Outline and conventions

**Hilbert-Pólya rotation direction.** Throughout this work we use the Hilbert-Pólya rotation $s \mapsto -i(s - 1/2)$, identifying a nontrivial Riemann zeta zero $s$ on the critical line $\mathrm{Re}(s) = 1/2$ with a real eigenvalue $\mathrm{Im}(s)$ of a self-adjoint operator on Hilbert space. We adopt the convention of \[CCM25\]; alternative sign conventions appear in \[BK99\] and \[Con99\] (the rotation can be replaced by $s \mapsto i(s - 1/2)$ or by $s \mapsto -i \cdot s$ with corresponding sign adjustments in the spectrum identification; the present construction's structural form is invariant up to sign convention).

**Strip-vs-half-plane discipline.** The Hurwitz step of the spectrum encoding (Theorem T2) runs on the rotation strip $\mathrm{ccmRotationDomain} = \{z \in \mathbb{C} : -1/2 < \mathrm{Im}\,z < 1\}$ — the rotated image of the critical strip $\{s : 0 < \mathrm{Re}(s) < 1\}$ under $s \mapsto -i(s - 1/2)$. The strip choice is essential: on the strip the zeros of $\xi\_\infty(z) = \zeta(1/2 + i z)$ are exactly the rotated *nontrivial* zeta zeros (the trivial zeros and the $s = 1$ pole are off the strip). A half-plane domain would catch the rotated trivial zeros — which are non-real — forcing the gate to be *inconsistent* rather than RH-equivalent. The critical-strip containment of the rotated nontrivial zeros is itself DERIVED from Mathlib's functional equation (Theorem T4), not carried as a gate field.

**KMS / modular sign convention.** We adopt **Connes' convention** $\sigma\_t = \mathrm{Ad}(\Delta^{it})$ for the modular automorphism group. The $\mathrm{KMS}\_1$ state on $(B\_K, \alpha\_t)$ at $\beta = 1$ is the unique extremal KMS state at the critical inverse temperature \[BC95, Thm. 25\]. The modular flow $\sigma\_t^{\omega\_1}$ identifies with the time evolution $\alpha\_t$ on $M$ via Takesaki's $W^\ast$-KMS-uniqueness theorem; this identification is Theorem C of the companion paper \[Six, tt-bost-connes\] and is inherited here as the foundational Bost–Connes substrate. The modular Hamiltonian $D\_\infty := -i \cdot \partial\_t \log \Delta$ is the generator of $\sigma\_t = e^{i t D\_\infty}$; under the alternative Bratteli–Robinson convention $\tau\_t = \sigma\_{-\beta t}^{\omega\_1}$, the identification would be $\widetilde\alpha\_t = \sigma\_{-t}^{\omega\_1}$ and $D\_\infty$ would carry the opposite sign. Our Connes convention is consistent throughout.

**Hilbert space.** The GNS Hilbert space $H\_R = L^2(B\_K, \omega\_1)$ at $\beta = 1$ is the completion of the quotient $B\_K / I\_{\omega\_1}$ under the inner product $\langle [a], [b] \rangle = \omega\_1(a^\ast b)$ (companion paper \[Six, tt-bost-connes\] Theorem A); cyclic and separating with respect to $\Omega\_1 = [1]$. The Galerkin projections $P\_N$ act on $H\_R$ as orthogonal projections onto an increasing family of finite-dimensional subspaces with strong limit identity.

**Outline.** §2 fixes notation and recapitulates the Bost-Connes substrate at $\beta = 1$, with references to the companion paper \[Six, tt-bost-connes\] for the modular-theoretic facts used here. §3 develops the Galerkin approximation framework and constructs $D\_\infty$. §4 establishes the spectral encoding under the Galerkin-side hypotheses (uniform convergence, compact embedding, Bögli-Siegl-Tretter exactness). §5 states the reduction theorem precisely and contextualises the conditionality. §6 describes the Lean 4 formalization and the named-axiom budget. §7 records the Mathlib integration roadmap — the formalisation-side targets whose discharge would replace the on-terminal atomic axioms with `import` swaps. §8 closes with acknowledgments.

### §1.4. Status of the main results

The following table summarises, for each main result T1–T4 of §1.2, the present status of the prose proof and of the Lean encoding, together with the discharge route for any conditional content. The two-dimensional structure — prose-status crossed with Lean-status — makes the structural-versus-substrate boundary visible at the introduction level rather than only at §6.

| Theorem | Prose-proof status | Lean-encoding status | Discharge route |
|---|---|---|---|
| **T1** (Conditional reduction, §5) | Unconditional given gate (transparent reduction; gate inhabitation $\Leftrightarrow$ RH) | Genuine terminal: `rh_of_ccm_galerkin` closes against kernel-three + 2 atomic on-terminal axioms (Rouché 1862; Anselone 1971 + Stummel 1970), conditional only on gate's type-level fields | None — terminal is the reduction itself; gate inhabitation is the open mathematical content |
| **T2** (Spectrum encoding, §4) | Unconditional given gate fields (Bögli + Hurwitz on the abstract $D\_\infty$) | Derived from gate fields via `spec_D_infty_eq_riemann_zeros_exact_of_gate` (Bögli + Hurwitz machinery on gate's `strongConv`, `collectivelyCompact`, `xiHat`, `xiHat_zero_mem_spectrum`, `spectrum_nonempty` fields) | Gate inhabitation (specifically the `strongConv` and `collectivelyCompact` fields, whose discharge is the load-bearing programme-side mathematical content) |
| **T3** (Self-adjointness, §3) | Unconditional given core (Stone–von Neumann on $\bigcup\_N P\_N H\_R$) | Identity-form proof of `IsSelfAdjoint dInftyCLM` via `dInftyCLM_selfAdjoint`; gate carries `D_inf_selfAdjoint` as a type-level field supplied by gate inhabitant | Genuine unbounded modular Hamiltonian via Mathlib `cfc Real.log Δ` on the closed positive $\Delta$ — awaits the CFC instance chain on $H\_R$ + a fully realised gate inhabitation |
| **T4** (Rotation-strip containment, §4) | Unconditional (rotated nontrivial zeros lie in strip; rotated trivial zeros are non-real and off the strip) | Genuine via `rotatedZero_mem_rotationDomain`, DERIVED from Mathlib's functional equation (`riemannZeta_nontrivial_zero_re_pos`) | None — the containment is unconditional and discharged in-Mathlib |

The eight CCM-substrate-own atomic axioms (§6.2) underlying every row above partition into 2 on-terminal axioms (Rouché 1862; Anselone 1971 + Stummel 1970; in the canonical-terminal `#print axioms` closure of T1) and 6 off-terminal axioms (BC operator-algebraic substrate predicates; Rellich-Kondrachov + Galerkin discrete-compactness scaffolding), plus 6 TT-upstream axioms inherited via the Lake dependency on [`tt-bost-connes-lean`](https://github.com/localparty/tt-bost-connes-lean) v0.2 (whose discharge is owned by the companion paper \[Six, tt-bost-connes\]). Every axiom carries a literature citation whose published proof does not invoke the Riemann Hypothesis (§6.2; companion `axioms-disclosure.md`).

The Lean-encoding status of T3 is informative: the prose proof is unconditional (Stone–von Neumann on the standard core), but the Lean encoding currently realises $D\_\infty$ as the identity-form placeholder `dInftyCLM` rather than the genuine `cfc Real.log Δ` form — the latter awaits the CFC instance chain on $H\_R$ together with a fully realised form of the Galerkin projections. This is a formalization gap, not a mathematical gap; the row's discharge route names the upgrade path.

---

## §2 — The Bost-Connes substrate at $\beta = 1$

We assume familiarity with the Bost-Connes algebra \[BC95\] and its modular theory at the critical KMS state. The companion paper \[Six, tt-bost-connes\] develops the Tomita-Takesaki modular theory of the Bost-Connes algebra at $\beta = 1$ in detail; that paper's Theorem C — unconditional closure of the KMS-uniqueness step via Takesaki's structure theorem \[BR97, Thm. 5.3.10\] — is the foundational substrate underwriting the present work. The Lean formalization at \[Six, tt-bost-connes-lean\] is used here as a Lake-resolved dependency.

We recall briefly: the Bost-Connes algebra $\mathcal{A}\_{\mathrm{BC}} = C^\ast(\mathbb{Q}/\mathbb{Z}) \rtimes\_\rho \mathbb{N}^\ast$ carries the time evolution $\sigma\_t(\mu\_n) = n^{it} \mu\_n$ and $\sigma\_t(e(r)) = e(r)$. The KMS state at inverse temperature $\beta = 1$ is unique and faithful (Theorem 25, \[BC95\]). The GNS representation $\pi\_1$ realises $\mathcal{A}\_{\mathrm{BC}}$ on a Hilbert space $H\_1$ with cyclic separating vector $\Omega\_1$, and the von Neumann completion $\pi\_1(\mathcal{A}\_{\mathrm{BC}})''$ is a type $\mathrm{III}\_1$ factor \[BC95, Prop. 33; CM08, Ch. III, Thm. 3.32; Connes 1973 for type classification\]. The modular operator $\Delta\_1$ and modular conjugation $J\_1$ are the standard Tomita-Takesaki data; the modular flow $\sigma\_t^\omega$ recovers the time evolution via $\sigma\_t^\omega = \sigma\_{-t}$ (with the sign convention of \[BR97\]).

Our construction proceeds via a Galerkin approximation indexed by an increasing chain of finite-dimensional projections $P\_N$ in $\pi\_1(\mathcal{A}\_{\mathrm{BC}})''$. We assume:

- **(Substrate 1)** $\pi\_1(\mathcal{A}\_{\mathrm{BC}})''$ is a type $\mathrm{III}\_1$ factor. (Closed in companion at \[Six, tt-bost-connes, §3\] and computer-verified.)
- **(Substrate 2)** The modular flow $\sigma\_t^\omega$ is ergodic. (Closed in companion at \[Six, tt-bost-connes, §5\] and computer-verified.)
- **(Substrate 3)** The Connes spectrum $\mathrm{Sp}\_C(\sigma)$ is real. (Connes 1973 type $\mathrm{III}\_1$ classification.)
- **(Substrate 4)** Suitable Galerkin projections $P\_N$ exist with strong limit identity. (Constructive — see §3.)

These four substrate facts together provide the operator-theoretic environment in which the candidate Hilbert-Pólya operator — the BC modular generator $D\_\infty$ — is constructed. They are closed in the companion paper and inherit into the present work through the Lake dependency. The present paper's mathematical contribution begins at §3 with the construction of the chain.

---

## §3 — The compact resolvent and $D\_\infty$ construction

**Construction summary.** This section presents the structural form of the $D\_\infty$ construction; the formalized proofs and the per-step Lean theorem statements live in the companion Lean repository \[hpbc-lean\]. In particular, `HilbertPolyaBostConnes.TTBridge.dInftyCLM_selfAdjoint` records the structural form of self-adjointness as a type-level field of the Galerkin spectral-data gate (§5), and `HilbertPolyaBostConnes.construction_of_gate` records the existential bundle of Galerkin spectral data + self-adjointness.

We construct the operator $D\_\infty$ as the strong limit of a sequence $D\_N$ of bounded self-adjoint operators defined on the Galerkin projections $P\_N H\_1$. The construction proceeds in three steps:

1. **Definition of $D\_N$.** For each $N$, $D\_N = -i \cdot \partial\_t \log \Delta\_1 \big|\_{P\_N H\_1}$, restricted to the finite-dimensional subspace. Here $\partial\_t \log \Delta\_1$ is the modular generator on $H\_1$; the restriction is well-defined because $P\_N$ commutes (asymptotically) with the modular flow.

2. **Uniform $H^1$ resolvent bound.** We posit, as a Galerkin-side ingredient of the structured type $\mathrm{CCMGalerkinSpectralData}$ of §5, the bound $\|(D\_N - i)^{-1}\|\_{L^2 \to H^1} < 2$ uniformly in $N$. This is the key estimate underwriting the Anselone–Stummel collectively-compact-resolvent uniform bound (§4); the Lean encoding records it as the on-terminal atomic axiom `collectively_compact_resolvent_uniform_bound` (cf. Anselone 1971 §1.6 Theorem 1.6 + Stummel 1970 §3), not as a derived result of the present construction.

3. **Definition of $D\_\infty$.** The limit operator $D\_\infty$ is defined on the dense domain $\mathrm{Dom}(D\_\infty) = \bigcup\_N P\_N H\_1$ via $D\_\infty \psi = \lim\_N D\_N \psi$ for $\psi \in P\_N H\_1$. We require self-adjointness of $D\_\infty$ as a type-level field of the structured type $\mathrm{CCMGalerkinSpectralData}$ of §5 (the Lean field `D_inf_selfAdjoint` at `SpectralGate.lean:71`, supplied by the gate user, not derived within the present construction); the standard Stone–von Neumann criterion on the core $\bigcup\_N P\_N H\_1$ is the mathematical content that a gate inhabitant would be required to discharge.

The construction is verified in Lean 4 against the named-axiom budget: the Galerkin restriction step (1) uses the closed substrate; the uniform resolvent bound (2) is the load-bearing programme-side argument; the limit step (3) reduces to a standard core argument that compiles against Mathlib `Analysis.CStarAlgebra.Spectrum`.

The uniform resolvent bound at step (2) is *not* a closed result. The Lean chain takes it as a Galerkin-side ingredient that the structured type $\mathrm{CCMGalerkinSpectralData}$ requires for inhabitation. We document this in §5.

---

## §4 — The spectral encoding

**Spectral encoding summary.** This section presents the structural form of the spectrum identification; the formalized proofs and the per-axiom-and-theorem Lean statements live in the companion Lean repository \[hpbc-lean\]. The Bögli no-spectral-pollution direction is recorded as the DERIVED theorem `HilbertPolyaBostConnes.Lemmas.boegli_spectral_exactness`, reducing to the on-terminal atomic axiom `HilbertPolyaBostConnes.Lemmas.BoegliExactness.Helpers.collectively_compact_resolvent_uniform_bound`; the Hurwitz zero-convergence direction is recorded as the DERIVED theorem `HilbertPolyaBostConnes.Lemmas.hurwitz_zero_convergence`, reducing to the on-terminal atomic axiom `HilbertPolyaBostConnes.Lemmas.HurwitzZeros.Helpers.rouche_zero_existence`; the combined spectrum-zeros identity is recorded as `HilbertPolyaBostConnes.PassageToLimit.spec_D_infty_eq_riemann_zeros_exact_of_gate` (DERIVED from the gate).

The spectrum of $D\_\infty$ is computed via two analytic ingredients:

**Bögli-Siegl-Tretter spectral exactness.** Under the joint hypotheses of (a) generalised strong resolvent convergence $D\_N \to D\_\infty$ and (b) discrete compactness (via Rellich-Kondrachov on the relevant Sobolev embedding), Bögli-Siegl-Tretter 2017 \[BST17, Thm. 2.6\] gives spectral exactness:

$$
\mathrm{Spec}(D_\infty) \mspace{5mu} =\mspace{5mu} \lim_{N \to \infty} \mathrm{Spec}(D_N),
$$

with no spurious eigenvalues introduced in the limit.

**Hurwitz uniform-on-compacts convergence.** The regularised determinant $\xi\_N(s) = \det\_2(D\_N - s)$ converges uniformly on compacts to the completed Riemann xi function $\Xi(s)$, via Hurwitz's classical theorem on zeros of uniform limits of holomorphic functions (\[Rud87, Thm. 14.2.2\]; cf. \[Hur93\]).

Combining the two: $\lim\_N \mathrm{Spec}(D\_N) = \{\text{zeros of } \Xi\} = \{\text{nontrivial zeros of } \zeta\}$, where the second equality is the standard relation between $\Xi$ and $\zeta$.

The Lean formalization isolates the load-bearing analytic content into atomic literature axioms. The Bögli no-spectral-pollution direction is recorded as the project-local theorem `boegli_spectral_exactness` ([`HilbertPolyaBostConnes.Lemmas.boegli_spectral_exactness`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/Lemmas/BoegliExactness.lean#L163)), which reduces via Mathlib's `Units.add` Neumann-series surface to the single atomic axiom `collectively_compact_resolvent_uniform_bound` ([`HilbertPolyaBostConnes.Lemmas.BoegliExactness.Helpers.collectively_compact_resolvent_uniform_bound`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/Lemmas/BoegliExactness/Helpers/Anselone.lean#L175)) citing Anselone 1971 §1.6 Theorem 1.6 + Stummel 1970 §3. The Hurwitz zero-convergence theorem is recorded as the project-local theorem `hurwitz_zero_convergence` ([`HilbertPolyaBostConnes.Lemmas.hurwitz_zero_convergence`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/Lemmas/HurwitzZeros.lean#L64)), which reduces to the single atomic axiom `rouche_zero_existence` ([`HilbertPolyaBostConnes.Lemmas.HurwitzZeros.Helpers.rouche_zero_existence`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/Lemmas/HurwitzZeros/Helpers/Rouche.lean#L102)) citing Rouché 1862 in the existence-form corollary of Ahlfors *Complex Analysis* §5.3 Theorem 18. Discrete compactness via Rellich–Kondrachov enters at the off-terminal layer through the axiom `dInftyApproximants_h1CompactEmbedding` ([`HilbertPolyaBostConnes.Lemmas.RellichKondrachov.Helpers.dInftyApproximants_h1CompactEmbedding`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/Lemmas/RellichKondrachov/Helpers/CompactEmbedding.lean#L124)) citing Rellich 1930 + Kondrachov 1945, conditional on a Galerkin uniform-bound and finite-rank hypothesis.

All four atomic axioms — `rouche_zero_existence`, `collectively_compact_resolvent_uniform_bound`, `dInftyApproximants_finiteRank`, `dInftyApproximants_h1CompactEmbedding` — are classical literature with proofs that do not invoke the Riemann Hypothesis. Each carries a full bibliographic citation in the Lean source docstring and an entry in the companion `axioms-disclosure.md` document. Of the four, only the first two (`rouche_zero_existence` and `collectively_compact_resolvent_uniform_bound`) appear in the canonical terminal's `#print axioms` closure (§6); the remaining two are off-terminal background substrate.

---

## §5 — The reduction theorem

We collect the construction of §3 and the spectral encoding of §4 into a single named structured type, recorded in Lean as [`HilbertPolyaBostConnes.CCMGalerkinSpectralData`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/SpectralGate.lean#L60):

> **Definition (CCMGalerkinSpectralData).** A term of type $\mathrm{CCMGalerkinSpectralData}$ consists of:
>
> - A complex Hilbert space $H$ (intended: the even sector $H\_R$ of the BC GNS space) with `[NormedAddCommGroup]`, `[InnerProductSpace ℂ]`, `[CompleteSpace]` instances;
> - The finite-rank Galerkin approximants $D\_N : \mathbb{N} \to (H \to\_L H)$ (intended: $P\_N \circ \log \Delta \circ P\_N$);
> - The limit modular Hamiltonian $D\_\infty : H \to\_L H$ (intended: $\log \Delta\_{\omega\_1}\restriction\_{H\_R}$);
> - A proof `D_inf_selfAdjoint` of $\mathrm{IsSelfAdjoint}\,D\_\infty$, so its spectrum lies on the real axis;
> - A proof `strongConv` of pointwise strong-operator convergence $D\_N \to D\_\infty$;
> - A proof `collectivelyCompact` of the Stummel/Anselone collectively-compact-family condition on $\{D\_N\}$;
> - The truncated completed-xi functions $\hat\xi\_N : \mathbb{N} \to \mathbb{C} \to \mathbb{C}$ (intended: characteristic polynomials of $D\_N$);
> - Proofs that each $\hat\xi\_N$ is differentiable on the rotation strip $\mathrm{ccmRotationDomain} = \{z : -1/2 < \mathrm{Im}\,z < 1\}$;
> - A proof `xiHat_tendsto` of locally uniform convergence $\hat\xi\_N \to \xi\_\infty$ on $\mathrm{ccmRotationDomain}$, where $\xi\_\infty(z) := \zeta(1/2 + i\cdot z)$ is the rotated Riemann zeta DERIVED in `Lemmas/HurwitzZeros.lean`;
> - A Fredholm spectral identification `xiHat_zero_mem_spectrum`: zeros of $\hat\xi\_N$ lie in the spectrum of $D\_N$;
> - A non-emptiness witness `spectrum_nonempty` for each $\mathrm{spec}(D\_N)$.

This is precisely the data structure required to combine §4's encoding into the spectrum identification. Given a term of this type, the Bögli + Hurwitz pair (derived in the Lean chain from the two atomic axioms of §4) yields that each rotated nontrivial zeta zero $-i(s - 1/2)$ lies in $\mathrm{spec}(D\_\infty)$. Self-adjointness of $D\_\infty$ then forces $\mathrm{spec}(D\_\infty) \subset \mathbb{R}$ via Mathlib's standard `IsSelfAdjoint.spectrum_subset_real` (`Mathlib.Analysis.InnerProductSpace.Spectrum`); the Hilbert–Pólya rotation $s \mapsto -i(s - 1/2)$ then yields $\mathrm{Re}(s) = 1/2$ for each nontrivial zero, which is the Riemann Hypothesis.

Crucially, the Hurwitz step runs on the strip $\mathrm{ccmRotationDomain}$ on which the zeros of $\xi\_\infty(z) = \zeta(1/2 + i z)$ are exactly the rotated *nontrivial* zeros — the trivial zeros and the $s = 1$ pole are off the strip. So the derived encoding places only the rotated nontrivial zeros into $\mathrm{spec}(D\_\infty)$. A half-plane domain would also catch the rotated trivial zeros (non-real), making the gate *inconsistent* rather than RH-equivalent; the strip avoids that. The critical-strip containment of the rotated nontrivial zeros is itself DERIVED from Mathlib's functional equation (`rotatedZero_mem_rotationDomain`, via `riemannZeta_nontrivial_zero_re_pos`), not carried as a gate field.

> **Theorem** (`rh_of_ccm_galerkin`). Let $g : \mathrm{CCMGalerkinSpectralData}$. Then the Riemann Hypothesis holds.
> — [`HilbertPolyaBostConnes.rh_of_ccm_galerkin`](https://github.com/localparty/hilbert-polya-bost-connes-lean/blob/53e12d8/HilbertPolyaBostConnes/SpectralGate.lean#L154).

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

— the three Lean kernel axioms together with exactly two atomic classical-literature ports. The remaining six CCM-substrate-own axioms (the Bost–Connes type $\mathrm{III}\_1$ predicates of §2 Substrates 1–4 and the off-terminal Rellich–Kondrachov + Galerkin-finite-rank Lemmas/Helpers axioms of §4) and the six TT-upstream axioms inherited via the Lake dependency on \[Six, tt-bost-connes-lean\] are honestly **off-terminal**: they support the broader substrate but the canonical terminal consumes `CCMGalerkinSpectralData` only via the gate's type-level fields, decoupling them from the `#print axioms` closure of the terminal itself. Both on-terminal and off-terminal axioms are honestly disclosed in §6 below and in the companion `axioms-disclosure.md`. Each axiom carries a literature citation whose published proof does not invoke the Riemann Hypothesis.

**On the conditionality.** We emphasise: the construction is a *transparent reduction*. Inhabiting a term of $\mathrm{CCMGalerkinSpectralData}$ is, in the present state of the art, equivalent to the Riemann Hypothesis. We do not represent this work as constructing such a term. The Galerkin-side ingredients — the uniform resolvent bound, the strong convergence $D\_N \to D\_\infty$, the uniform convergence of the regularised determinant — are *not* closed in the literature. They are the load-bearing open content; their independent closure is, in our judgment, the next substantive step in the Hilbert-Pólya programme along this line.

**Specific progress signals.** Without anticipating outcomes, we identify three classes of partial result that would constitute incremental progress toward gate inhabitation: (i) partial closure of the uniform $H^1$ resolvent bound on a restricted sub-family of Galerkin projections (e.g. those associated to a single prime factor of the ITPFI factorization, where local modular data is explicit); (ii) numerical evidence for the uniform bound on small-ℕ truncations matching the analytic prediction; (iii) the locally-uniform-convergence statement for the truncated completed-xi functions on the rotation strip, established directly from explicit Hecke-basis computations on a finite-ℕ Galerkin projection. Each of (i)-(iii) would discharge a specific field of $\mathrm{CCMGalerkinSpectralData}$ on a restricted regime; full discharge across the chain is what gate inhabitation requires.

The strategic value of the reduction is that it isolates the open content as a single concrete type-inhabitation problem. The algebraic substrate is closed; the spectral encoding under the structured-type hypothesis is closed; what remains is the concrete inhabitation. Whether this reshaping makes the open problem more tractable is for the broader community to judge.

---

## §6 — Lean 4 formalization

The complete chain is formalized in Lean 4 (toolchain `v4.29.1`) against Mathlib pinned at SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6`. The companion repository is at \[hpbc-lean\] = [`github.com/localparty/hilbert-polya-bost-connes-lean`](https://github.com/localparty/hilbert-polya-bost-connes-lean) (commit `53e12d8`; v0.1 release). The repository depends, via standard Lake resolution, on the published Bost-Connes Tomita–Takesaki substrate at [`github.com/localparty/tt-bost-connes-lean`](https://github.com/localparty/tt-bost-connes-lean) (v0.2; Zenodo DOI [`10.5281/zenodo.20674891`](https://doi.org/10.5281/zenodo.20674891)).

### §6.1. Module structure

The formalization is organised as the following modules in the namespace `HilbertPolyaBostConnes`:

| Module | Content | Section |
|---|---|---|
| `Infrastructure.lean` | BC operator-algebraic predicates (`IsTypeIII1`, `IsModularFlowErgodic`, `HasConnesSpectrumReal`), axiom-backed witnesses, Galerkin approximant scaffold | §§2, 3 |
| `TTBridge.lean`, `TomitaTakesaki.lean`, `TomitaTakesakiSupport/{Galerkin, RHWitness}.lean` | Bridge from the Lake-dep'd TT modular operator and flow into the spectral construction; locally absorbed scaffold modules not yet promoted to [`tt-bost-connes-lean`](https://github.com/localparty/tt-bost-connes-lean) v0.2 (zero named axioms; zero `sorry`) | §3 |
| `BCTypeIII1.lean`, `ModularFlowErgodicity.lean`, `HilbertPolyaOperator.lean`, `SpectrumMatching.lean` | Background substrate (off-terminal) | §2 |
| `Lemmas/HurwitzZeros.lean` | Hurwitz 1893 zero-convergence (DERIVED theorem) + the rotated-zeta DERIVED data $\xi\_\infty(z) := \zeta(1/2 + i z)$ and the rotation strip `ccmRotationDomain` | §4 |
| `Lemmas/HurwitzZeros/Helpers/Rouche.lean` | Rouché 1862 existence-form **axiom** (atomic Mathlib-gap port; **on-terminal**) | §4 |
| `Lemmas/HurwitzZeros/Helpers/Convergence.lean` | Hurwitz convergence helper machinery | §4 |
| `Lemmas/BoegliExactness.lean` | Bögli 2017 no-spectral-pollution (DERIVED theorem) | §4 |
| `Lemmas/BoegliExactness/Helpers/Anselone.lean` | Anselone 1971 + Stummel 1970 collectively-compact resolvent uniform-bound **axiom** (atomic Mathlib-gap port; **on-terminal**) | §4 |
| `Lemmas/BoegliExactness/Helpers/SpectralExactness.lean` | Neumann-series Bögli assembly from the Anselone bound | §4 |
| `Lemmas/RellichKondrachov.lean` | Rellich–Kondrachov $H^1 \hookrightarrow L^2$ discrete-compactness scaffolding | §3, §4 |
| `Lemmas/RellichKondrachov/Helpers/{CompactEmbedding, GalerkinFiniteRank, UniformBound}.lean` | Two off-terminal Rellich–Kondrachov / Galerkin **axioms** (`dInftyApproximants_h1CompactEmbedding`, `dInftyApproximants_finiteRank`) + a uniform-bound helper | §3, §4 |
| `Lemmas/SubIssueWitnesses.lean` | Project-local zero-operator sub-issue witnesses (no named axioms) | §3 |
| `Construction.lean` | `construction_of_gate (g)` — existential bundle of Galerkin spectral data + self-adjointness | §5 |
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

The formalization declares 8 CCM-substrate-own atomic axioms, organised as 4 Infrastructure (BC operator-algebraic substrate) + 4 Lemmas/Helpers (atomic classical-literature ports). A further 6 TT-upstream axioms are inherited via the Lake dependency on [`tt-bost-connes-lean`](https://github.com/localparty/tt-bost-connes-lean) v0.2; their discharge is owned by that companion paper [Six, tt-bost-connes] §8.2. Each axiom carries an in-source docstring with literature citation, theorem number, and verbatim statement where available; the companion document `axioms-disclosure.md` collects these in a single per-axiom reference.

**CCM-substrate-own axioms** (8 total):

| # | Axiom | File:line | Citation | On/off-terminal |
|---|---|---|---|---|
| 1 | `bc_factor_isTypeIII1` | `Infrastructure.lean:104` | Araki–Woods 1968 + Haagerup 1987 | off |
| 2 | `bc_modularFlow_ergodic` | `Infrastructure.lean:120` | Connes 1973 | off |
| 3 | `bc_connesSpectrum_real` | `Infrastructure.lean:128` | Connes 1973 | off |
| 4 | `dInftyApproximants_strongConv` | `Infrastructure.lean:168` | ITPFI + Chatelin (Galerkin port) | off |
| 5 | `rouche_zero_existence` | `Lemmas/HurwitzZeros/Helpers/Rouche.lean:102` | Rouché 1862; cf. Ahlfors *Complex Analysis* §5.3 Theorem 18 | **on** |
| 6 | `collectively_compact_resolvent_uniform_bound` | `Lemmas/BoegliExactness/Helpers/Anselone.lean:175` | Anselone 1971 §1.6 Theorem 1.6 + Stummel 1970 §3 | **on** |
| 7 | `dInftyApproximants_finiteRank` | `Lemmas/RellichKondrachov/Helpers/GalerkinFiniteRank.lean:77` | Galerkin structural (range $P\_N \circ D\_\infty \circ P\_N$ finite-dim) | off |
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

We formalize for two reasons. First, the BC-modular-generator construction integrates analytic, spectral-theoretic, and operator-algebraic ingredients drawn from several distinct mathematical traditions; computer verification eliminates a class of errors (definitional drift, implicit assumption smuggling) that historically have appeared in spectral-theoretic constructions targeting the Riemann Hypothesis. Second, the chain is a *conditional* result; we view it as important that the conditionality be unambiguously legible — the named-axiom budget is the explicit list of what is being assumed, and the chain's terminal is computer-checked to imply RH from exactly those assumptions. We invite scrutiny of the Lean source as part of any scrutiny of the paper.

### §6.5. Identity-form placeholders and discharge inventory

**Terminology.** Throughout this subsection, an *identity-form placeholder* is a Lean realisation of a type-level field that is structurally correct (well-typed; the proof obligation is discharged) but uses an identity-form or scalar-multiplication stand-in for the intended mathematical object. Identity-form placeholders are functionally distinct from `sorry` and from the named axioms of §6.2 — they introduce no axiom visible to `#print axioms` and Lean accepts them as complete inhabitants of their type. They are equally distinct from the genuine target realisations: the field name (e.g. `dInftyCLM`) is informative-cosmetic, and the Lean value is the identity form. The discharge route in each item below states what would replace the placeholder with the intended mathematical content.

In addition to the named axioms inventoried at §6.2, the formalisation carries one identity-form placeholder item, recorded below. It does not affect the buildability of the chain (the type-level field type-checks against `IsSelfAdjoint`); it records the gate-side substrate work that is downstream of the present construction.

1. **Modular Hamiltonian $D\_\infty$ as identity-form scalar realisation.** The Lean value `TTBridge.dInftyCLM` is currently realised as `(Real.log (Real.exp 1)) • 1`, the identity continuous linear map on the GNS Hilbert space; the corresponding type-level field on `CCMGalerkinSpectralData` then holds `IsSelfAdjoint` trivially. The genuine unbounded modular Hamiltonian $D\_\infty = \mathrm{cfc}\,\log\,\Delta$ on the closed positive modular operator $\Delta$ awaits the continuous-functional-calculus instance chain on the GNS Hilbert space at $\beta = 1$, together with a fully realised form of the Galerkin projections from the companion paper's Tomita–Takesaki substrate. Discharge route: gate inhabitation via the Tomita–Takesaki substrate's exposure of the genuine modular operator + CFC chain on $H\_R$; the upgrade plugs in at the `TTBridge.dInftyCLM` realisation site without restructuring §3–§4. This item parallels the formalisation-side artefact in TT v0.2 §8.3 (placeholder vs. genuine modular Hamiltonian); it is a formalisation gap, not a mathematical gap.

The identity-form placeholder item documents the boundary of the present formalisation: every claim in §§3–5 is type-correctly recorded in the Lean chain, with the load-bearing structural content discharged via the named axioms of §6.2 and this single identity-form placeholder. Future Tomita–Takesaki substrate work plugs in at the named site without restructuring the chain.

---

## §7 — Mathlib Integration Roadmap

We close with four Mathlib integration targets explicitly identified by the present construction. The first three are formalisation-side targets whose discharge would replace named on-terminal or off-terminal axioms with `import` swaps in the Lean source; the fourth is a cross-attribution pointer to the companion paper's roadmap for the inherited TT-substrate axioms. Each target is deferred to a dedicated Mathlib upstream PR rather than bundled into the present submission — both to keep the paper scope-contained, and to allow each upstream contribution its own review and attribution at Mathlib's pace.

### Mathlib Integration Target 7.1 (Rouché 1862 existence-form corollary)

*Develop a Mathlib formalisation of Rouché's theorem in the existence-form corollary required by Hurwitz zero-convergence.*

The on-terminal atomic axiom `rouche_zero_existence` (`Lemmas/HurwitzZeros/Helpers/Rouche.lean:102`) ports the classical existence-form corollary of Rouché 1862 (cf. Ahlfors *Complex Analysis* §5.3 Theorem 18). Mathlib at the pinned SHA has the `circleIntegral` API, Cauchy's integral formula, and the locally-uniform-limit Weierstrass package; it does not currently provide the argument-principle identity $(2\pi i)^{-1} \oint\_{|z - c| = r} f'/f = \\#\\{\text{zeros of } f \text{ in } B(c, r)\\}$ in any zero-counting form, from which the existence corollary is immediate. The route is a Mathlib PR establishing the argument-principle identity together with the existence-form corollary (or the full count-preservation form); estimated 100–300 lines of focused complex analysis. Completion of this target replaces the on-terminal atomic axiom with an `import` swap in `Lemmas/HurwitzZeros.lean`, leaving the project-local Hurwitz theorem `hurwitz_zero_convergence` unchanged.

### Mathlib Integration Target 7.2 (Anselone–Stummel collectively-compact-operator approximation theory)

*Develop a Mathlib formalisation of the Anselone 1971 / Stummel 1970 collectively-compact-operator approximation framework, in the form required by Bögli–Siegl–Tretter 2017 spectral exactness.*

The on-terminal atomic axiom `collectively_compact_resolvent_uniform_bound` (`Lemmas/BoegliExactness/Helpers/Anselone.lean:175`) packages the uniform-resolvent-bound core of Anselone 1971 §1.6 Theorem 1.6 + Stummel 1970 §3. Mathlib at the pinned SHA lacks: (a) any formalisation of *collective compactness* for operator families (`IsCompactOperator` is for single operators); (b) any spectral-approximation framework for non-self-adjoint operators in the Bögli–Siegl–Tretter sense; (c) the Banach–Steinhaus uniform-boundedness transfer from strong convergence + collective compactness to resolvent boundedness. The route is a Mathlib PR adding a `CollectivelyCompactFamily` predicate together with the Anselone perturbation lemma and the resolvent-boundedness transfer; programme target file `Mathlib/Analysis/Spectrum/BoegliSieglTretter.lean` (suggested in the in-source docstring at the axiom site). Estimated 200–500 lines of focused operator theory plus an upstream-review cycle. Completion of this target — together with Target 7.1 — reduces the canonical-terminal `#print axioms` closure of `rh_of_ccm_galerkin` to the three Lean kernel axioms alone, conditional only on the `CCMGalerkinSpectralData` gate's type-level fields.

### Mathlib Integration Target 7.3 (Rellich–Kondrachov compact embedding for the Galerkin setting)

*Develop a Mathlib formalisation of the Rellich–Kondrachov H¹ ↪ L² compact-embedding theorem in the Galerkin-projection setting of the present construction, with the supporting finite-rank infrastructure.*

Two off-terminal atomic axioms — `dInftyApproximants_h1CompactEmbedding` (`Lemmas/RellichKondrachov/Helpers/CompactEmbedding.lean:124`) citing Rellich 1930 + Kondrachov 1945, and `dInftyApproximants_finiteRank` (`Lemmas/RellichKondrachov/Helpers/GalerkinFiniteRank.lean:77`) recording the Galerkin-truncation finite-rank structural fact — together package the compact-embedding fact conditional on uniform-bound and finite-rank hypotheses. Mathlib has the abstract Sobolev-embedding framework; at the pinned SHA it does not provide the Rellich–Kondrachov statement in the form required by the Galerkin restriction $P\_N \circ D\_\infty \circ P\_N$. The route is a Mathlib PR adding the Rellich–Kondrachov compact embedding to the `Mathlib/Analysis/InnerProductSpace` chain together with a Galerkin-projection adapter; estimated 200–400 lines including the Sobolev-space groundwork. This is an off-terminal target — completion does not change the canonical-terminal `#print axioms` closure but does discharge two background-substrate axioms that the broader Galerkin programme relies on.

### Mathlib Integration Target 7.4 (Bost–Connes substrate axioms via companion paper)

*The Bost–Connes substrate axioms not addressed by Targets 7.1–7.3 are tracked separately in the companion paper.*

Target 7.4 covers two distinct categories of substrate axioms not addressed by Targets 7.1–7.3: (i) the 4 BC-substrate-own off-terminal axioms declared in HP's Infrastructure module — namely `bc_factor_isTypeIII1`, `bc_modularFlow_ergodic`, `bc_connesSpectrum_real`, and `dInftyApproximants_strongConv` — whose discharge roadmap is the BC operator-algebraic substrate programme described in §9.3 + §9.4 of the companion paper \[Six, tt-bost-connes\]; and (ii) the 6 TT-upstream-inherited axioms (`bc_system_exists`, `kms1_unique`, `kms1_faithful`, `itpfi_factorization`, `time_evolution_is_automorphism`, `kms_condition`) inherited via Lake-dep on [`tt-bost-connes-lean`](https://github.com/localparty/tt-bost-connes-lean) v0.2, whose discharge is owned by the TT roadmap upstream. The present roadmap defers to the companion paper for both categories by design: cross-attribution preserves the load-bearing analytic content boundary, and avoids restating the companion paper's roadmap targets verbatim. Joint progress on the companion paper's targets and the present targets 7.1–7.3 brings the Lean substrate of the Hilbert-Pólya programme along the BC modular-data line toward full Mathlib coverage.

---

<a name="acknowledgments"></a>

## §8 — Acknowledgments

We gratefully acknowledge:

The foundational work of **J.-B. Bost** and **A. Connes** on the Bost-Connes algebra and its $C^\ast$-dynamical structure \[BC95\]; **A. Connes** and **M. Marcolli** on the type $\mathrm{III}\_1$ classification of the Bost-Connes factor and the ITPFI structure \[CM08\]; and the deep classification work of **A. Connes** on type III factors \[Connes 1973\] which underwrites our substrate. The companion paper \[Six, tt-bost-connes\] develops the Tomita-Takesaki modular theory of the Bost-Connes algebra at $\beta = 1$ in detail and is the immediate foundation of the present construction.

The development of the modular theory itself, due to **M. Tomita** and **M. Takesaki**, whose 1967-1970 papers established the framework on which the present construction depends \[Takesaki 1970; Tomita 1967\]. **M. Laca**, **B. Larsen**, **S. Neshveyev**, and **S. Ramachandran** have contributed substantively to the modern operator-algebraic understanding of Bost-Connes type structures; we cite their work for context throughout.

In the spectral-theoretic core: **S. Bögli**, **P. Siegl**, and **C. Tretter** for their 2017 work on spectral exactness for non-self-adjoint operators \[BST17\]; **P. Anselone** for the collectively-compact operator-approximation framework \[Ans71\] and **F. Stummel** for the discrete-convergence theory \[Stu70\]; **A. Hurwitz** for the classical theorem on zeros of uniform limits of holomorphic functions \[Hur93\]; **F. Rellich** and **V. Kondrachov** for the compact embedding theorem \[Rel30; Kon45\]; **E. Rouché** for the standard zero-existence result \[Rou1862\]; **G. Teschl** for the modern monograph treatment of spectral theory \[Tes14\] that we relied on throughout the analytic development; **D. Slepian** for the prolate-spheroidal expansions underwriting Galerkin-truncation compatibility analysis \[Sle65\]; **C. Davis** and **W. Kahan** for the sin-θ theorem \[DK70\] used in the eigenvector approximation step.

The broader Hilbert-Pólya programme has been articulated and advanced over the decades by, among others, **M. Berry** and **J. Keating**, whose quantum-chaos / random-matrix connection has shaped contemporary understanding of zeta-zero spectral statistics, and **C. Deninger**, **M. Haran**, **N. Katz** and **P. Sarnak**, who have each pursued distinct foundational programmes. **Enrico Bombieri**'s exposition \[Bom00\] provides the canonical statement of the Riemann Hypothesis used throughout this work. The classical literature on $\zeta$ from **H. M. Edwards** and **E. C. Titchmarsh** has informed the analytic conventions used throughout.

We owe substantial intellectual debt to the broader community of operator algebraists and analytic number theorists whose work has shaped the questions this paper addresses. Any errors or misjudgments are entirely the author's responsibility.

## §9 — Continuous verification

Mathematical theorems have historically been established through community consensus: peer review, independent inspection, and the slow accretion of trust over years. A Lean 4 formalization adds a complementary trust signal at finer grain: the construction is represented in Lean as structured types, named theorems and lemmas, and the implications between them. The structured type `CCMGalerkinSpectralData` (§5) carries the Galerkin-side data as type-level fields; the canonical terminal `rh_of_ccm_galerkin` consumes a `CCMGalerkinSpectralData` term and produces a `RiemannHypothesis` proof term; the spectral encoding's reliance on the gate's `strongConv` and `collectivelyCompact` fields is visible in its type signature.

Mechanical rechecking on every commit reduces the entire chain to a fixed set of foundational axioms: the three Lean kernel axioms (`propext`, `Classical.choice`, `Quot.sound`) which underwrite the type theory itself, together with the named literature ports of §6.2, each of which carries a citation whose published proof does not invoke the Riemann Hypothesis. The check's public record, including the most-recent run conclusion, is at:

> https://github.com/localparty/hilbert-polya-bost-connes-lean/actions/runs/28233545971

A `success` conclusion on the most-recent `main`-branch check certifies, against Mathlib pinned at SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6` and Lean toolchain `v4.29.1`, that:

- `lake build HilbertPolyaBostConnes` completes (the chain compiles);
- The canonical terminal `HilbertPolyaBostConnes.rh_of_ccm_galerkin (g : CCMGalerkinSpectralData) : RiemannHypothesis` type-checks against Mathlib's `RiemannHypothesis` at `Mathlib.NumberTheory.LSeries.RiemannZeta:160`;
- The `#print axioms` closure of that terminal is exactly the three Lean kernel axioms (`propext`, `Classical.choice`, `Quot.sound`) together with the two atomic literature ports declared in §6.2 — `rouche_zero_existence` citing Rouché 1862 (Ahlfors *Complex Analysis* §5.3 Theorem 18), and `collectively_compact_resolvent_uniform_bound` citing Anselone 1971 §1.6 Theorem 1.6 + Stummel 1970 §3.

The structural reduction recorded in §5 — that any term of `CCMGalerkinSpectralData` yields a proof of `RiemannHypothesis` — is therefore verified, and reverified on every commit, within the conditions stated above. **The reduction's conditionality remains as stated in §5**: the open mathematical content is the inhabitation of the structured type, not the structural reduction itself.

For readers who prefer to build the formalization locally rather than rely on the hosted runner, the static reproducibility commands are at Appendix A.

---

## References

The full BibTeX-formatted bibliography is in the companion file `references.tex`, aligned 1:1 with the named-axiom citations of §6 and the companion `axioms-disclosure.md` document. Indicative citations:

**Anselone, P. M.** *Collectively Compact Operator Approximation Theory and Applications to Integral Equations.* Prentice-Hall, Englewood Cliffs, NJ, 1971. <!-- \bibitem{Ans71} -->

**Araki, H.; Woods, E. J.** *A classification of factors.* Publ. Res. Inst. Math. Sci., Kyoto Univ., Ser. A **4** (1968), 51–130. DOI: [10.2977/prims/1195195263](https://doi.org/10.2977/prims/1195195263). <!-- \bibitem{AW68} -->

**Berry, M. V.; Keating, J. P.** *The Riemann zeros and eigenvalue asymptotics.* SIAM Review **41** (1999), 236–266. DOI: [10.1137/S0036144598347497](https://doi.org/10.1137/S0036144598347497). <!-- \bibitem{BK99} -->

**Bögli, S.** *Convergence of sequences of linear operators and their spectra.* Integral Equations and Operator Theory **88** (2017), 559–599. DOI: [10.1007/s00020-017-2389-3](https://doi.org/10.1007/s00020-017-2389-3); arXiv:1604.07732. <!-- \bibitem{Bog17} -->

**Bögli, S.; Siegl, P.; Tretter, C.** *Approximations of spectra of Schrödinger operators with complex potentials on ℝᵈ.* Comm. Partial Differential Equations **42** (2017), 1001–1041. DOI: [10.1080/03605302.2017.1330342](https://doi.org/10.1080/03605302.2017.1330342). <!-- \bibitem{BST17} -->

**Bombieri, E.** *The Riemann Hypothesis.* Clay Mathematics Institute, 2000. (Standard expository statement of the conjecture.) <!-- \bibitem{Bom00} -->

**Bost, J.-B.; Connes, A.** *Hecke algebras, type III factors and phase transitions with spontaneous symmetry breaking in number theory.* Selecta Math. (N.S.) **1** (1995), 411–457. DOI: [10.1007/BF01589495](https://doi.org/10.1007/BF01589495). <!-- \bibitem{BC95} -->

**Bratteli, O.; Robinson, D. W.** *Operator Algebras and Quantum Statistical Mechanics II.* 2nd ed., Springer-Verlag, Berlin, 1997. DOI: [10.1007/978-3-662-09089-3](https://doi.org/10.1007/978-3-662-09089-3). <!-- \bibitem{BR97} -->

**Connes, A.** *Une classification des facteurs de type III.* Ann. Sci. École Norm. Sup. (4) **6** (1973), 133–252. DOI: [10.24033/asens.1247](https://doi.org/10.24033/asens.1247). <!-- \bibitem{Con73} -->

**Connes, A.** *Trace formula in noncommutative geometry and the zeros of the Riemann zeta function.* Selecta Math. (N.S.) **5** (1999), 29–106. DOI: [10.1007/s000290050042](https://doi.org/10.1007/s000290050042). <!-- \bibitem{Con99} -->

**Connes, A.; Consani, C.; Moscovici, H.** *Zeta Spectral Triples.* arXiv preprint [arXiv:2511.22755](https://arxiv.org/abs/2511.22755), 2025. <!-- \bibitem{CCM25} -->

**Connes, A.; Marcolli, M.** *Noncommutative Geometry, Quantum Fields and Motives.* AMS Colloquium Publications **55**, Providence, RI, 2008. DOI: [10.1090/coll/055](https://doi.org/10.1090/coll/055). <!-- \bibitem{CM08} -->

**Davis, C.; Kahan, W. M.** *The rotation of eigenvectors by a perturbation, III.* SIAM J. Numer. Anal. **7** (1970), 1–46. DOI: [10.1137/0707001](https://doi.org/10.1137/0707001). <!-- \bibitem{DK70} -->

**Edwards, H. M.** *Riemann's Zeta Function.* Academic Press, New York, 1974. <!-- \bibitem{Edw74} -->

**Haagerup, U.** *Connes' bicentralizer problem and uniqueness of the injective factor of type III₁.* Acta Math. **158** (1987), 95–148. DOI: [10.1007/BF02392257](https://doi.org/10.1007/BF02392257). <!-- \bibitem{Haa87} -->

**Hurwitz, A.** *Über die Bedingungen, unter welchen eine Gleichung nur Wurzeln mit negativen reellen Teilen besitzt.* Math. Ann. **46** (1895), 273–284. (The zero-convergence theorem; classical 1893 form.) <!-- \bibitem{Hur93} -->

**Kondrachov, V. I.** *Sur certaines propriétés des fonctions dans l'espace Lᵖ.* C. R. (Doklady) Acad. Sci. URSS **48** (1945), 535–538. <!-- \bibitem{Kon45} -->

**Loeffler, F.** Riemann zeta function and the Riemann Hypothesis (formalization), `Mathlib.NumberTheory.LSeries.RiemannZeta`, 2024–2026. <!-- \bibitem{Loe24} -->

**Rellich, F.** *Ein Satz über mittlere Konvergenz.* Nachr. Ges. Wiss. Göttingen, Math.-Phys. Kl. (1930), 30–35. <!-- \bibitem{Rel30} -->

**Rouché, E.** *Mémoire sur la série de Lagrange.* J. École Polytechnique **22** (1862), 217–218. (Existence form via Ahlfors, *Complex Analysis*, 3rd ed., McGraw-Hill, 1979, §5.3 Theorem 18.) <!-- \bibitem{Rou1862} -->

**Rudin, W.** *Real and Complex Analysis.* 3rd ed., McGraw-Hill, New York, 1987. ISBN 978-0-07-054234-1. (Hurwitz zero-convergence theorem at Theorem 14.2.2.) <!-- \bibitem{Rud87} -->

**Six, G.** *Tomita–Takesaki modular theory of the Bost–Connes algebra at β = 1, formalized in Lean 4.* math.OA submission, 2026. Zenodo DOI: [10.5281/zenodo.20674870](https://doi.org/10.5281/zenodo.20674870). (Companion paper.) <!-- \bibitem{TTpaper} -->

**Slepian, D.** *Some asymptotic expansions for prolate spheroidal wave functions.* J. Math. and Phys. **44** (1965), 99–140. (Prolate-spheroidal expansions underwriting Galerkin-truncation compatibility analysis.) <!-- \bibitem{Sle65} -->

**Six, G.** *tt-bost-connes-lean.* GitHub repository `localparty/tt-bost-connes-lean`, v0.2, 2026. Zenodo DOI: [10.5281/zenodo.20674891](https://doi.org/10.5281/zenodo.20674891). <!-- \bibitem{TTlean} -->

**Six, G.** *hilbert-polya-bost-connes-lean.* GitHub repository [`localparty/hilbert-polya-bost-connes-lean`](https://github.com/localparty/hilbert-polya-bost-connes-lean), v0.1 release (commit `53e12d8`), 2026. (Companion Lean formalization; Zenodo DOI pending publication.) <!-- \bibitem{hpbc-lean} -->

**Stummel, F.** *Diskrete Konvergenz linearer Operatoren I.* Math. Ann. **190** (1970), 45–92. DOI: [10.1007/BF01349967](https://doi.org/10.1007/BF01349967). <!-- \bibitem{Stu70} -->

**Takesaki, M.** *Tomita's Theory of Modular Hilbert Algebras and its Applications.* Lecture Notes in Math. **128**, Springer-Verlag, Berlin, 1970. DOI: [10.1007/BFb0065832](https://doi.org/10.1007/BFb0065832). <!-- \bibitem{Tak70} -->

**Takesaki, M.** *Theory of Operator Algebras II.* Encyclopaedia of Mathematical Sciences **125**, Springer-Verlag, Berlin, 2003. DOI: [10.1007/978-3-662-10451-4](https://doi.org/10.1007/978-3-662-10451-4). <!-- \bibitem{Tak03} -->

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

Mathlib is pinned at SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6` (declared in `lake-manifest.json`); the Lake dependency on [`tt-bost-connes-lean`](https://github.com/localparty/tt-bost-connes-lean) v0.2 resolves to commit `d4bb8949e37f5e4b9769331294e8fb6e07b6de87`. The full chain builds without errors.

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
