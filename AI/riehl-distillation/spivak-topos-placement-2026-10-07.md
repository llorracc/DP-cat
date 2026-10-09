# Where the Spivak–Topos work fits in the distillation

**Status:** memo, 2026-10-07, by Claude for Chris Carroll; not yet applied to the deck or the PR.
Prompted by a clarification from a member of Riehl's group that the dynamical-systems work meant
was "Spivak and coauthors at Topos". Sources were read in the primary text (the Niu–Spivak book, Myers's book draft, Spivak's
2020 Poly paper, Libkind–Spivak 2024/25, Lynch–Myers–Rischel–Staton 2026), not from summaries.

## 1. What the work is, in the terms the deck uses

**Poly** (Spivak, *Poly: an abundant categorical setting for mode-dependent dynamics*, 2020;
Niu & Spivak, *Polynomial Functors: A Mathematical Theory of Interaction*, CUP 2025). A polynomial
$p=\sum_{i\in p(1)}y^{p[i]}$ is an *interface*: positions $p(1)$ (outputs) and, at each position, a
set of directions $p[i]$ (inputs). Morphisms are dependent lenses: forward on positions, backward
on directions. A **dynamical system** with states $S$ and interface $p$ is a lens
$Sy^{S}\to p$ (Def. 4.18): a return map $S\to p(1)$ and an update $p[\mathrm{return}(s)]\to S$ at
each $s$. Monomial interfaces $Iy^{A}$ are Moore machines; general $p$ gives state-dependent input
sets. Of the four monoidal structures, $\otimes$ juxtaposes systems and wiring diagrams are lenses
$p_1\otimes\cdots\otimes p_n\to q$; the **composition product** $\triangleleft$ models multi-step
runs, $\mathrm{Run}_n(\varphi)\colon\mathfrak s\to p^{\triangleleft n}$, and "a lens
$p\to q_1\triangleleft\cdots\triangleleft q_n$ is a multi-step policy for $p$ to make decisions by
asking for decisions from $q_1$, then $q_2$, etc." (Niu–Spivak, p. 194). Comonoids in
$(\mathbf{Poly},\triangleleft)$ are categories (Ahman–Uustalu); the **cofree comonoid** $\mathcal T_p$
is the category of $p$-trees, "a decision tree of all the possible sequences of directions"
(§8.1); a system $Sy^S\to p$ is the same as a retrofunctor $Sy^S\nrightarrow\mathcal T_p$.
**Sections** $p\to y$ choose a direction at every position; "sectioning off" a system means
closing it with such a choice (§4.3.4, §4.4.2).

**Pattern runs on matter** (Libkind & Spivak, ACT 2024, EPTCS 429, 2025). The free monad
$\mathfrak m_p$ on $(\mathbf{Poly},\triangleleft)$ has as positions the *terminating* decision trees
of shape $p$ ("pattern"); the cofree comonad $\mathfrak c_q$ the infinite behavior trees
("matter"); a natural module action $\Xi\colon\mathfrak m_p\otimes\mathfrak c_q\to\mathfrak m_{p\otimes q}$
is "runs on": a terminating tree of choices evaluated against a system.

**Categorical systems theory** (Myers, book draft 2023; Libkind & Myers, *Towards a double operadic
theory of systems*, 2025). "Systems theories" as doctrines; deterministic, differential and
non-deterministic systems, the last via the Kleisli category of a commutative monad (§2.3; Myers's
$\mathrm D$ is finitely supported distributions, Giry is not treated in the draft). §2.4 "Adding
rewards": the commutative monad $M(R\times-)$ puts rewards into the dynamics,
$\mathrm{update}\colon\mathrm{State}\times\mathrm{In}\to\mathrm D(\mathbb R\times\mathrm{State})$,
and "a Markov decision process is a dynamical system in a stochastic systems theory" (ch. 1).
Behaviors are *representable*: a behavior of shape $T$ in $S$ is a map of systems $T\to S$, and
trajectories are the behaviors of shape **Time** $=(\mathbb N,t\mapsto t+1)$ (Example 3.3.0.7),
which is Riehl's Example 2.1.1 made into a general principle. Two kinds of composition (wiring and
maps) are organized as double categories.

