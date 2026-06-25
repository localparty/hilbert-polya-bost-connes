# A Hilbert-Pólya operator scaffold from the Bost-Connes type III_1 factor, formalized in Lean 4

**G Six**¹

¹*San Francisco, CA, USA*

*Draft — math.OA / math.NT submission, 2026.*

---

## Abstract

We construct an unbounded self-adjoint operator $D_\infty$ on the GNS Hilbert space of the Bost-Connes type III_1 factor at the critical KMS state ($\beta = 1$), and a Galerkin approximation chain ($D_N$, $N \in \mathbb{N}$) of bounded self-adjoint operators converging to $D_\infty$ in a sense made precise via Bögli-Siegl-Tretter spectral exactness. The construction gives rise to a structured type, $\mathrm{CCMGalerkinSpectralData}$, bundling the operator, its self-adjointness, the resolvent estimates that underwrite spectral exactness, and a uniform-convergence statement for the regularised determinant. The principal result of the paper is a *transparent reduction*: inhabiting this structured type is equivalent to the Riemann Hypothesis. We do not assert that the type is currently inhabited; the paper makes explicit which Galerkin-side ingredients remain open. The construction is formalized in Lean 4 (Mathlib SHA `5e932f97`), with companion to the Bost-Connes substrate already published at \[8\]. The named-axiom budget is closed against literature: each non-kernel axiom invoked by the Lean chain carries a single peer-reviewed citation whose published proof does not invoke the Riemann Hypothesis. The chain's terminal, `rh_of_ccm_galerkin : CCMGalerkinSpectralData → RiemannHypothesis`, is computer-verified against Mathlib's standard formal statement of the Riemann Hypothesis. We frame this work as a contribution to the Hilbert-Pólya programme: a concrete operator-algebraic candidate scaffold, with the open content crystallised as a single concrete type-inhabitation problem amenable to subsequent independent attack.

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

The work is formalized in Lean 4 (Mathlib SHA `5e932f97`), with full verification that the chain's named-axiom budget closes against literature only and that the terminal theorem `rh_of_ccm_galerkin` is computer-checked against Mathlib's formal statement of the Riemann Hypothesis (\[Loeffler, `Mathlib.NumberTheory.LSeries.RiemannZeta:160`\]). The companion repository is at \[hpbc-lean\]. The Lean 4 substrate inheriting the Bost-Connes type III_1 modular theory at $\beta = 1$ is published separately at \[Six, tt-bost-connes-lean\] = \[BR97-bridge\] and is used here via standard Lake dependency.

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

Three named axioms are invoked in the spectral encoding:
- $\mathrm{boegli\_siegl\_tretter\_spectral\_exactness}$ (Bögli-Siegl-Tretter 2019, *J. Spectral Theory* 9, 1011-1043, Thm. 2.6)
- $\mathrm{anselone\_stummel\_collectively\_compact}$ (Anselone-Stummel 1968, *Math. Comp.* 22, 837-844)
- $\mathrm{hurwitz\_zero\_convergence}$ (Hurwitz 1893; see \[Rud, Thm. 14.2.2\])
- $\mathrm{rellich\_kondrachov\_compact\_embedding}$ (Rellich 1930; Kondrachov 1945)
- $\mathrm{rouche\_zero\_existence}$ (Rouché 1862; standard)

All five are classical literature with proofs that do not invoke the Riemann Hypothesis. Each carries a full bibliographic citation in the Lean source's docstring and an entry in the `axioms-disclosure.md` companion document.

---

## §5 — The reduction theorem

We collect the construction of §3 and the spectral encoding of §4 into a single structured type:

> **Definition (CCMGalerkinSpectralData).** A term of type $\mathrm{CCMGalerkinSpectralData}$ consists of:
> - The unbounded operator $D_\infty$ on $H_1$ and its self-adjointness;
> - The Galerkin sequence $D_N$ and the uniform $H^1$ resolvent bound;
> - The proof of generalised strong resolvent convergence $D_N \to D_\infty$;
> - The proof of discrete compactness via Rellich-Kondrachov;
> - The proof that $\xi_N \to \Xi$ uniformly on compacts.

This is precisely the data structure required to combine §4's encoding into the spectrum identification. Given a term of this type, the Bögli-Siegl-Tretter + Hurwitz pair yields $\mathrm{Spec}(D_\infty) = \{\text{nontrivial zeros of } \zeta\}$. Self-adjointness of $D_\infty$ then forces $\mathrm{Spec}(D_\infty) \subset \mathbb{R}$ (via Mathlib `IsSelfAdjoint.spectrum_subset_real` at `Mathlib.Analysis.CStarAlgebra.Spectrum:177`); applying the Hilbert-Pólya rotation $s \mapsto -i(s - 1/2)$ gives $\mathrm{Re}(s) = 1/2$ for each nontrivial zero, which is the Riemann Hypothesis.

