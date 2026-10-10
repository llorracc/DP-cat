# The buffer-stock saving problem, written the Spivak–Topos way

*An explainer for an economist. Written 2026-10-08 by Claude for Chris Carroll, from the primary
sources: Spivak, "Poly: an abundant categorical setting for mode-dependent dynamics" (2020);
Niu and Spivak, *Polynomial Functors: A Mathematical Theory of Interaction* (CUP 2025; cited by
section number of the 2024 draft); Myers, *Categorical Systems Theory* (draft, 2023); Libkind and
Spivak, "Pattern runs on matter" (ACT 2024); Lynch, Myers, Rischel and Staton, "Clock systems for
stochastic and non-deterministic categorical systems theories" (MFPS 2026). Where I say what the
framework "would" do for a feature it does not treat explicitly, I say so.*

---

## 1. The model, as you write it

A consumer has cash-on-hand $m>0$, consumes $c\in(0,m]$, saves $a=m-c$, earns return $R$, and
receives next period's income $\xi'\sim\nu$, independent across periods:

$$m'=R(m-c)+\xi'.$$

Utility $u(c)$, discount $\beta\in(0,1)$. A policy is a rule $c=\sigma(m)$. The value function
solves $v=\mathbb{T}v$,

$$(\mathbb{T}v)(m)=\max_{0<c\le m}\Big\{u(c)+\beta\,\mathbb{E}\,v\big(R(m-c)+\xi'\big)\Big\},$$

and for a fixed policy the policy operator is $T_\sigma v=u(\sigma(m))+\beta\,\mathbb{E}\,v(R(m-\sigma(m))+\xi')$.

Six ingredients, then: a **state** ($m$), a **feasible choice** ($c\in(0,m]$), a **shock**
($\xi'$), a **transition** ($m'=R(m-c)+\xi'$), a **reward** ($u$, discounted by $\beta$), and the
**value function** with its operators. Below, each is written three ways: yours, Akshay's, and
Spivak's. The short version of the comparison:

> Spivak's framework is a theory of **machines with plugs**. It says precisely what a machine
> shows to the world, what it accepts from the world, how machines are wired together, and what a
> run of a machine is. The buffer-stock consumer is such a machine, and the policy is literally the
> plug that closes its input. What the framework does **not** contain is the value function: nothing
> in it runs backward from the future. That backward half is exactly what Akshay's "backward model"
> supplies, and the two halves are tied together by his pairing $\langle\mu K,v\rangle=\langle\mu,K^{*}v\rangle$.

---

## 2. The Spivak vocabulary, in six ideas

**2.1 An interface is a polynomial.** Spivak writes the "shape of the plugs" of a machine as a
polynomial in a formal variable $y$:

$$p \;=\; \sum_{i\in p(1)} y^{\,p[i]} .$$

Read it as a menu: the machine can be in one of the **positions** $i\in p(1)$ (what it *shows*,
its output), and at position $i$ it accepts one of the **directions** $d\in p[i]$ (what it
*takes in*, its input). The exponent is the input set; the coefficient is the output set. Two
examples:

- $I\,y^{A}$: shows an element of $I$, always accepts an element of $A$. (Same input set at every
  output: a classical Moore machine interface.)
- $\sum_{m>0} y^{(0,m]}$: shows a number $m>0$ and accepts a number in $(0,m]$. **This is the
  consumer's interface**: it shows its cash and accepts a feasible consumption. The feasible set
  depends on the position; that is what the polynomial, as opposed to a monomial, buys you.
  Niu–Spivak call this a *dependent* interface (§4.2).

**2.2 A machine is a lens from its state system to its interface.** A dynamical system with
state set $S$ and interface $p$ is a map of polynomials

$$\varphi\colon S\,y^{S}\longrightarrow p,$$

which unpacks to two functions: a **return** map $S\to p(1)$ (which position does state $s$
show?) and, for each state, an **update** map $p[\mathrm{return}(s)]\to S$ (given the input the
world chose, what is the next state?). Niu–Spivak, Definition 4.18. Maps of polynomials go
*forward on positions and backward on directions*; that is why they are called lenses. For a
Moore machine with interface $Iy^{A}$ this is just $\mathrm{return}\colon S\to I$ and
$\mathrm{update}\colon S\times A\to S$ (Definition 4.1).

**2.3 Closing a plug is a section; a policy is a section.** A **section** of $p$ is a map
$p\to y$: it chooses one direction at every position. Composing $S y^{S}\to p\to y$ closes the
machine: it no longer takes input, and $S y^{S}\to y$ is nothing but a function $S\to S$, an
autonomous system. Niu–Spivak §4.3.4 ("sections as wrappers") and §4.4.2 ("sectioning off").
*A consumption rule $c=\sigma(m)$ is exactly a section of $\sum_m y^{(0,m]}$.* This is the single
most exact point of contact with the economics.

**2.4 Wiring is a lens between products; running is the composition product.** Two machines side
by side have interface $p\otimes q$ (positions $p(1)\times q(1)$, directions the products); a
**wiring diagram** that connects their plugs is a lens $p\otimes q\to r$ into the outer box's
interface (§4.4.3). Separately, the **composition product** $p\triangleleft q$ describes "a $p$-step
followed by a $q$-step", and $n$ steps of a machine form a lens
$\mathrm{Run}_n(\varphi)\colon Sy^{S}\to p^{\triangleleft n}$ (§6.1.4). Niu–Spivak read a lens
$p\to q_1\triangleleft\cdots\triangleleft q_n$ as "a multi-step policy for $p$ to make decisions by asking
for decisions from $q_1$, then $q_2$, etc." (p. 194).

**2.5 Randomness and rewards come from a monad.** Plain Poly is deterministic. Myers (ch. 2)
makes a machine stochastic by letting the update land in a probability monad: a *stochastic
system* has $\mathrm{update}\colon\mathrm{State}\times\mathrm{In}\to\mathrm D(\mathrm{State})$,
with $\mathrm D$ distributions (his draft uses finitely supported ones; Lynch–Myers–Rischel–Staton
2026 use the Giry monad on measurable spaces, which is the right one for $m\in\mathbb R_+$).
Myers §2.4 adds **rewards** the same way: for a commutative monoid $(R,+,0)$, the update lands
in $\mathrm D(R\times\mathrm{State})$, returning the reward together with the next state, and
rewards *accumulate by addition* along a run. He writes: "a Markov decision process is a dynamical
system in a stochastic systems theory" (ch. 1). Niu–Spivak do the same thing by wiring: a
"reward-tracking" machine whose state is the list of rewards so far, juxtaposed with the main
machine (§4.4, the robot example).

**2.6 Behaviours are maps from clock machines; all futures form a tree.** In Myers's theory a
*behaviour* of a machine $S$ is a map of machines $T\to S$ from a machine $T$ that does only that
behaviour. A **trajectory** is a map from the clock $\mathsf{Time}=(\mathbb N,\ t\mapsto t+1)$
(Example 3.3.0.7): the same universal property of $(\mathbb N,s,0)$ that Akshay's Session A takes
from Riehl, now as a general principle. For stochastic machines the 2026 paper shows a clock exists
too: a machine emitting a growing stream of random seeds, whose maps into $S$ are the
sample paths. Finally, the **cofree comonoid** $\mathcal T_p$ on an interface is "the decision tree
of all the possible sequences of directions" (§8.1), and the **free monad** $\mathfrak m_p$ is the
set of *terminating* decision trees, i.e. finite-horizon plans; Libkind–Spivak's "pattern runs on
matter" is the operation of evaluating such a plan against a machine.

That is the whole toolkit needed below.

---

## 3. Side by side

Notation for Akshay's column: fields $m,a,m'$; symbols $\mathtt{save}\colon\mathtt{Xm}\to\mathtt{Xa}$
(a decision symbol with the policies as its set $\Sigma$) and $\mathtt{income}\colon\mathtt{Xa}\to\mathtt{Xm}$;
$D_f$ the forward model (into Markov kernels), $D_b$ the backward model (value functions and
operators). Akshay chooses savings $a=\sigma(m)$ as the control; Spivak's column keeps $c$ to match
yours. Nothing depends on that choice.

| element | as you write it | Akshay (free category) | Spivak–Topos (Poly, systems theory) |
|---|---|---|---|
| **state** | cash $m>0$ | field $m$ with carrier $X_m=(0,\infty)$ | the position set $p(1)=(0,\infty)$ of the consumer's interface; equally the state set $S$ of the state system $Sy^{S}$ |
| **feasible choice** | $c\in(0,m]$ | decision symbol $\mathtt{save}$; one parallel edge $(\mathtt{save},\sigma)$ per policy | the directions at position $m$: $p[m]=(0,m]$. The interface is $p=\sum_{m>0}y^{(0,m]}$ |
| **shock** | $\xi'\sim\nu$ i.i.d. | inside the income kernel $P(a,B)=\nu\{\xi:Ra+\xi\in B\}$ | two options: (a) an input from a second machine, "Nature", a *stochastic source* (Myers Ex. 2.2.0.9) wired in; (b) integrated into a stochastic update, $\mathrm{update}(m,c)=\text{law of }R(m-c)+\xi'$ in $\mathrm D(S)$ |
| **transition** | $m'=R(m-c)+\xi'$ | edge $\ell\colon a\to m'$ sent by $D_f$ to the kernel $P$; the decision edge to $\delta_\sigma$ | the update half of the lens: deterministic $\mathrm{update}(m,(c,\xi'))=R(m-c)+\xi'$ with input set $(0,m]\times Z$, or stochastic as in (b) |
| **reward** | $u(c)$ | reward $r_{e,\sigma}=u(m-\sigma(m))$ attached to the decision edge in $D_b$ | Myers §2.4: update in $\mathrm D(\mathbb R\times S)$ returning $(u(c),m')$, rewards summed along the run; or Niu–Spivak's reward-tracking box wired in parallel |
| **discounting** | $\beta^{t}$ | factor $\beta_e$ on the edge; the backward operator is affine, $v\mapsto r+\beta K^{*}v$ | not a primitive. Myers's reward monoid must be commutative, and discounted accumulation $(r,b)\cdot(r',b')=(r+br',bb')$ is not; so either put the clock $t$ into the state and add $\beta^{t}u(c_t)$, or leave discounting to the backward side (Akshay's affine operators compose fine, since a category needs no commutativity) |
| **policy** | $c=\sigma(m)$ | the edge $(\mathtt{save},\sigma)$; Prop. 10: policies are the sections of $E=\{(m,c):c\in(0,m]\}\to S$ | a section $\sigma\colon p\to y$; composing $Sy^{S}\to p\to y$ closes the loop. **Exact match**: Akshay's $E\to S$ is the polynomial $p$ |
| **closed-loop dynamics** | the Markov chain $m\mapsto R(m-\sigma(m))+\xi'$ | the path $\mathtt{income}\cdot(\mathtt{save},\sigma)$ in the free category, sent by $D_f$ to the kernel $P\circ\delta_\sigma$ | the sectioned machine $Sy^{S}\to y$: a function $S\to S$ (deterministic) or a Markov kernel $S\rightsquigarrow S$ (stochastic) |
| **one period / stage** | arrive with $m$, decide $c$, continue to $m'$ | the stage form $T_\sigma=U\cdot D_b(e,\sigma)\cdot F$: arrival map, decision operator, continuation map | one run step, $\mathrm{Run}_1$; several periods $\mathrm{Run}_n\colon Sy^{S}\to p^{\triangleleft n}$; a lens $p\to q_1\triangleleft q_2$ "asks $q_1$ for a decision, then $q_2$" |
| **composing stages into periods** (the Bellman project's trellis) | — | groupings $[n]\rightarrowtail[N]$ of the chain; stagings | wiring diagrams: boxes with interfaces, a lens $p_1\otimes p_2\to q$; computationally, AlgebraicDynamics (Libkind–Baas–Patterson–Fairbanks) |
| **value function** | $v(m)$ | an element of $L^{X_m}=D_b(m)$; $D_b$ is a presheaf on the free category | **absent as a primitive.** The nearest object is the expected accumulated reward of the sectioned machine started at $m$, a functional of its trajectories |
| **policy operator** | $T_\sigma v=u(\sigma(m))+\beta\,\mathbb E\,v(m')$ | $D_b$ applied to the one-period path: the image of a path is the operator | absent; the framework runs forward only. (Value iteration as composition of *optics* is Hedges–Rodríguez Sakamoto, Strathclyde, not Topos) |
| **Bellman operator** | $\mathbb T v=\max_c\{\cdots\}$ | $\mathbb T=\bigvee_\sigma T_\sigma$, a join over the parallel edges | absent |
| **simulation** | draw $\xi_1,\xi_2,\ldots$, iterate | Session A: a trajectory is the unique map from $(\mathbb N,s,0)$ | a behaviour of shape $\mathsf{Time}$; stochastically, a map from the random-seed clock (Lynch–Myers–Rischel–Staton 2026) |
| **all possible futures** | the decision tree from $m_0$ | the set of paths out of $m_0$ in the free category $FQ_\Sigma$ | the cofree comonoid $\mathcal T_p$: the tree of all direction sequences; finite-horizon plans are the free monad $\mathfrak m_p$ |
| **portfolio choice / branching** | two decisions per period; discrete choices | Example E; an open problem for the free-category syntax | a polynomial with several positions, or two boxes under $\otimes$; plans are trees, not paths |

Reading the table top to bottom: the first eight rows translate essentially word for word; the
two frameworks differ only in whether the policy is baked into the edge (Akshay) or applied as a
plug (Spivak). The value-function rows are where Spivak's framework stops and Akshay's continues.

---

## 4. One period of the buffer-stock consumer, in their notation

Here is the model written out as Niu–Spivak would, deterministic form first (the shock is an input
from the world), then the stochastic form (the shock is integrated).

**Interface.** $p=\sum_{m>0}y^{(0,m]\times Z}$ in the deterministic form: the consumer shows $m$ and
accepts a pair (consumption $c$, shock $\xi'$). In the stochastic form, $p=\sum_{m>0}y^{(0,m]}$:
only $c$ is an input.

**Machine.** $\varphi\colon Sy^{S}\to p$ with $S=(0,\infty)$:
- return: $m\mapsto m$ (the consumer shows its cash);
- update at $m$: $(c,\xi')\mapsto R(m-c)+\xi'$ (deterministic), or $c\mapsto$ the law of
  $R(m-c)+\xi'$ under $\nu$ (stochastic; an update into $\mathrm D(S)$, Myers Def. 2.2.0.5, or into
  the Giry monad as in Lynch et al.).

**Reward.** Either enlarge the update to return $(u(c),\,m')$ in $\mathrm D(\mathbb R\times S)$
(Myers §2.4), so that a run of length $n$ returns $\sum_{t<n}u(c_t)$ beside $m_n$; or wire a second
box, $\psi\colon \mathrm{List}(\mathbb R)\,y^{\mathrm{List}(\mathbb R)}\to y^{\mathbb R}$, which accepts a
reward and appends it, next to the consumer, feeding it $u(c)$ each step (Niu–Spivak §4.4).
Discounting is handled by adding $t$ to the state and feeding $\beta^{t}u(c_t)$.

**Policy.** $\sigma\colon p\to y$, the section that at position $m$ picks the direction
$c=\sigma(m)$ (and, in the deterministic form, still leaves $\xi'$ to be supplied by Nature:
wire in a stochastic source $Z$). The sectioned machine $Sy^{S}\to p\xrightarrow{\sigma}y$ is the
Markov chain $m\mapsto R(m-\sigma(m))+\xi'$.

**Runs.** $\mathrm{Run}_n(\varphi)\colon Sy^{S}\to p^{\triangleleft n}$ is the $n$-period problem as a single
lens: its positions are the trees of what the consumer shows over $n$ periods, its directions the
sequences of consumptions. A lens $p\to q_1\triangleleft\cdots\triangleleft q_n$ is their "multi-step
policy", which is where a staging would be written.

**Trajectories.** A map of machines $\mathsf{Time}\to(\text{sectioned consumer})$ is a sequence
$m_0,m_1,\ldots$ with $m_{t+1}=R(m_t-\sigma(m_t))+\xi_{t+1}$; stochastically, a map from the
random-seed clock is a sample path.

**What is still missing.** $v$, $T_\sigma$ and $\mathbb T$. To state them you need, for each state
space, the *space of functions on it*, and for each kernel the operator $K^{*}$ that pulls a
function back through the kernel. That is Akshay's backward model $D_b$, a functor against the
arrows of the forward model. Spivak's machinery is covariant: it pushes states and distributions
forward along runs and never pulls functions back. The one place the two meet is the pairing

$$\langle\mu K,\,v\rangle=\langle\mu,\,K^{*}v\rangle,$$

"the expected value of $v$ after a step equals the value of the pulled-back $v$ before it", which
Akshay shows is natural (Session B). In Topos terms: the forward model is a stochastic machine in
Myers's sense; the backward model is what you would add to Myers's framework to do dynamic
programming in it.

---

## 5. Glossary

| economics | Akshay's decks | Spivak–Topos |
|---|---|---|
| state variable | field; type | position; state of the state system |
| feasible set at a state | policy set of a decision symbol | directions at a position, $p[i]$ |
| transition equation | edge; its kernel under $D_f$ | update map (half of a lens) |
| observable / what is reported | — | return map; the position shown |
| policy | a parallel edge $(e,\sigma)$; a section of $E\to S$ | a section $p\to y$ |
| closing the loop with a policy | fixing one edge per decision | sectioning off (§4.4.2) |
| period | one-period path; stage | one step; $\mathrm{Run}_1$ |
| $N$-period problem | chain declaration with $2N-1$ edges | $\mathrm{Run}_N\colon Sy^{S}\to p^{\triangleleft N}$ |
| putting stages into a period (trellis) | grouping; staging | wiring diagram; operad algebra (AlgebraicDynamics) |
| simulation from $m_0$ | map from $(\mathbb N,s,0)$ | behaviour of shape $\mathsf{Time}$ / random-seed clock |
| expected utility along a path | — | accumulated reward in the monad $\mathrm D(\mathbb R\times-)$ |
| value function; Bellman operator | $D_b$; $\bigvee_\sigma T_\sigma$ | not in the framework |
| decision tree | paths of $FQ_\Sigma$ | cofree comonoid $\mathcal T_p$ (all futures); free monad $\mathfrak m_p$ (finite plans) |
| discrete choice / branching | open problem | polynomial with several positions; trees |

---

## 6. What an economist gets from the translation

1. **A precise notion of "the plumbing" of a model.** The interface $\sum_m y^{(0,m]}$ records
   the state space and the state-dependent feasible set in one object, before any kernel or
   utility is chosen. Akshay's signature does the same job for the sequential case; Spivak's
   version also handles state-dependent and branching choice sets.
2. **Composition as a first-class operation.** Wiring diagrams are the mathematics behind the
   Bellman project's "stage files composed by a trellis file". AlgebraicDynamics is software that
   does this composition for deterministic systems in continuous and discrete time; it has no
   value side. The one existing software that composes Bellman problems is the CyberCat group's
   Open Game Engine (Haskell), which treats Bellman operators as optics; see the note on
   AlgebraicDynamics and the Bellman project.
3. **A principled definition of simulation for stochastic models** (the 2026 clock-systems
   result): sample paths are the maps from a universal random-seed machine, in the same way
   deterministic trajectories are maps from the clock. This is the stochastic version of the
   universality Akshay's Session A ends on.
4. **A clear statement of what is new in the DP-cat work.** Everything forward-looking in it has a
   Topos counterpart; the backward model, the pairing, and the stage results have none. If a
   category theorist asks "isn't this just systems theory?", the answer is the value-function rows
   of the table in §3.

---

## 7. References

- Spivak, D. I. (2020). "Poly: An abundant categorical setting for mode-dependent dynamics." arXiv:2005.01894.
- Niu, N., and D. I. Spivak (2025). *Polynomial Functors: A Mathematical Theory of Interaction.* Cambridge University Press; draft arXiv:2312.00990 (section numbers above).
- Myers, D. J. (2023). *Categorical Systems Theory.* Book draft, davidjaz.com/Papers/DynamicalBook.pdf.
- Libkind, S., and D. I. Spivak (2025). "Pattern Runs on Matter: The Free Monad Monad as a Module over the Cofree Comonad Comonad." *EPTCS* 429, 1–28; arXiv:2404.16321.
- Lynch, O., D. J. Myers, E. F. Rischel and S. Staton (2026). "Clock systems for stochastic and non-deterministic categorical systems theories." MFPS 2026; arXiv:2603.29573.
- Libkind, S., and D. J. Myers (2025). "Towards a double operadic theory of systems." arXiv:2505.18329.
- Libkind, S., A. Baas, E. Patterson and J. Fairbanks (2022). "Operadic Modeling of Dynamical Systems: Mathematics and Computation." *EPTCS* 372, 192–206; AlgebraicDynamics.jl.
- Schultz, P., D. I. Spivak and C. Vasilakopoulou (2020). "Dynamical Systems and Sheaves." *Applied Categorical Structures* 28, 1–57.
- Hedges, J., and R. Rodríguez Sakamoto (2022). "Value Iteration is Optic Composition." ACT 2022; arXiv:2206.04547. (Not Topos; the nearest treatment of the value side.)
- Shanker, A. (2026). *Categorical types and AGI*, Sessions A–C. akshayshanker.github.io/DP-cat.