**Clock systems for stochastic and non-deterministic categorical systems theories** (Lynch, Myers,
Rischel, Staton, MFPS 2026, arXiv:2603.29573). For $M$-Moore machines
$X\times A^-\to M(X)$, including the Giry monad on $\mathsf{Meas}$, trajectories are represented by a
"clock system": in the measurable stochastic case a machine producing a growing stream of random
seeds, with trajectories the random variables indexed by a filtration. Companion: Wang,
*Nondeterministic behaviours in double categorical systems theory* (2025), via Markov categories
with conditionals.

**Software and composition:** Libkind, Baas, Patterson, Fairbanks, *Operadic modeling of dynamical
systems* (ACT 2021) and AlgebraicDynamics.jl: systems as operad algebras over wiring diagrams.
**Behaviors as sheaves:** Schultz, Spivak, Vasilakopoulou (2020): behaviors on time intervals glue
along boundaries. **Adjacent:** Smithe (Topos-affiliated), Bayesian lenses: a kernel with its
Bayesian inversion as a lens.

**What this literature does not have** (checked by keyword in both books): value functions, policy
operators, the Bellman operator, dynamic programming. It has the forward, sample-path side
(stochastic Moore machines, accumulated rewards, trajectories) and the composition of open systems.
The backward model, the pairing, stages as natural transformations and the coarsest cut are not
there. That is the honest statement of where Akshay's work adds something.

## 2. Correspondences with the deck's constructions

| deck | Spivak–Topos | strength |
|---|---|---|
| slide 17: $E=\{(s,b):b\in\mathcal D(s)\}\to S$; policies are sections; policy insertion $w\mapsto w\iota_\sigma$ | the polynomial $p=\sum_{s\in S}y^{\mathcal D(s)}$ has $p(1)=S$ and total directions $E$; a policy is a section, i.e. a lens $p\to y$ (§4.3.4); inserting it is "sectioning off" the open system (§4.4.2) | exact |
| slide 3/6: a decision problem with feasible correspondence $\mathcal D$; forward model in $\mathsf{Stoch}$ | a dependent dynamical system $Sy^S\to p$ with state-dependent direction sets; stochastic systems as Kleisli lenses (Myers §2.2–2.3, Lynch et al.) | exact, modulo open vs. closed loop: the deck's $Q_\Sigma$ indexes edges by policies (closed loop); Poly keeps the action as an input (open loop); a policy closes it |
| slide 7: rewards in the backward model | Myers §2.4: rewards in the forward monad $M(R\times-)$; no value functions | complementary |
| slides 9–10: groupings as $[n]\rightarrowtail[N]$; stages; the trellis composing stage files | $\mathrm{Run}_n$ and lenses $p\to q_1\triangleleft\cdots\triangleleft q_n$ as multi-step policies "asking $q_1$, then $q_2$"; stage files as boxes composed by wiring diagrams (Libkind et al.) | close; the deck fixes the composite by the declaration, Topos composes open boxes |
| slide 19: initiality of $(\mathbb N,s,0)$; Session A's objective, "natural diagrams for broader classes of stochastic and branching systems" | Myers's representable behaviors; Lynch–Myers–Rischel–Staton give the clock system for Giry-monad machines (2026) | direct hit on a stated objective |
| slide 21 item 2: branching; slide 22 question 6 | free monad $\mathfrak m_p$ = terminating decision trees; the sequential case is paths in the free category; "pattern runs on matter" is evaluating a term in a model | the right generalization of "terms are paths" |
| slide 22 question 7: is $\int D_b$ useful? | Niu–Spivak Prop. 7.108 (copresheaves on $C$ ≅ discrete opfibrations over $C$) and Exercise 7.107 (the category of elements of a functor out of a free category is again free on a graph) | answers the shape of the question: $\int D_b$ is a discrete fibration over $FQ_\Sigma$, itself free on the graph of (field, value function) pairs |
| slide 21 item 1: arities/products | Myers §1.3.4: wiring diagrams with operations as lenses in a Lawvere theory | minor |

## 3. Proposed placements, with draft text

Each is one to three lines; none changes a result. Numbers are the deck's slides.

**Slide 17 (add after Proposition 10).**
> In Poly the data $E\to S$ is the polynomial $p=\sum_{s\in S}y^{\mathcal D(s)}$: positions are
> states, directions the feasible actions. A policy is a lens $p\to y$, a *section* (Niu–Spivak
> §4.3.4), and inserting it is *sectioning off* the open system (§4.4.2). Proposition 10 says
> these are the only operations natural in the value set.