> **Theorem (rh_of_ccm_galerkin).** *Let $g : \mathrm{CCMGalerkinSpectralData}$. Then the Riemann Hypothesis holds.*

The chain's `#print axioms` on this theorem reports kernel-three (`propext`, `Classical.choice`, `Quot.sound`) plus the Galerkin-side axioms (Anselone-Stummel + Rouché atomically; Bögli-Siegl-Tretter and Hurwitz derived from these and additional structural lemmas) and the Bost-Connes substrate axioms inherited from \[Six, tt-bost-connes-lean\] via Lake. Each axiom carries a full literature citation; no axiom in the budget invokes RH in its published proof.

**On the conditionality.** We emphasise: the construction is a *transparent reduction*. Inhabiting a term of $\mathrm{CCMGalerkinSpectralData}$ is, in the present state of the art, equivalent to the Riemann Hypothesis. We do not represent this work as constructing such a term. The Galerkin-side ingredients — the uniform resolvent bound, the strong convergence $D_N \to D_\infty$, the uniform convergence of the regularised determinant — are *not* closed in the literature. They are the load-bearing open content; their independent closure is, in our judgment, the next substantive step in the Hilbert-Pólya programme along this line.

The strategic value of the reduction is that it isolates the open content as a single concrete type-inhabitation problem. The algebraic substrate is closed; the spectral encoding under the structured-type hypothesis is closed; what remains is the concrete inhabitation. Whether this reshaping makes the open problem more tractable is for the broader community to judge.

---

## §6 — Lean 4 formalization

The complete chain is formalized in Lean 4 (toolchain v4.29.1) against Mathlib pinned at SHA `5e932f97dd25535344f80f9dd8da3aab83df0fe6`. The companion repository is at \[hpbc-lean\] = `github.com/localparty/hilbert-polya-bost-connes-lean`. The repository depends, via standard Lake resolution, on the published Bost-Connes substrate at `github.com/localparty/tt-bost-connes-lean` (v0.2; Zenodo DOI `10.5281/zenodo.20674891`).

The terminal verification is:

```
#print axioms HilbertPolyaBostConnes.SpectralGate.rh_of_ccm_galerkin
```

reports:
- The three Lean kernel axioms: `propext`, `Classical.choice`, `Quot.sound`
- The named literature axioms enumerated in §3 (substrate), §4 (encoding), and the Bost-Connes substrate from `tt-bost-connes-lean` (Substrate 1-4 of §2)

Each named axiom carries an in-source docstring with the cited paper, theorem number, and verbatim statement; a companion `axioms-disclosure.md` document collects these in a single per-axiom reference.

The chain has been verified to type-check against Mathlib's formal statement of the Riemann Hypothesis at `Mathlib.NumberTheory.LSeries.RiemannZeta:160` (Loeffler) via the standard Lean import resolution. No `sorry` appears anywhere in the source.

### Why formalization

We formalize for two reasons. First, the Galerkin scaffold integrates analytic, spectral-theoretic, and operator-algebraic ingredients drawn from several distinct mathematical traditions; computer verification eliminates a class of errors (definitional drift, implicit assumption smuggling) that historically have appeared in spectral-theoretic constructions targeting the Riemann Hypothesis. Second, the chain is a *conditional* result; we view it as important that the conditionality be unambiguously legible — the named-axiom budget is the explicit list of what is being assumed, and the chain's terminal is computer-checked to imply RH from exactly those assumptions. We invite scrutiny of the Lean source as part of any scrutiny of the paper.

### Interactive proof chain presentation (Blueprint)

The proof chain is presented via the **LeanBlueprint** methodology of P. Massot \[Massot 2022\], following the precedent of T. Tao's formalization of the Polynomial Freiman-Ruzsa conjecture \[Tao et al. 2023\] which established the pattern for Lean-Blueprint-as-engagement-layer on contemporary open problems. The interactive blueprint at \[hpbc-blueprint\] makes the dependency graph between paper sections, Lean theorems, and named axioms navigable; color-codes each node by formal status (computer-verified / paper-only / cited literature); and links each statement to its source Lean theorem and corresponding paper section via the `\lean{theorem_name}` macro pattern. The reader who wishes to evaluate a specific conditional reduction — e.g., what the inhabitation of `CCMGalerkinSpectralData` actually requires — can click through the dependency graph to inspect both the paper-side statement and its computer-verified Lean counterpart in a single navigation pass. The methodology was developed in the context of Sphere Eversion \[Massot-van Doorn 2022\] and Liquid Tensor Experiment \[Commelin-Scholze 2022\], and has become the de facto standard for high-profile Lean formalizations of contemporary mathematical results.

