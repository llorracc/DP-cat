# The buffer-stock saving problem in the Open Game Engine framework

*An explainer for an economist, companion to "The buffer-stock saving problem, written the
Spivak–Topos way". Written 2026-10-08 by Claude for Chris Carroll, from the primary sources: Ghani,
Hedges, Winschel and Zahn, "Compositional game theory" (LICS 2018); Bolt, Hedges and Zahn, "Bayesian
open games" (Compositionality 2023); Hedges and Rodríguez Sakamoto, "Value iteration is optic
composition" (ACT 2022, EPTCS 380); the same authors' "Reinforcement learning in categorical
cybernetics" (EPTCS 429, 2025); and the engine itself, github.com/CyberCat-Institute/open-game-engine
(Haskell; its tutorial, `Examples/Decision.hs` and `Examples/Markov/NStageMarkov.hs`). The Haskell
in §5 is written in the engine's syntax by analogy with those examples; it has not been compiled.*

---

## 1. The model, as you write it

Cash $m>0$; consumption $c\in(0,m]$; savings $a=m-c$; return $R$; income $\xi'\sim\nu$ i.i.d.;
$m'=R(m-c)+\xi'$; utility $u(c)$, discount $\beta$. Policy $c=\sigma(m)$. Value function
$v=\mathbb T v$ with $(\mathbb Tv)(m)=\max_{0<c\le m}\{u(c)+\beta\,\mathbb E\,v(R(m-c)+\xi')\}$ and
policy operator $T_\sigma v=u(\sigma(m))+\beta\,\mathbb E\,v(R(m-\sigma(m))+\xi')$.

The short version of the comparison:

> The open-games framework is a theory of **boxes with two-way wires**: information flows forward
> (states, actions), payoffs flow backward (utilities, continuation values), and a box composes with
> the next by plugging both directions at once. The buffer-stock period is one such box; its forward
> pass is the transition kernel and its backward pass is $r\mapsto u(c)+\beta r$. Composing boxes is
> composing periods, and **value improvement is literally precomposition with the period's box**
> ("value iteration is optic composition"). So, unlike Spivak's framework, this one *does* contain the
> value function: it is the thing that closes the backward wire. What the engine as shipped does
> with it is check optimality of a policy you supply, on a finite action grid, rather than solve.

---

## 2. The open-games vocabulary, in five ideas

**2.1 An open game is a box with four wires.** Ghani–Hedges–Winschel–Zahn: an open game
$\mathcal G\colon(X,S)\to(Y,R)$ receives an *observation* $X$ from the past and sends an *output*
$Y$ to the future (forward wires); it receives a *return* $R$, the payoff coming back from the
future, and passes *feedback* $S$ back to the past (backward wires). Inside: a set of strategies
$\Sigma$, a play function $\Sigma\times X\to Y$, a coplay function $\Sigma\times X\times R\to S$,
and a best-response relation. Boxes compose **sequentially** (one's $Y$ is the next's $X$, and
returns flow back the other way) and **in parallel** (side by side, for simultaneous moves). A
Nash equilibrium of the composite is a profile with no profitable deviation in any box.

**2.2 The forward-and-backward pair is an optic.** Strip out the strategies and the box is an
*optic* $(X,S)\to(Y,R)$: a forward map $X\to M\otimes Y$ and a backward map $M\otimes R\to S$, where
the *residual* $M$ carries what the backward pass needs to remember from the forward pass (here:
the state and action). Optics compose by stacking residuals. In a cartesian setting this is a lens
(forward $X\to Y$, backward $X\times R\to S$); in a Markov-kernel setting (Bolt–Hedges–Zahn's
*Bayesian open games*) the forward map is a kernel and expectations appear in the backward map.
Hedges–Rodríguez Sakamoto use the mixed category $\mathbf{Optic}_{\mathbf{Mark},\mathbf{Conv}}$:
"the forwards direction is a Markov kernel and the backwards direction is a function involving
expectations" (§3.1, Example 4).

**2.3 An MDP is one optic; a policy is another; a value function is a costate.** This is the
content of "Value iteration is optic composition", §4. Given state space $X$, actions $A$,
transition $f\colon X\times A\to X$ (or a kernel), reward $U\colon X\times A\to\mathbb R$ and discount
$\beta$:

- the **decision-process optic** $\lambda\colon\binom{X\otimes A}{\mathbb R}\to\binom{X}{\mathbb R}$ has
  forward pass $(x,a)\mapsto(x,a,\,f(x,a))$ (remember the pair, move the state) and backward pass
  $(x,a,r)\mapsto U(x,a)+\beta r$ (add this period's reward to the discounted continuation);
- a **policy** $\pi\colon X\to A$ is the optic $\binom{X}{\mathbb R}\to\binom{X\otimes A}{\mathbb R}$ with
  forward $x\mapsto(x,\pi(x))$ and backward the identity on $\mathbb R$;
- a **value function** $V\colon X\to\mathbb R$ is a *costate*, an optic $\binom{X}{\mathbb R}\to I$ into
  the unit: no forward content, backward pass $V$.

Then **value improvement is the composite** $\pi\,\#\,\lambda\,\#\,V$, which unpacks to
$V'(x)=U(x,\pi(x))+\beta V(f(x,\pi(x)))$; in the stochastic case,
$V'(x)=\mathbb E_{a\sim\pi(x)}[U(x,a)+\beta V(f(x,a))]$. **Policy improvement** is
$\pi'(x)=\arg\max_{a}(\lambda\#V)(x,a)$. Alternating the two is value iteration; the authors'
slogan is that value improvement is a *representable functor* on optics, $\mathrm{Optic}(-,I)$.

**2.4 The engine's language.** A game is written as a block,

    name params = [opengame|
        inputs    : x ;        -- X, what this box observes
        feedback  : s ;        -- S, what it passes back
        :-----:
        ... LineBlocks ...
        :-----:
        outputs   : y ;        -- Y, what it sends forward
        returns   : r ;        -- R, what comes back to it
    |]

whose LineBlocks each have an `operation`. The operations you need: `dependentDecision "player"
(\x -> actionList)`, a player choosing from a (finite) list that may depend on the observation;
`forwardFunction f` and `liftStochasticForward process`, deterministic or stochastic maps on the
forward wire; `natureDraw dist`, a move of nature; `backwardFunction g`, a map on the backward
wire; and `discount "player" (\x -> x * β)`, which scales the payoffs flowing back. A **strategy**
is a `Kleisli Stochastic x y`, i.e. a function from observations to distributions over actions
(`pureAction` for a constant, `playDeterministically` for a pure choice). **Analysis** is
`evaluate game strategies context`, which checks whether the supplied strategies are an
equilibrium and otherwise lists profitable deviations; the *context* supplies what is outside the
box, in particular the continuation payoff.

**2.5 Markov games in the engine.** `Examples/Markov/NStageMarkov.hs` shows the pattern for a
repeated game with a state: a stage game whose `inputs` carry the state and last actions, a
`liftStochasticForward` transition to the next state, `discount` operations on the backward wire,
and a *continuation context* built by `determineContinuationPayoffs`, which plays the game
forward for a finite number of iterations with the supplied strategy, feeding each stage's
continuation payoff back. That is finite-horizon backward induction done by composing the stage
optic with itself; the tutorial notes that "repeated games are not possible (but Markov games will
be soon)", and the Markov examples are how they are done.

---

## 3. Side by side

Akshay's column uses his symbols (fields $m,a,m'$; decision symbol `save`; forward model $D_f$,
backward model $D_b$); the open-games column uses Hedges–Rodríguez Sakamoto's optics and the
engine's operations.

| element | as you write it | Akshay (free category) | open games / optics / the engine |
|---|---|---|---|
| **state** | cash $m$ | field $m$, carrier $X_m$ | the observation wire $X$ into the period's box; `inputs : m` |
| **feasible choice** | $c\in(0,m]$ | decision symbol with policy set $\Sigma$ | `dependentDecision "household" (\m -> grid m)`: an action list that depends on the observation; in the paper, the constraint set $A(x)$ imposed inside the $\arg\max$ (Example 8) |
| **shock** | $\xi'\sim\nu$ | inside the income kernel $P$ | a move of nature on the forward wire: `natureDraw ν` or folded into `liftStochasticForward`; mathematically the forward pass lives in the Kleisli category $\mathbf{Mark}$ (Bayesian open games) |
| **transition** | $m'=R(m-c)+\xi'$ | edge $\ell\colon a\to m'$, $D_f(\ell)=P$ | the **forward pass** of the period optic $\lambda$: $(m,c)\mapsto(m,c,\,R(m-c)+\xi')$; in the engine `liftStochasticForward (\(m,c) -> law of R(m-c)+ξ')` |
| **reward** | $u(c)$ | reward $r_{e,\sigma}$ on the decision edge in $D_b$ | the **backward pass** of $\lambda$: $(m,c,r)\mapsto u(c)+\beta r$; in the engine the decision's `returns : u c` plus what flows back |
| **discounting** | $\beta$ | factor $\beta_e$ on the edge | the $\times\beta$ box on the backward wire; in the engine `discount "household" (\x -> x * β)` |
| **policy** | $c=\sigma(m)$ | parallel edge $(\mathtt{save},\sigma)$; Prop. 10: a section | a **strategy** `Kleisli Stochastic m c`; as mathematics, the optic $\pi\colon\binom{X}{\mathbb R}\to\binom{X\otimes A}{\mathbb R}$ with forward $m\mapsto(m,\sigma(m))$ |
| **closed-loop period** | the chain $m\mapsto R(m-\sigma(m))+\xi'$ with reward $u(\sigma(m))$ | the path $\ell\cdot(\mathtt{save},\sigma)$ under $D_f$ and $D_b$ | the composite optic $\pi\#\lambda$: forward the kernel, backward $r\mapsto u(\sigma(m))+\beta r$ |
| **value function** | $v(m)$ | element of $D_b(m)=L^{X_m}$ | a **costate** $V\colon\binom{X}{\mathbb R}\to I$; in the engine, the continuation payoff supplied by the context |
| **policy operator** $T_\sigma$ | $T_\sigma v=u(\sigma(m))+\beta\,\mathbb E\,v(m')$ | $D_b$ of the period path | **precomposition with the optic**: $V\mapsto\pi\#\lambda\#V$ ("value improvement is optic composition") |
| **Bellman operator** $\mathbb T$ | $\max_c\{u(c)+\beta\,\mathbb E\,v(m')\}$ | $\bigvee_\sigma T_\sigma$ | policy improvement $\pi'(m)=\arg\max_{c\in A(m)}(\lambda\#V)(m,c)$ interleaved with value improvement; a fixed point $(\pi^*,V^*)$ satisfies $V^*=\max_c(\lambda\#V^*)$ |
| **optimality check** | Euler / no profitable deviation | — | `evaluate` with the continuation context: for one player this is a one-shot-deviation check of the supplied policy against the Bellman condition on the action grid |
| **$N$ periods** | backward induction | chain of $2N-1$ edges; stage form | sequential composition $\pi_1\#\lambda\#\pi_2\#\lambda\#\cdots\#V_N$; in the engine, `determineContinuationPayoffs` iterating the stage |
| **stages inside a period** | arrival, decision, continuation | $T_\sigma=U\cdot D_b(e,\sigma)\cdot F$ | three optics composed; the arrival and continuation optics have trivial decisions |
| **simulation** | draw shocks, iterate | map from $(\mathbb N,s,0)$ | the forward passes alone: `play` / `extractNextState` |
| **portfolio choice, several agents** | two decisions; equilibrium | Example E; open problem | the home ground: parallel composition $\otimes$ of decision boxes, Nash equilibrium of the composite |

Two rows deserve comment. The *value function* row is where this framework differs from Spivak's:
the costate is a primitive, and $T_\sigma$ is the most basic operation the framework has.
The *feasible choice* row is where the engine differs from the paper: the engine enumerates a
finite action list to test deviations, so $(0,m]$ becomes a grid; the paper's Example 8 keeps
$A(x)$ continuous by doing the $\arg\max$ "externally to the category".

---

## 4. The buffer-stock period as an optic, written out

Objects are pairs (forward type, backward type). Take $X=M=(0,\infty)$ for cash, $A$ for
consumption, $\mathbb R$ for payoffs.

**The period optic** $\lambda\colon\binom{M\otimes A}{\mathbb R}\to\binom{M}{\mathbb R}$, residual
$M\otimes A$:
- forward: $(m,c)\;\mapsto\;\big((m,c),\;R(m-c)+\xi'\big)$, a Markov kernel into $M\otimes A\otimes M$
  (keep the pair, draw next cash);
- backward: $\big((m,c),\,r\big)\;\mapsto\;u(c)+\beta\,r$, extended by expectation over the residual's
  law, as in the paper's $g'(\alpha)(r)=\mathbb E U(\alpha)+\beta r$.

**The policy optic** $\pi_\sigma\colon\binom{M}{\mathbb R}\to\binom{M\otimes A}{\mathbb R}$: forward
$m\mapsto(m,\sigma(m))$, backward $r\mapsto r$.

**A value function** $V\colon\binom{M}{\mathbb R}\to I$: backward pass $m\mapsto V(m)$.

**Value improvement.** $\pi_\sigma\#\lambda\#V$ is the costate
$$m\;\longmapsto\;u(\sigma(m))+\beta\,\mathbb E_{\xi'}\,V\big(R(m-\sigma(m))+\xi'\big)=(T_\sigma V)(m).$$

**Policy improvement.** $\sigma'(m)=\arg\max_{c\in(0,m]}\big\{u(c)+\beta\,\mathbb E\,V(R(m-c)+\xi')\big\}$,
i.e. $\arg\max_c(\lambda\#V)(m,c)$ with the feasibility constraint imposed in the $\arg\max$.

**Horizon.** Value iteration is the chain
$\cdots\#\pi_{\sigma_2}\#\lambda\#\pi_{\sigma_1}\#\lambda\#V$, each $\sigma_i$ optimal for the
costate to its right; the limit is $(\sigma^*,v)$. The paper's own savings example (Example 8) is
this with $f(x,a)=(1+\gamma)x-a+\mathcal N(\mu,\sigma)$ and $U(x,a)=a$ in the Gaussian category;
your $u$ concave and $\xi'$ non-Gaussian take it out of $\mathbf{Gauss}$ and into the general
$\mathbf{Optic}_{\mathbf{Mark},\mathbf{Conv}}$, which is where the paper's §4.3–4.4 live.

---

## 5. The same period in the engine's syntax (illustrative, uncompiled)

Modelled on `Examples/Markov/NStageMarkov.hs` and `Examples/Decision.hs`. The engine's
distributions are finite-support (`Numeric.Probability.Distribution`), so the income shock is a
discretised $\nu$ and consumption a grid, as in a standard numerical solution.

    -- Parameters and primitives
    beta = 0.96; rGross = 1.03
    u c = c ** (1 - rho) / (1 - rho)
    incomeDraw = distFromList [(xi, p) | (xi, p) <- discretisedNu]       -- a finite ν
    consumptionGrid m = [c | c <- grid, c > 0, c <= m]                   -- A(m) = (0, m]
    transition m c = do xi <- incomeDraw; pure (rGross * (m - c) + xi)   -- the kernel

    -- One period of the buffer-stock problem, as an open game with one player
    bufferStockPeriod = [opengame|
        inputs    : m ;                                 -- arrival: cash observed
        feedback  :   ;
        :----------------------------:
        inputs    : m ;
        feedback  :   ;
        operation : dependentDecision "household" (\m -> consumptionGrid m) ;
        outputs   : c ;
        returns   : u c ;                               -- this period's reward, on the backward wire

        inputs    : (m, c) ;
        feedback  :   ;
        operation : liftStochasticForward (uncurry transition) ;
        outputs   : mNext ;                             -- continuation: next cash, forward
        returns   :   ;

        operation : discount "household" (\x -> x * beta) ;   -- ×β on everything that flows back
        :----------------------------:
        outputs   : mNext ;
        returns   :   ;
    |]

    -- A policy is a strategy: observe m, consume σ(m)
    policy sigma :: Kleisli Stochastic Double Double
    policy sigma = Kleisli (\m -> playDeterministically (sigma m))

    -- Finite-horizon continuation: play the period forward n times with this policy,
    -- feeding each period's payoff back (the pattern of determineContinuationPayoffs)
    continuation n strat m = ...      -- as in NStageMarkov.hs: extractContinuation / extractNextState

    -- Check: is σ unimprovable by a one-period deviation, given its own continuation value?
    isOptimal n sigma m0 = generateIsEq $ evaluate bufferStockPeriod (policy sigma :- Nil)
                               (StochasticStatefulContext (pure ((), m0)) (\_ m -> continuation n (policy sigma :- Nil) m))

Read in the paper's terms: the two LineBlocks are the optic $\lambda$ (decision then transition),
the `discount` line is the $\times\beta$ on the backward wire, `policy sigma` is $\pi_\sigma$, the
context's continuation is the costate $V$, and `evaluate` asks whether $\sigma(m)$ attains
$\max_c(\lambda\#V)(m,c)$ on the grid. What the engine does **not** do is compute $\sigma^*$: the
paper's value-iteration loop is a few lines around this, but it is not a shipped feature; Philipp
Zahn's reinforcement-learning implementation that does it is closed-source (paper §1.1).

---

## 6. How this sits beside Akshay's formulation

- **The optic is the pair (forward model, backward model) as one object.** Akshay's $D_f$ sends
  the period to the kernel $P\circ\delta_\sigma$; his $D_b$ sends it to $T_\sigma$. The composite
  optic $\pi_\sigma\#\lambda$ carries exactly these two as its forward and backward passes, and the
  passes are composed *together* when periods are composed. Akshay's slide "the two models are
  not naturally isomorphic; they are paired" describes, from the outside, the structure that
  optics have from the inside: his pairing $\langle\mu K,v\rangle=\langle\mu,K^{*}v\rangle$ is the
  relation that lets a kernel be moved from the forward pass to the backward pass, which is the
  equivalence (the coend) in the definition of an optic.
- **His backward model is the represented functor of the paper's slogan.** Value improvement
  being "a representable functor on optics", $\mathrm{Optic}(-,I)$, means: send each period's optic
  $\Lambda(e)$ to the map $V\mapsto\Lambda(e)\#V$ on costates. That map is $D_b(e)$. So one could
  define a model of a declaration as a single functor $\Lambda\colon FQ_\Sigma\to\mathbf{Optic}_{\mathbf{Mark},\mathbf{Conv}}$
  and recover $D_f$ as its forward projection and $D_b$ as $\mathrm{Optic}(\Lambda(-),I)$. *(This
  reading is mine, not in either source; it is the kind of statement a category theorist would
  want to see checked.)*
- **Stages are composites of optics**, and the stage form $U\cdot G_\sigma\cdot F$ is the backward
  pass of the composite of three optics, only the middle of which has a decision.
- **Where open games go further:** several agents and equilibrium (Example E's two decisions are
  one household, but a market of households is a parallel composite), and Bayesian updating of
  beliefs along the backward wire (Bolt–Hedges–Zahn), which Akshay's slides do not treat.
- **Where Akshay's goes further:** the syntax (signature, declaration, typing) and the results on
  stagings, the coarsest cut and the Yoneda characterisations have no counterpart in the
  open-games papers, which take the MDP as given and study the solution operators.

---

## 7. Bearing on the Bellman project

- **The engine is the closest architectural precedent to Bellman-SYM.** A `.bl` stage file and an
  `[opengame| … |]` block are the same kind of object: named inputs and outputs, a list of
  operations, and payoff equations, compiled by a preprocessor into composable two-way
  morphisms. The engine's `inputs/feedback/outputs/returns` are the project's perches and fields
  split by direction; its LineBlocks are the project's blocks; its optics are the project's
  builders paired forward and backward.
- **What it has that the project could borrow:** the one-shot-deviation *check* as a first-class
  operation. `evaluate` asks of a supplied policy "is there a profitable deviation at this state,
  given the continuation value?" A solved buffer-stock policy could be tested the same way on a
  grid, as a diagnostic independent of the solver (EGM or otherwise).
- **What it lacks for the project's purposes:** continuous controls and shocks (finite lists and
  finite-support distributions only), any solver, performance ("not yet optimized"), and Python.
  Last push to the repository: January 2025; 196 stars; MIT licence.

---

## 8. Glossary

| economics | Akshay's decks | open games / optics / engine |
|---|---|---|
| state observed at decision | field; arrival perch | observation wire $X$; `inputs` |
| control | decision symbol | `dependentDecision`; the $A$ in $X\otimes A$ |
| transition | edge under $D_f$ | forward pass; `liftStochasticForward` |
| period reward | reward on the edge in $D_b$ | backward pass $U(x,a)+\beta r$; `returns` |
| discount | $\beta_e$ | $\times\beta$ on the backward wire; `discount` |
| policy | parallel edge; section | strategy `Kleisli Stochastic x a`; optic $\pi$ |
| value function | $D_b(j)$ | costate $\binom{X}{\mathbb R}\to I$; continuation context |
| policy operator | $D_b$ of a path | optic precomposition $V\mapsto\pi\#\lambda\#V$ |
| Bellman optimality | $\bigvee_\sigma T_\sigma$ | $V=\max_a(\lambda\#V)$; `evaluate` = deviation check |
| backward induction over periods | chain; stagings | sequential composition of optics; `determineContinuationPayoffs` |
| several decision-makers | — | parallel composition $\otimes$; Nash equilibrium |

---

## 9. References

- Ghani, N., J. Hedges, V. Winschel and P. Zahn (2018). "Compositional Game Theory." *LICS '18*, 472–481; arXiv:1603.04641.
- Bolt, J., J. Hedges and P. Zahn (2023). "Bayesian Open Games." *Compositionality* 5(9); arXiv:1910.03656.
- Capucci, M., B. Gavranović, J. Hedges and E. F. Rischel (2021). "Towards Foundations of Categorical Cybernetics." ACT 2021; arXiv:2105.06332.
- Hedges, J., and R. Rodríguez Sakamoto (2023). "Value Iteration is Optic Composition." *EPTCS* 380, 417–432; arXiv:2206.04547.
- Hedges, J., and R. Rodríguez Sakamoto (2025). "Reinforcement Learning in Categorical Cybernetics." *EPTCS* 429, 270–286; arXiv:2404.02688.
- CyberCat Institute, *open-game-engine* (Haskell), github.com/CyberCat-Institute/open-game-engine; tutorial `Tutorial/TUTORIAL.md`; examples `src/Examples/Decision.hs`, `src/Examples/Markov/NStageMarkov.hs`.
- Hedges, J. (2025). "From equilibrium checking to learning with the Open Game Engine." cybercat.institute, 26 June 2025.
- Shanker, A. (2026). *Categorical types and AGI*, Sessions A–C. akshayshanker.github.io/DP-cat.
