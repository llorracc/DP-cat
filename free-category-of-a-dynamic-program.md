---
marp: true
title: The free category of a dynamic program
theme: econ-ark-cat
paginate: true
math: katex
---

<!-- DRAFT 2026-10-07. A distillation of Akshay Shanker's DP-cat decks (Sessions A–C, commit ca94dff)
     for category theorists, prepared by Chris Carroll with Claude as drafting assistant.
     Every statement attributed to the decks is Shanker's. Passages flagged in a comment
     beginning CHECK are reformulations introduced here; verify them (ideally with Shanker)
     before use.
     Build from this folder (marp.config.js supplies the callout engine):
       marp --no-stdin free-category-of-a-dynamic-program.md --theme-set theme/econ-ark-cat.css \
            -o AI/riehl-distillation/build/deck.pdf --allow-local-files --html
     Citations "Riehl, X.Y.Z" are to Category Theory in Context, second edition (online). -->

<!-- _class: title -->

<p class="title-eyebrow">Dynamic programming &middot; category theory</p>

# The free category of a dynamic program

## Dynamic programming as functors out of a free category

<p class="title-authors">Mathematics: Akshay Shanker (DP-cat, 2026) &middot; distilled by Christopher Carroll<span class="title-date">October 2026</span></p>

---

<div class="kicker p1">Introduction &middot; the question</div>

## From first-order equations to a higher-order operator

Economists write a dynamic program as first-order equations between named quantities, $a=\sigma(m)$ and $m'=Ra+\xi'$. What a solver computes is higher order: the Bellman operator $\mathbb{T}$ on a space of value functions. A first-order syntax tree has no node for $\mathbb{T}$ and no record of the variables that $\max$ and $\mathbb{E}$ bind.

> [!callout] **Proposal (Shanker).**
> The operator is not a term. It is the image of an arrow of a free category under a functor. Syntax is a quiver over a signature; semantics is a functor out of its free category; the devices economists use informally, stages, post-decision states and Sargent–Stachurski's factored programs, are natural transformations, restriction along inclusions of ordinals, and an image factorization; and the Yoneda lemma says which operations on value functions are the natural ones.

Everything in *Category Theory in Context*, chapters 1–6, is assumed. The economics is defined once, on the next slide. Result numbers follow the source decks and their companion note.

<div class="footnote">Jacobs (1999) for classifying categories and the syntax-to-semantics step. Source: Shanker, <em>Categorical types and AGI</em>, Sessions A–C, akshayshanker.github.io/DP-cat.</div>

---

<div class="kicker p1">Introduction &middot; the economics, once</div>

## A sequential decision problem