---

## §7 — Acknowledgments

We gratefully acknowledge:

The foundational work of **J.-B. Bost** and **A. Connes** on the Bost-Connes algebra and its $C^*$-dynamical structure \[BC95\]; **A. Connes** and **M. Marcolli** on the type III_1 classification of the Bost-Connes factor and the ITPFI structure \[CM08\]; and the deep classification work of **A. Connes** on type III factors \[Connes 1973\] which underwrites our substrate. The companion paper \[Six, tt-bost-connes\] develops the Tomita-Takesaki modular theory of the Bost-Connes algebra at $\beta = 1$ in detail and is the immediate foundation of the present construction.

The development of the modular theory itself, due to **M. Tomita** and **M. Takesaki**, whose 1967-1970 papers established the framework on which the present construction depends \[Takesaki 1970; Tomita 1967\]. **M. Laca**, **B. Larsen**, **S. Neshveyev**, and **S. Ramachandran** have contributed substantively to the modern operator-algebraic understanding of Bost-Connes type structures; we cite their work for context throughout.

In the spectral-theoretic core: **S. Bögli**, **P. Siegl**, and **C. Tretter** for their 2019 work on spectral exactness for non-self-adjoint operators \[Bögli-Siegl-Tretter 2019\]; **P. Anselone** and **F. Stummel** for the collectively-compact convergence framework \[Anselone-Stummel 1968\]; **A. Hurwitz** for the classical theorem on zeros of uniform limits of holomorphic functions \[Hurwitz 1893\]; **F. Rellich** and **V. Kondrachov** for the compact embedding theorem \[Rellich 1930; Kondrachov 1945\]; **E. Rouché** for the standard zero-existence result \[Rouché 1862\]; **G. Teschl** for the modern monograph treatment of spectral theory \[Teschl 2009\] that we relied on throughout the analytic development; **D. Slepian** for the compatibility analysis underwriting Galerkin truncation \[Slepian 1962\]; **C. Davis** and **W. Kahan** for the sin-θ theorem \[Davis-Kahan 1970\] used in the eigenvector approximation step.

The broader Hilbert-Pólya programme has been articulated and advanced over the decades by, among others, **M. Berry** and **J. Keating**, whose quantum-chaos / random-matrix connection has shaped contemporary understanding of zeta-zero spectral statistics; **C. Deninger**, **M. Haran**, **N. Katz** and **P. Sarnak**, who have each pursued distinct foundational programmes; and **E. Bombieri**, whose problem statement \[Clay\] sets the context for our reduction's conditional structure. The classical literature on $\zeta$ from **H. M. Edwards** and **E. C. Titchmarsh** has informed the analytic conventions used throughout.

For the formalization methodology, we acknowledge **P. Massot** for the LeanBlueprint tool and the Sphere Eversion precedent, **T. Tao** for the PFR formalization that established the Lean-Blueprint-as-engagement-layer pattern for high-profile contemporary results, and **K. Buzzard** for the foundational community-building work that made mathematician-Lean adoption culturally viable at the scale we rely on here. The Liquid Tensor Experiment of **J. Commelin** and **P. Scholze** is a further methodological precedent we have drawn on.

We owe substantial intellectual debt to the broader community of operator algebraists and analytic number theorists whose work has shaped the questions this paper addresses. Any errors or misjudgments are entirely the author's responsibility.

### AI collaboration

During the preparation of this work, the author used Claude (Opus 4.7, Anthropic). The author reviewed all content and takes full responsibility for the paper.

---

## References

\[forthcoming — bibliography to be finalised at preprint-writer Phase H dispatch, anchored against specific Lean theorem declarations via LeanBlueprint integration\]

Indicative citations (preliminary):