**Slide 19 (replace the second "It does" bullet).**
> The initiality of $(\mathbb N,s,0)$ (Riehl, Example 2.1.1) is, in Myers's systems theory, the
> statement that trajectories are representable behaviors, maps from the clock system Time
> (*Categorical Systems Theory*, Ex. 3.3.0.7). Lynch, Myers, Rischel and Staton (2026) construct the
> clock system for Giry-monad machines: a stream of random seeds, with trajectories the random
> variables of a filtration. That is the stochastic case of Session A's objective.

**Slide 21 item 2 (branching), replace.**
> **Branching.** A discrete choice among continuations reads several fields. In Poly the terms of
> a branching program are the positions of the free monad $\mathfrak m_p$, terminating decision trees
> of shape $p$; the sequential case, where $p$ is linear, is the paths of $FQ_\Sigma$. Libkind and
> Spivak's module action $\mathfrak m_p\otimes\mathfrak c_q\to\mathfrak m_{p\otimes q}$, "pattern runs
> on matter", is the evaluation of such a term in a system. The conjecture to test is that a
> branching declaration is a polynomial and its models are retrofunctors into the cofree comonoid.

**Slide 10 (one line after the stage form).**
> In Poly, $n$ steps of a system are $\mathrm{Run}_n\colon\mathfrak s\to p^{\triangleleft n}$, and a
> lens $p\to q_1\triangleleft\cdots\triangleleft q_n$ "asks $q_1$ for a decision, then $q_2$" (Niu–Spivak,
> p. 194): a staging is a $\triangleleft$-factorization of the interface. The Bellman project's trellis,
> which composes stage files into periods, is a wiring diagram in the sense of Libkind et al.

**Slide 20 (replace the systems-theory bullet).**
> **Categorical systems theory (Topos).** Open systems as lenses $Sy^S\to p$ in Poly (Spivak 2020;
> Niu–Spivak 2025), stochastic ones through the Kleisli category of a monad with rewards put into
> the monad (Myers 2023, §2.2–2.4: "a Markov decision process is a dynamical system in a stochastic
> systems theory"); composition by wiring diagrams and operads (Libkind et al. 2021); behaviors as
> representable functors, with clock systems for stochastic theories (Lynch–Myers–Rischel–Staton
> 2026). That literature has the forward, sample-path side and the composition of open boxes. It
> has no value functions, policy operators or Bellman operator; the backward model, the pairing,
> and stages as natural transformations are what is added here.

**Slide 22 (sharpen questions 6 and 7).**
> 6. Branching: is the right syntax a polynomial $p$ rather than a quiver, with terms the free monad
>    $\mathfrak m_p$ and models retrofunctors into $\mathcal T_p$?
> 7. $\int D_b$ is a discrete fibration over $FQ_\Sigma$ and, by the dual of Niu–Spivak's Exercise
>    7.107, free on the graph of pairs (field, value function). Is this the right home for backward
>    induction?

**Slide 7 (footnote, optional).**
> Myers (§2.4) puts rewards into the forward dynamics through the monad $M(\mathbb R\times-)$; here they
> enter the backward model instead, and the pairing relates the two.

**References to add.** Libkind & Spivak (2025), *Pattern runs on matter*, EPTCS 429, 1–28;
Lynch, Myers, Rischel & Staton (2026), *Clock systems for stochastic and non-deterministic
categorical systems theories*, arXiv:2603.29573; Libkind & Myers (2025), *Towards a double
operadic theory of systems*, arXiv:2505.18329; Wang (2025), *Nondeterministic behaviours in double
categorical systems theory*, arXiv:2502.02517. Already present: Spivak 2020; Niu–Spivak 2025;
Myers 2023; Schultz–Spivak–Vasilakopoulou 2020; Libkind–Baas–Patterson–Fairbanks 2022.

## 4. Caveats

- The open/closed-loop difference is real: the deck's $Q_\Sigma$ fixes a policy per edge; Poly keeps
  actions as inputs. The exact bridge is slide 17's section $p\to y$. Say so rather than claim the
  frameworks coincide.
- "Models are retrofunctors into the cofree comonoid" is a conjecture for the branching case; in
  the sequential case it should reduce to functors out of $FQ_\Sigma$, but that reduction was not
  checked here.
- Myers's draft uses finitely supported distributions; the measurable (Giry) case is in the 2026
  paper, which I read in its introduction only.
- Slide budget: these additions fit without new slides if slide 20 is rewritten rather than
  extended and the slide-7 footnote is dropped.