- State spaces $X$ are measurable. A **stochastic kernel** $K\colon X\rightsquigarrow X'$ assigns to each $x$ a probability measure $K(x,-)$ on $X'$. **Value functions** are measurable $v\colon X\to\bar{\mathbb{R}}$, forming $L^{X}$. The **backward operator** of $K$ is $(K^{*}v)(x)\coloneqq\int K(x,dx')\,v(x')$, where the integral exists; $(K'K)^{*}=K^{*}K'^{*}$.
- A **policy** $\sigma$ selects a feasible action at each state. Its **policy operator** is $T_\sigma v=r_\sigma+\beta\,K_\sigma^{*}v$, with reward $r_\sigma$ and discount $\beta\in(0,1)$. The **Bellman operator** is $\mathbb{T}v=\sup_\sigma T_\sigma v$; the value function is its fixed point.

> [!definition] **Example A (buffer stock), used throughout.**
> Cash $m>0$; savings $a=\sigma(m)\in[0,m)$; income $\xi'\sim\nu$, independent across periods; next cash $m'=Ra+\xi'$; reward $u(m-\sigma(m))$. Then
> $$(\mathbb{T}v)(m)=\sup_{0<c\le m}\Big\{u(c)+\beta\,\mathbb{E}\,v\big(R(m-c)+\xi'\big)\Big\}.$$

<div class="footnote">Sargent and Stachurski (2026), §A.5.4: kernels; the backward operator is their Markov operator. Carroll and Shanker (2026) for Example A.</div>

---

<div class="kicker p1">1 Syntax &middot; signatures and declarations</div>

## A dynamic program is a quiver over a signature

> [!definition] **Definition (signature).**
> A quiver $\mathcal{S}$ whose vertices are **types** and edges **symbols** $\mathtt{s}\colon\mathtt{X}\to\mathtt{X}'$, with a set of **decision symbols**, each carrying a nonempty set $\Sigma_{\mathtt{s}}$ of **policies**. The quiver $\mathcal{S}_\Sigma$ has the same vertices, an edge $(\mathtt{s},\sigma)$ for each policy of each decision symbol and one edge $(\mathtt{s},\ast)$ for every other symbol; $p\colon\mathcal{S}_\Sigma\to\mathcal{S}$ forgets the policy.

> [!definition] **Definition (declaration).**
> A quiver $Q$ of **fields** with a quiver map $\tau\colon Q\to\mathcal{S}$, the **typing**: an object of $\mathsf{Quiver}/\mathcal{S}$. Its **policy quiver** is the pullback $Q_\Sigma\coloneqq Q\times_{\mathcal{S}}\mathcal{S}_\Sigma$: each decision edge $e$ becomes the parallel family $(e,\sigma)_{\sigma\in\Sigma_{\tau e}}$.

<!-- CHECK: the pullback description of Q_Σ is a reformulation; the decks define Q_Σ edge by edge. -->

**Example A.** Types $\mathtt{Xm},\mathtt{Xa}$; the decision symbol $\mathtt{save}\colon\mathtt{Xm}\to\mathtt{Xa}$ with the policies as $\Sigma_{\mathtt{save}}$, and $\mathtt{income}\colon\mathtt{Xa}\to\mathtt{Xm}$. Over $N$ periods, $Q$ is the chain $m_0\to a_0\to m_1\to\cdots\to a_{N-1}$, typed alternately.

<div class="center">

![w:520](assets/fs-period-chain.svg)

</div>

<div class="footnote">Goguen, Thatcher, Wagner and Wright (1977), §2: types are sorts, each rank of length one. A field resembles an atom of Backus's (1978) FP.</div>

---

<div class="kicker p1">1 Syntax &middot; models</div>

## Models are functors out of the free category

$F\dashv U\colon\mathsf{Cat}\rightleftarrows\mathsf{Quiver}$ (Riehl, Example 4.1.13). The **terms** of the signature are the arrows of $F\mathcal{S}_\Sigma$.

> [!definition] **Definition (algebra, model).**
> An **algebra** of the signature in $\mathsf{C}$ is a functor $F\mathcal{S}_\Sigma\to\mathsf{C}$. A **model** of a declaration in $\mathsf{C}$ is a functor $D\colon FQ_\Sigma\to\mathsf{C}$, equivalently a quiver map $Q_\Sigma\to U\mathsf{C}$: a carrier $D(j)$ for each field and an arrow $D(e,\sigma)$ for each edge, with no equations imposed. An algebra $A$ induces the model $A\circ F(\mathrm{pr}_2)$.

Models form the functor category $\mathsf{C}^{FQ_\Sigma}$, and a morphism of models is a natural transformation. (The decks' Proposition 1 is this adjunction; for large $\mathsf{C}$ read it as the explicit description of functors out of a free category.)

<!-- CHECK: "induced model = restriction along F(pr_2)" is a reformulation of the decks' "an algebra induces a model". -->

> [!callout-sm] **Remark.**
> In Lawvere's terms this is functorial semantics with no equations and no products: a model is a representation of the quiver $Q_\Sigma$ in $\mathsf{C}$. Arities, hence products, enter only with equations that read several fields (slide 21).

<div class="footnote">Riehl, Example 4.1.13: a functor out of a free category "defines a diagram in C with no commutativity requirements".</div>

---

<div class="kicker p2">2 Semantics &middot; the forward model</div>

## The forward model takes values in Markov kernels

$\mathsf{Stoch}\coloneqq\mathsf{Meas}_{\mathrm{Prob}}$, the Kleisli category of the Giry monad (Riehl, Example 5.2.11(iv)), a Markov category in Fritz's sense. Arrows $K\colon X\rightsquigarrow X'$ are kernels; $\delta_f$ is the deterministic kernel of a measurable map $f$.

> [!definition] **Definition (forward model).**
> $D_f\colon FQ_\Sigma\to\mathsf{Stoch}$ sends each field $j$ to a measurable **state space** $X_j$ and each edge $(e,\sigma)\colon j\to i$ to a kernel $X_j\rightsquigarrow X_i$, the law of the target field given the source field under that policy. In Example A the decision edge goes to the deterministic kernel $\delta_\sigma$ of the savings policy; in the decks' Example E the portfolio decision edge goes to a kernel that also integrates the returns.

<style scoped>table { font-size: 19px; }</style>

| | in general | Example A, one period $m\to a\to m'$ |
|---|---|---|
| **field** $j$ | its state space $X_j$ | $m,m'\mapsto M=(0,\infty)$; $\ a\mapsto A=[0,\infty)$ |
| **edge** $(e,\sigma)\colon j\to i$ | a kernel $X_j\rightsquigarrow X_i$ | $d_\sigma\mapsto\delta_\sigma$; $\ \ell\mapsto P$, $\ P(a,B)\coloneqq\nu\{\xi: Ra+\xi\in B\}$ |

**Laws.** $\mathrm{Prob}(X)=\mathsf{Stoch}(\ast,X)$, so the push-forward $\mu\mapsto\mu K$ of a law along a path is postcomposition: the represented functor $\mathsf{Stoch}(\ast,-)$ restricted along $D_f$.

<!-- CHECK: the representability remark is a reformulation; the decks write Δ(X) for laws and push forward by (μK)(B)=∫μ(dy)K(y,B). -->

<div class="footnote">Fritz (2020), §4; Riehl, Example 5.1.5(iv) for the Giry monad Prob on Meas.</div>

---

<div class="kicker p2">2 Semantics &middot; the backward model</div>

## The backward model is a presheaf of value functions

> [!definition] **Definition (backward model).**
> $D_b\colon(FQ_\Sigma)^{\mathrm{op}}\to\mathsf{Set}$ sends a field $j$ to $L^{X_j}$ and an edge $(e,\sigma)\colon j\to i$ to an operator $L^{X_i}\to L^{X_j}$ against the edge; in the time-separable case, with $K$ the edge's forward kernel, it is the affine map $v\mapsto r_{e,\sigma}+\beta_e\,K^{*}v$, with reward $r_{e,\sigma}=0$ on policy-free edges and discount $\beta_e>0$. Functoriality is $(K'K)^{*}=K^{*}K'^{*}$.

<div class="cols" style="grid-template-columns: 1fr 400px; gap: 1.2em; align-items: center;">
<div>

- The period $m_t\to a_t\to m_{t+1}$ of Example A goes to $D_b(d_\sigma)\,D_b(\ell)=T_\sigma$: **the policy operator is the image of a path.** Here $D_b(\ell)=P^{*}$ is the income expectation and $D_b(d_\sigma)\colon w\mapsto u(m-\sigma(m))+\beta\,w(\sigma(m))$.
- Top row: $Q_\Sigma$ for one policy. Middle: $D_f$, along the edges. Bottom: $D_b$, against them. No vertical arrows join the rows, so the diagram asserts no equation.

</div>
<div class="center">

![w:380](assets/fs-three-rows.svg)

</div>
</div>

<div class="footnote">The reward-free D<sub>b</sub> is Meas(−, ℝ̄) extended to Kleisli arrows by the barycentre, where the integral exists; with rewards it is affine in v. <!-- CHECK: this footnote is a reformulation. --> Riehl, Definition 1.3.5 (contravariant functors).</div>

---

<div class="kicker p2">2 Semantics &middot; forward and backward</div>

## The two models are not naturally isomorphic; they are paired

1. $D_f$ and $D_b$ differ in domain and in codomain, so no natural transformation between them is defined, let alone a natural isomorphism.
2. The pairing $\langle\mu,v\rangle\coloneqq\int v\,d\mu$ satisfies $\langle\mu K,v\rangle=\langle\mu,K^{*}v\rangle$ for every kernel $K$, by Fubini, where both sides are finite. Hence $\mu\mapsto\langle\mu,-\rangle$ is natural, $\mathsf{Stoch}(\ast,-)D_f\Rightarrow\mathsf{Set}\big(D_b(-),\bar{\mathbb{R}}\big)$, the analogue of evaluation into the double dual (Riehl, Example 1.4.4(i)); equivalently the pairings $\langle-,-\rangle_j$ on $\mathrm{Prob}(X_j)\times L^{X_j}$ form a **dinatural** transformation (Riehl, footnote 34 and Exercise 1.4.viii).
3. With rewards, $\langle\mu,D_b(e,\sigma)v\rangle=\langle\mu,r_{e,\sigma}\rangle+\beta_e\langle\mu K,v\rangle$ is affine in $v$ and is not a dinaturality condition.
4. For finite state spaces $K$ is a stochastic matrix and the identity reads $(\mu K)v=\mu(Kv)$.

<div class="footnote">Sargent and Stachurski (2026), Lemma A.5.33, for a kernel on one space.</div>

---

<div class="kicker p2">3 Stages &middot; chains and groupings</div>

## Groupings are inclusions of ordinals

A **chain** is a declaration whose quiver is $0\to1\to\cdots\to N$. With one policy fixed at each decision edge, its free category is the ordinal $[N]=\{0<\cdots<N\}$ (Riehl's $\mathbb{N}{+}\mathbb{1}$, Example 4.1.14). Example A over $N$ periods is a chain with $2N-1$ edges, positions $m_0,a_0,m_1,\ldots,a_{N-1}$.

> [!definition] **Definition (grouping, grouped model, stage structure).**
> A **grouping** $j_0<\cdots<j_n$ is a monomorphism $\pi\colon[n]\rightarrowtail[N]$, a composite of cofaces $d^{i}$. The **grouped model** of $D$ is the restriction $\pi^{*}D=D\pi$. A **stage structure** is a grouping with $\pi(0)=0$ and $\pi(n)=N$.

<!-- CHECK: the decks define a grouping as a sequence of positions and the grouped quiver Q′ explicitly; the ordinal-monomorphism phrasing is a reformulation. -->

Grouping changes the category on which the model is defined, so it is not a natural transformation, which compares two functors on one category. Natural transformations between grouped models appear on the next slides.

<div class="footnote">Riehl, Example 1.1.4(iv) (ordinal categories) and Example 4.1.14 (the ordinal freely generated by 0 → ⋯ → n, with its cofaces d<sup>i</sup>).</div>

---

<div class="kicker p2">3 Stages &middot; stages and stagings</div>

## A stage contains exactly one decision

A **run** is a path of policy-free edges, the empty path included. A **stage** is an edge of the grouped quiver whose path contains exactly one decision edge: from its **arrival field** through a run to the decision edge, then through a run to its **continuation field**. A **staging** is a stage structure all of whose edges are stages. A boundary may move across a run, never across a decision edge.

> [!lemma] **Lemma (number of stagings).**
> With $n_d\ge1$ decision edges and $n_k$ edges in the run between the $k$-th and $(k+1)$-st, a chain has $\prod_{k=1}^{n_d-1}(n_k+1)$ stagings, which is $1$ when $n_d=1$.

Under $D_b$ a stage goes to $T_\sigma=U\cdot D_b(e,\sigma)\cdot F$: the **continuation map** $F$ and the **arrival map** $U$ are the operators of the two runs and do not depend on $\sigma$. This is the **stage form** of $\{T_\sigma\}$; in the Bellman project it is the arrival–decision–continuation structure of a stage.

<div class="center">

![w:820](assets/fs-stage-sandwich.svg)

</div>

<div class="footnote">Example C of the decks, two decisions separated by a run of two edges, has three stagings.</div>

---

<div class="kicker p2">3 Stages &middot; factored programs</div>

## Sargent–Stachurski's factored programs are naturality squares

A **factored dynamic program** $(V,F,\hat V,\{G_\sigma\}_{\sigma\in\Sigma})$ defines $T_\sigma\coloneqq G_\sigma F$ and $\hat T_\sigma\coloneqq FG_\sigma$, so $FT_\sigma=\hat T_\sigma F$. For each $\sigma$, $n\mapsto T_\sigma^{\,n}$ and $n\mapsto\hat T_\sigma^{\,n}$ are functors $\mathsf{B}\mathbb{N}\to\mathsf{Set}$ and $F$ is a natural transformation between them: the square at $n$ follows from the square at $1$ by pasting (Riehl, Lemma 1.6.11).

> [!claim] **Proposition 5.**
> On a chain of periods oriented as the operators act, with edges $(t,\sigma)\colon t{+}1\to t$, $D(t)=V_t$ and $D(t,\sigma)=T_{t,\sigma}$, suppose $T_{t,\sigma}=G_{t,\sigma}F_{t+1}$ with $F_{t+1}$ independent of $\sigma$; put $F_0\coloneqq\mathrm{id}$. Then the $F_t$ are the components of a natural transformation $F\colon D\Rightarrow\hat D$ to the **reduced model** $\hat D(t)\coloneqq\hat V_t$, $\hat D(t,\sigma)\coloneqq F_tG_{t,\sigma}$, and for every policy sequence
> $$F_0\,T_{0,\sigma_0}\cdots T_{N-1,\sigma_{N-1}}=\hat D(0,\sigma_0)\cdots\hat D(N-1,\sigma_{N-1})\,F_N .$$

Since $F_0=\mathrm{id}$, the reduced model returns the same date-$0$ value function for every policy sequence. In Example A, $F$ is the income expectation and $\hat V$ consists of value functions of savings; Sargent and Stachurski call the two systems strongly semiconjugate under $F,G_\sigma$.

<div class="footnote">Sargent and Stachurski (2026), factored dynamic programs and subordinate programs.</div>

---

<div class="kicker p2">3 Stages &middot; moving a boundary</div>

## Moving a boundary is whiskering a 2-cell of posets

> [!claim] **Proposition 4.**
> Let $D$ be a model of a chain with one policy fixed at each decision edge, and $\pi,\pi'\colon[n]\rightarrowtail[N]$ groupings with $\pi(i)\le\pi'(i)$ for every $i$. The arrows $D(\pi i\to\pi' i)$ are the components of a natural transformation $\pi^{*}D\Rightarrow\pi'^{*}D$. If $\pi(i)>\pi'(i)$ for some $i$, the family is not defined.

*Proof.* $[N]$ is a poset, so $\pi\le\pi'$ is the unique natural transformation $\pi\Rightarrow\pi'$; whisker it with $D$ (Riehl, Remark 1.7.6). $\square$

<!-- CHECK: this one-line proof is a reformulation; the decks verify the naturality squares directly. -->

- For the contravariant $D_b$ the inequality is read in the reversed order: the components run from the grouping whose boundaries are later in $Q$ to the one whose boundaries are earlier.
- **Example A.** Moving every interior boundary from cash $m_{t+1}$ to savings $a_t$ gives $D_b(\text{cash staging})\Rightarrow D_b(\text{savings staging})$, with components the income expectations $D_b(\ell_t)$.

---

<div class="kicker p2">3 Stages &middot; transforming some fields</div>

## Transforming some fields: descent to a quotient

> [!claim] **Proposition 6 (partial transformations).**
> Let $D\colon FQ_\Sigma\to\mathsf{Set}$ be a model (for $D_b$, reverse every edge), $J$ a set of fields, and $\varphi_j\colon D(j)\twoheadrightarrow B_j$ onto for $j\in J$; put $B_j\coloneqq D(j)$ and $\varphi_j\coloneqq\mathrm{id}$ for $j\notin J$. There exist a model $D'$ with $D'(j)=B_j$ and a natural transformation $\varphi\colon D\Rightarrow D'$ with these components if and only if, for every edge $(e,\sigma)\colon j\to i$ with $j\in J$ and all $v,v'\in D(j)$,
> $$\varphi_j(v)=\varphi_j(v')\quad\text{implies}\quad\varphi_i\big(D(e,\sigma)\,v\big)=\varphi_i\big(D(e,\sigma)\,v'\big).$$
> Then $D'$ is unique, with $D'(e,\sigma)(\varphi_jv)=\varphi_i(D(e,\sigma)\,v)$. Such a family is **admissible**.

In other words, $\varphi_i\,D(e,\sigma)$ coequalizes the kernel pair of $\varphi_j$, so $D(e,\sigma)$ descends to the quotient $B_j$. The condition is local: one field and one edge at a time.

<!-- CHECK: the kernel-pair gloss is a reformulation. -->

<div class="footnote">The decks' Example B, a deterministic chain with an unreached point, illustrates the condition.</div>

---

<div class="kicker p2">3 Stages &middot; the coarsest cut</div>

## The coarsest cut is an image factorization

<div class="small">

For maps $T_\sigma\colon V\to W$, $\sigma\in\Sigma$, a **cut** is $(\hat V,F,G_\sigma)$ with $T_\sigma=G_\sigma F$ and $F$ independent of $\sigma$; it is **coarser** than $(\hat V',F',G'_\sigma)$ when $F=\omega F'$ for some $\omega\colon F'(V)\to\hat V$. Write $v\sim v'$ when $T_\sigma v=T_\sigma v'$ for every $\sigma$.

> [!claim] **Theorem 7 (the coarsest cut).**
> Let $\bar V\coloneqq V/{\sim}$, $q\colon V\twoheadrightarrow\bar V$ the quotient map and $\bar G_\sigma[v]\coloneqq T_\sigma v$. Then $(\bar V,q,\bar G_\sigma)$ is a cut, and for every cut $(\hat V,F,G_\sigma)$ there is exactly one $\omega_F\colon F(V)\to\bar V$ with $q=\omega_FF$; it is onto, and $\bar G_\sigma\omega_F=G_\sigma$ on $F(V)$. Hence a coarsest cut exists and is unique up to a unique bijection.

*Proof.* $(\bar V,q)$ is the image factorization of $\langle T_\sigma\rangle_{\sigma}\colon V\to W^{\Sigma}$ (Riehl, Example 3.6.11); a cut is a factorization of this map through $F$, and the image is the smallest quotient of $V$ through which it factors. $\square$

<!-- CHECK: this proof is a reformulation; the decks prove the theorem directly from the definitions. -->

> [!callout-sm] **Example A.**
> $v\sim v'$ iff $\mathbb{E}\,v(Ra+\xi')=\mathbb{E}\,v'(Ra+\xi')$ for every $a$: $q$ is the income expectation and $\bar V$ its image, the economists' value function of the **post-decision state**. When $\nu$ has a density, $q$ is not injective, so this is a genuine quotient and not a natural isomorphism; its use is exactly that it discards what no policy operator can distinguish.

</div>

---

<div class="kicker p2">3 Stages &middot; the canonical stage form</div>

## Every stage form factors through the canonical one

<div class="cols" style="grid-template-columns: 1fr 380px; gap: 1.2em; align-items: center;">
<div class="small">

1. Let $W_\Sigma\coloneqq\bigcup_\sigma T_\sigma(V)\subseteq W$, with inclusion $U_\Sigma$, and read $\bar G_\sigma$ as a map $\bar V\to W_\Sigma$. A **stage form** is a factorization $T_\sigma=UG_\sigma F$ with $F\colon V\to\hat V$ and $U\colon\tilde V\to W$ independent of $\sigma$. The top row $T_\sigma=U_\Sigma\bar G_\sigma q$ is one.
2. Two equations link every stage form to it through the single map $\omega_F$: $q=\omega_FF$ on $V$, by Theorem 7 for the cut $(\hat V,F,UG_\sigma)$; and $U_\Sigma\bar G_\sigma\omega_F=UG_\sigma$ on $F(V)$, since both sides send $Fv$ to $T_\sigma v$.
3. Dually, $U_\Sigma$ is the image of the copairing $[T_\sigma]_\sigma\colon\Sigma\times V\to W$: for every factorization $T_\sigma=U\tilde G_\sigma$ with $U$ monic and independent of $\sigma$ there is exactly one $\omega_U\colon W_\Sigma\to\tilde V$ with $U\omega_U=U_\Sigma$ (Proposition 8 of the companion note).
4. The family $\{T_\sigma\}$ determines neither the $F$ nor the $U$ of a declaration: $q$ is the coarsest $F$ and $U_\Sigma$ the finest $U$. Each factor of a stage is order-preserving for the pointwise order, so every policy operator of a stage is.

</div>
<div class="center">

![w:360](assets/fs-canonical.svg)

</div>
</div>

<!-- CHECK: item 3's copairing/image phrasing is a reformulation; the decks state Proposition 8 without proof. -->

---

<div class="kicker p2">3 Stages &middot; forward and backward again</div>

## From a backward factorization to a forward one

<div class="small">

1. **Forward to backward is immediate.** A factorization $K=K_2K_1$ of a kernel gives $K^{*}=K_1^{*}K_2^{*}$, and a grouping groups both models alike, as $\pi^{*}D_f$ and $(\pi^{\mathrm{op}})^{*}D_b$.
2. **Backward to forward is not.** In a cut $T_\sigma=G_\sigma F$ the map $F$ may be any map of sets, for instance $Fv\coloneqq v^{3}$ pointwise. On bounded functions, a map $L^{X'}\to L^{X}$ is $K^{*}$ for a kernel $K$ iff it is linear, positive, unital and continuous under bounded monotone limits; then $K(x,B)=(T\mathbf{1}_B)(x)$.
3. **For a stage,** on functions with finite integrals, $T_\sigma v-T_\sigma v'=\beta_\sigma K_\sigma^{*}(v-v')$, so $v\sim v'$ iff $K_\sigma^{*}v=K_\sigma^{*}v'$ for every $\sigma$, and $q$ is the backward operator of the kernel $J((\sigma,x),-)\coloneqq K_\sigma(x,-)$ on pairs (policy, arrival state), corestricted to its image. In Example A the reduced model is itself a backward model, on functions of savings.
4. **Isomorphisms.** A component is a bijection when it is the backward operator of an invertible deterministic map, as in the decks' perfect-foresight application; a bijective $K^{*}$ need not be deterministic, since the stochastic matrix with rows $(0.9,0.1)$ and $(0.1,0.9)$ is invertible.

</div>

<div class="footnote">Items 2 and 3 are stated in the decks without proof.</div>

---

<div class="kicker p3">4 Yoneda &middot; policies</div>

## Policies are sections; inserting one is precomposition

Let $E\coloneqq\{(s,b): s\in S,\ b\in\mathcal{D}(s)\}$ with $p\colon E\to S$, $p(s,b)\coloneqq s$. Every section of $p$ is $\iota_\sigma(s)=(s,\sigma(s))$ for a feasible policy $\sigma$: sections are exactly the feasible policies. In the reward-free backward model the decision operator is $\iota_\sigma^{*}\colon\mathsf{Set}(E,Z)\to\mathsf{Set}(S,Z)$, $w\mapsto w\iota_\sigma$.

> [!claim] **Proposition 10.**
> A family $\Phi_Z\colon\mathsf{Set}(E,Z)\to\mathsf{Set}(S,Z)$ natural in $Z$ is $\iota^{*}$ for exactly one $\iota\colon S\to E$, namely $\iota=\Phi_E(\mathrm{id}_E)$; and $\iota$ is a section of $p$ if and only if $\Phi_S(p)=\mathrm{id}_S$. (Riehl, Corollary 2.2.8 with $c=E$ and $c'=S$.)

Expectation is **not** natural in the value set: for $\lambda(z)\coloneqq z^{2}$, averaging $0$ and $2$ then squaring gives $1$; squaring then averaging gives $2$. It is natural for affine $\lambda$, since a probability integrates to one. So $T_\sigma$, which adds a reward and takes an expectation, is not of the form $\iota^{*}$.

---

<div class="kicker p3">4 Yoneda &middot; rules</div>

## Rules: the stage form is the form of every natural operation

For $\mathsf{C}$ locally small and objects $c_0,c_1,c_2,c_3$, a **rule** is a family of maps $\alpha_P\colon P(c_1,c_2)\to P(c_0,c_3)$, one for each bifunctor $P\colon\mathsf{C}^{\mathrm{op}}\times\mathsf{C}\to\mathsf{Set}$, natural in $P$: $\kappa_{(c_0,c_3)}\,\alpha_P=\alpha_{P'}\,\kappa_{(c_1,c_2)}$ for every $\kappa\colon P\Rightarrow P'$.

> [!claim] **Proposition 11 (companion note).**
> Every rule is $\alpha_P=P(F,U)$ for exactly one pair of arrows $F\colon c_0\to c_1$ and $U\colon c_2\to c_3$.

*Proof.* The Yoneda argument for the representable $(\mathsf{C}^{\mathrm{op}}\times\mathsf{C})((c_1,c_2),-)=\mathsf{C}(-,c_1)\times\mathsf{C}(c_2,-)$ and its element $(\mathrm{id}_{c_1},\mathrm{id}_{c_2})$: a rule is determined by $\alpha(\mathrm{id}_{c_1},\mathrm{id}_{c_2})=(F,U)$. The proof of Theorem 2.2.4 uses only that the transformations out of a representable form a set, which the lemma itself supplies, so $\mathsf{C}=\mathsf{Set}$ is admitted although $\mathsf{Set}^{\mathsf{C}^{\mathrm{op}}\times\mathsf{C}}$ is not locally small. $\square$

<!-- CHECK: the size remark is a reformulation of the decks' "the proof needs C only locally small". -->

On the hom bifunctor the rule sends $G\colon c_1\to c_2$ to $UGF$. So a factorization $T_\sigma=UG_\sigma F$ with the same $F$ and $U$ for every $\sigma$, the stage form, is the only way of varying with the policy that is natural in this sense.

---

<div class="kicker p3">4 Yoneda &middot; scope</div>

## What the lemma does, and does not, do here

<div class="small">

**It does.**
- Propositions 10 and 11 characterize the operations inside a stage that are natural in the value set and in the bifunctor: precomposition with a section, and conjugation by fixed maps.
- The initiality of $(\mathbb{N},s,0)$ (Riehl, Example 2.1.1) and of $(FQ,\eta)$ (Proposition 1) are initial-object statements; Proposition 2.4.8 adds that $0$ is a universal element. For $(FQ,\eta)$ the lemma is not applied: interpretations in $\mathsf{Set}$ or $\mathsf{Stoch}$ live in a category of categories that is not locally small.

**It does not.**
- Choose a factorization: $F=U=\mathrm{id}$ with $G_\sigma=T_\sigma$ is a stage form.
- Produce the quotient $q$, which comes from images, not from the lemma; by Proposition 6, $q$ is the coarsest admissible component at one field.
- Make precise the resemblance between knowing a declaration through its models and knowing an object by the arrows out of it (Riehl, Proposition 2.3.1); the decks leave it informal.

</div>

---

<div class="kicker p3">5 Context &middot; related work</div>

## Where this sits

<div class="small">

- **Functorial semantics.** Models as functors (Lawvere 1963; Riehl, §5.5), here with no equations and no products: representations of a quiver. The syntax-to-semantics step follows Jacobs's classifying categories.
- **Categorical probability.** $\mathsf{Stoch}$ as the Kleisli category of Giry's monad; Markov categories (Fritz 2020).
- **Categorical systems theory.** Open dynamical systems as lenses and double categories: Myers, *Categorical Systems Theory*; Spivak and coauthors (wiring-diagram operads, 2015; sheaves, 2019; $\mathbf{Poly}$, 2020; Niu and Spivak, 2025); AlgebraicDynamics (Libkind, Baas, Patterson and Fairbanks, 2021). Those frameworks compose systems along interfaces. Here the composite is fixed by the declaration, and the question is which functors out of it, and which transformations between them, carry the economics.
- **Open games and optics.** Ghani, Hedges, Winschel and Zahn (2018); Hedges and Rodríguez Sakamoto, value iteration as optic composition (2022) and reinforcement learning in categorical cybernetics (2025), where Bellman updates are parametrised optics and a representable functor does the work. Bakirtzis, Savvas and Topcu (2025): a category of MDPs with pushouts for task composition.
- **Decisions by Kan extension.** Mahadevan, *Universal decisions with Kan extensions*.

Shanker's own assessment: "not novel category theory; the applied mathematics is." The claim is a syntax for dynamic programs whose semantics is a functor, and the recognition of the economists' devices as the constructions above.

</div>

<!-- CHECK: this slide is new; the decks cite none of the systems-theory or open-games literature. -->

---

<div class="kicker p3">5 Context &middot; open</div>

## Not yet done

1. **Several inputs.** An equation that reads several fields is not an edge of a quiver. Symbols need arities, interpreted by products of state spaces; the product of measurable spaces is not a categorical product in $\mathsf{Stoch}$, because a kernel into $X\times Y$ is not determined by its two marginals.
2. **Branching.** A discrete choice among continuations reads several fields, so it needs item 1. Shanker: "branching is where the role of initial diagrams becomes really powerful."
3. **Dimension.** In Example A the quotient $q$ moves from functions of cash to functions of savings, both of one variable. Which declarations have coarsest cuts on functions of fewer variables than the state is not examined.
4. **Choice of staging.** Among the $\prod_k(n_k+1)$ stagings one may seek those with the fewest distinct stage terms, or those whose coarsest cuts are smallest; neither criterion is examined. A **rotation** of a closed term (a term with equal source and target type, cyclically shifted, so that the period starts at another field) gives operators related by a natural transformation under every model (Proposition 4); an exchange of two symbols does not.

---

<div class="kicker p3">5 Context &middot; questions</div>

## Questions for a category theorist

<div class="small">

1. Is "a quiver over a signature quiver, with models the functors out of $F(Q\times_{\mathcal{S}}\mathcal{S}_\Sigma)$" the right syntax, or should it be a sketch or a finite-product theory from the start, to admit arities and branching?
2. The backward model is a presheaf on $FQ_\Sigma$ built from $\mathsf{Meas}(-,\bar{\mathbb{R}})$ through the Giry monad. Is there a clean statement, as a Kleisli extension against a barycentre algebra, a profunctor, or an extranatural transformation, that also carries the rewards?
3. The Bellman operator $\mathbb{T}=\bigvee_\sigma T_\sigma$ is a join over a parallel family of arrows. Is the right setting $\mathsf{Pos}$- or $\mathsf{Sup}$-enriched models, with optimality a colimit-like operation on hom-posets?
4. Stagings are restrictions along cofaces. Do $\mathrm{Lan}_\pi$ and $\mathrm{Ran}_\pi$ have economic meaning, as canonical refinements or coarsenings of a stage structure?
5. The coarsest cut is an image in $\mathsf{Set}$. In which categories of value functions (bounded, measurable, $L^{p}$) does the (epi, mono) factorization still give the economists' reduction?
6. For branching: tree-shaped quivers, colimits, or initial algebras of polynomial functors as in the systems-theory literature?
7. Is the category of elements $\int D_b$, whose objects are a field with a value function and whose morphisms are the policy paths realizing it, a useful object? Backward induction walks in it.

</div>

<!-- CHECK: these questions are new; items 3, 4 and 7 are not raised in the decks. -->

---

<div class="kicker">References</div>

## References

<div class="small">

- Bakirtzis, G., M. Savvas and U. Topcu (2025). "Categorical Semantics of Compositional Reinforcement Learning." *JMLR* 26.
- Backus, J. (1978). "Can Programming Be Liberated from the von Neumann Style?" *CACM* 21(8), 613–641.
- Carroll, C. D., and A. Shanker (2026). *Theoretical Foundations of Buffer Stock Saving.*
- Fritz, T. (2020). "A Synthetic Approach to Markov Kernels, Conditional Independence and Theorems on Sufficient Statistics." *Advances in Mathematics* 370.
- Ghani, N., J. Hedges, V. Winschel and P. Zahn (2018). "Compositional Game Theory." *LICS '18*, 472–481.
- Goguen, J. A., J. W. Thatcher, E. G. Wagner and J. B. Wright (1977). "Initial Algebra Semantics and Continuous Algebras." *JACM* 24(1), 68–95.
- Hedges, J., and R. Rodríguez Sakamoto (2022). "Value Iteration is Optic Composition." *ACT 2022*; arXiv:2206.04547. — (2025). "Reinforcement Learning in Categorical Cybernetics." *EPTCS* 429, 270–286; arXiv:2404.02688.
- Jacobs, B. (1999). *Categorical Logic and Type Theory.* Elsevier.
- Lawvere, F. W. (1963). *Functorial Semantics of Algebraic Theories.* PhD thesis, Columbia; reprinted *TAC* Reprints 5 (2004).
- Libkind, S., A. Baas, E. Patterson and J. Fairbanks (2022). "Operadic Modeling of Dynamical Systems: Mathematics and Computation." *EPTCS* 372, 192–206.

</div>

---

<div class="kicker">References</div>

## References

<div class="small">

- Mahadevan, S. (2026). *Universal Decisions with Kan Extensions.*
- Myers, D. J. *Categorical Systems Theory.* Book draft, davidjaz.com/Papers/DynamicalBook.pdf.
- Niu, N., and D. I. Spivak (2025). *Polynomial Functors: A Mathematical Theory of Interaction.* Cambridge University Press.
- Riehl, E. *Category Theory in Context*, second edition. emilyriehl.github.io/files/context.pdf. (First edition: Dover, 2016.)
- Sargent, T. J., and J. Stachurski (2026). *Dynamic Programming, Volume II: General States*, draft of 10 April 2026.
- Schultz, P., D. I. Spivak and C. Vasilakopoulou (2020). "Dynamical Systems and Sheaves." *Applied Categorical Structures* 28, 1–57; arXiv:1609.08086.
- Shanker, A. (2026). *Categorical types and AGI*, Sessions A–C, akshayshanker.github.io/DP-cat; the companion note *fields-and-stages* and the Bellman-calculus appendices (private).
- Spivak, D. I. (2020). "Poly: An Abundant Categorical Setting for Mode-Dependent Dynamics." arXiv:2005.01894.
- Vagner, D., D. I. Spivak and E. Lerman (2015). "Algebras of Open Dynamical Systems on the Operad of Wiring Diagrams." *TAC* 30(51), 1793–1822.

</div>