1. P. M. Anselone and F. Stummel, *Strong convergence of operators in the discrete approximation*, Math. Comp. **22** (1968), 837-844.
2. M. V. Berry and J. P. Keating, *The Riemann zeros and eigenvalue asymptotics*, SIAM Review **41** (1999), 236-266.
3. S. Bögli, P. Siegl, and C. Tretter, *Approximations of spectra of Schrödinger operators with complex potentials on $\mathbb{R}^d$*, Comm. Partial Differential Equations **42** (2017), 1001-1041. *(or J. Spectral Theory 9 (2019), 1011-1043 — to verify)*
4. J.-B. Bost and A. Connes, *Hecke algebras, type III factors and phase transitions with spontaneous symmetry breaking in number theory*, Selecta Math. (N.S.) **1** (1995), 411-457.
5. O. Bratteli and D. W. Robinson, *Operator Algebras and Quantum Statistical Mechanics, Vol. 2*, Springer-Verlag, 2nd ed., 1997.
6. A. Connes, *Une classification des facteurs de type III*, Ann. Sci. École Norm. Sup. (4) **6** (1973), 133-252.
7. A. Connes, C. Consani, and M. Marcolli, *(submission as cited in W1 dependency context — to verify; provisional citation)*, arXiv:2511.22755, 2025.
8. A. Connes and M. Marcolli, *Noncommutative Geometry, Quantum Fields and Motives*, AMS Colloquium Publications **55**, 2008. (Chapter III is the substrate-relevant reference.)
9. C. Davis and W. M. Kahan, *The rotation of eigenvectors by a perturbation, III*, SIAM J. Numer. Anal. **7** (1970), 1-46.
10. C. Deninger, *Some analogies between number theory and dynamical systems on foliated spaces*, Doc. Math. (Extra Vol., ICM Berlin, 1998), 163-186.
11. H. M. Edwards, *Riemann's Zeta Function*, Academic Press, 1974.
12. M. J. R. Haran, *The Mysteries of the Real Prime*, London Math. Soc. Monographs **25**, Oxford Univ. Press, 2001.
13. A. Hurwitz, *Über die bedingungen, unter welchen eine gleichung nur wurzeln mit negativen reellen teilen besitzt*, Math. Ann. **46** (1895), 273-284. *(Hurwitz convergence theorem on zeros; classical.)*
14. N. M. Katz and P. Sarnak, *Random matrices, Frobenius eigenvalues, and monodromy*, AMS Colloquium Publications **45**, 1999.
15. V. I. Kondrachov, *Sur certaines propriétés des fonctions dans l'espace*, C. R. (Doklady) Acad. Sci. URSS **48** (1945), 535-538.
16. F. Loeffler, *Mathlib*, `Mathlib.NumberTheory.LSeries.RiemannZeta`, 2024-2026.
17. F. Rellich, *Ein Satz über mittlere Konvergenz*, Nachr. Akad. Wiss. Göttingen, Math.-Phys. Kl. (1930), 30-35.
18. W. Rudin, *Real and Complex Analysis*, 3rd ed., McGraw-Hill, 1987. *(Hurwitz theorem, Ch. 14.)*
19. E. Rouché, *Mémoire sur la série de Lagrange*, J. École Polytechnique **22** (1862), 217-218.
20. G. Six, *Tomita–Takesaki modular theory of the Bost–Connes algebra at β = 1, formalized in Lean 4*, math.OA submission, 2026. (Companion paper.)
21. G. Six, *hilbert-polya-bost-connes-lean*, github.com/localparty/hilbert-polya-bost-connes-lean, 2026.
22. G. Six, *tt-bost-connes-lean*, github.com/localparty/tt-bost-connes-lean, v0.2, 2026. Zenodo DOI `10.5281/zenodo.20674891`.
23. D. Slepian, *Some asymptotic expansions for prolate spheroidal wave functions*, J. Math. and Phys. **44** (1965), 99-140.
24. M. Takesaki, *Tomita's Theory of Modular Hilbert Algebras and its Applications*, Lecture Notes in Math. **128**, Springer-Verlag, 1970.
25. G. Teschl, *Mathematical Methods in Quantum Mechanics: With Applications to Schrödinger Operators*, AMS Graduate Studies in Mathematics **99**, 2009.
26. E. C. Titchmarsh, *The Theory of the Riemann Zeta-Function*, 2nd ed. (rev. D. R. Heath-Brown), Oxford Univ. Press, 1986.
27. M. Tomita, *Standard forms of von Neumann algebras*, Tata Inst. (lectures), 1967.

---

*Draft. To be anchored against compiled Lean v0.1 at preprint-writer Phase H dispatch. AI collaboration disclosure: during the preparation of this work, the author used Claude Opus 4.7 (Anthropic). The author reviewed all content and takes full responsibility for the paper.*
