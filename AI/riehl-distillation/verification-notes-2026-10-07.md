# Verification notes for the `CHECK` items in `free-category-of-a-dynamic-program.md`

**Status:** written 2026-10-07 by Claude for Chris Carroll. Each item below is a reformulation that
appears in the deck but not in Akshay Shanker's decks. The proofs are short and standard; they are
recorded so that Chris or Akshay can confirm each in a minute. Nothing here changes what the decks
claim; it changes how it is said.

Deck slide numbers refer to the 24-page build of 2026-10-07. "Riehl" is *Category Theory in Context*,
second edition.

---

## 1. $Q_\Sigma$ is the pullback $Q\times_{\mathcal S}\mathcal S_\Sigma$ (slide 4)

Decks: $Q_\Sigma$ has the fields as vertices, one edge $(e,\sigma)$ for each decision edge $e$ and
each policy $\sigma\in\Sigma_{\tau(e)}$, and one edge $(e,\ast)$ for each policy-free edge.

Proof. $\mathsf{Quiver}$ is a presheaf category, so pullbacks are computed on vertices and on edges
separately. Vertices of $Q\times_{\mathcal S}\mathcal S_\Sigma$: pairs $(j,\mathtt X)$ with
$\tau(j)=\mathtt X$, i.e. the fields. Edges: pairs $(e,\lambda)$ with $\tau(e)=p(\lambda)$; if
$\tau(e)=\mathtt s$ is a decision symbol the $\lambda$ with $p(\lambda)=\mathtt s$ are the
$(\mathtt s,\sigma)$, $\sigma\in\Sigma_{\mathtt s}$; otherwise the only one is $(\mathtt s,\ast)$.
Sources and targets agree because $\tau$ and $p$ are quiver maps. $\square$

## 2. Proposition 1 is the adjunction $F\dashv U$; an algebra induces a model by restriction (slide 5)

Decks: Proposition 1 says every interpretation $(\mathsf C,\chi)$ of $Q$ extends uniquely to a functor
$FQ\to\mathsf C$; "an algebra induces a model, in which the field $j$ gets the carrier $|\tau(j)|$ and
the edge $(e,\sigma)$ gets $|(\tau(e),\sigma)|$".

Proof. Riehl, Example 4.1.13: functors $F(Q)\to\mathsf C$ correspond to quiver maps $Q\to U(\mathsf C)$,
which is the statement of Proposition 1 (the decks' own proof is the explicit construction, which is
what one uses when $\mathsf C$ is large). The pullback projection $\mathrm{pr}_2\colon Q_\Sigma\to
\mathcal S_\Sigma$ sends $j\mapsto\tau(j)$ and $(e,\sigma)\mapsto(\tau(e),\sigma)$, so for an algebra
$A\colon F\mathcal S_\Sigma\to\mathsf C$ the composite $A\circ F(\mathrm{pr}_2)$ assigns exactly the
carriers and arrows the decks prescribe. $\square$

## 3. $\mathrm{Prob}(X)=\mathsf{Stoch}(\ast,X)$ and push-forward is postcomposition (slide 6)

Proof. A Kleisli arrow $\ast\rightsquigarrow X$ is a measurable map $\ast\to\mathrm{Prob}(X)$, i.e. a
probability measure $\mu$ on $X$. Kleisli composition of $\mu$ with $K\colon X\rightsquigarrow X'$ is
$B\mapsto\int\mu(dx)\,K(x,B)=(\mu K)(B)$, the decks' push-forward. So $\mu\mapsto\mu K$ is the action of
the represented functor $\mathsf{Stoch}(\ast,-)$ on $K$. $\square$

## 4. Groupings are monomorphisms of ordinals; Proposition 4 is whiskering (slides 9, 12)

Decks: a grouping is an increasing sequence $j_0<\cdots<j_n$ of positions; the grouped quiver $Q'$ has
the boundaries as vertices and the paths between consecutive boundaries as edges; the grouped model is
$D\circ\pi$ for $\pi\colon FQ'\to FQ_\Sigma$. Proposition 4: if $j_i\le j'_i$ for all $i$ the arrows
$D(j_i\to j'_i)$ are the components of a natural transformation $D_\pi\Rightarrow D_{\pi'}$.

Proof. With one policy fixed at each decision edge, the free category of the chain is the ordinal
$[N]$ (one arrow $j\to j'$ iff $j\le j'$; Riehl, Example 4.1.14). An increasing sequence of $n+1$
positions is the same as an injective order-preserving map $\pi\colon[n]\to[N]$, i.e. a monomorphism in
$\Delta$, i.e. a composite of cofaces $d^i$. The edge $i-1\to i$ of $[n]$ goes to the unique arrow
$j_{i-1}\to j_i$ of $[N]$, so $D\pi$ assigns to it $D(j_{i-1}\to j_i)$: this is the decks' grouped model.
For Proposition 4: between functors $\pi,\pi'\colon[n]\to[N]$ into a poset, a natural transformation
exists iff $\pi(i)\le\pi'(i)$ for every $i$, its components are the unique arrows $\pi(i)\to\pi'(i)$,
and every naturality square commutes because parallel arrows in a poset are equal. Whiskering with $D$
(Riehl, Remark 1.7.6: $(D\alpha)_i=D(\alpha_i)$) gives $D\pi\Rightarrow D\pi'$ with components
$D(\pi i\to\pi' i)$. If $\pi(i)>\pi'(i)$ for some $i$ there is no arrow $\pi(i)\to\pi'(i)$, so the
family is not defined. For $D_b$, a functor on the reversed chain, the order is reversed. $\square$

## 5. Proposition 6's condition is descent along a quotient (slide 13)

Proof. In $\mathsf{Set}$ a surjection $\varphi_j\colon D(j)\twoheadrightarrow B_j$ is the coequalizer of
its kernel pair $D(j)\times_{B_j}D(j)\rightrightarrows D(j)$. A map $g$ out of $D(j)$ factors through
$\varphi_j$ iff it coequalizes the kernel pair, i.e. iff $\varphi_j(v)=\varphi_j(v')$ implies
$g(v)=g(v')$. With $g=\varphi_i\,D(e,\sigma)$ this is the decks' condition, and the induced map is
$D'(e,\sigma)$ with $D'(e,\sigma)\varphi_j=\varphi_iD(e,\sigma)$, unique because $\varphi_j$ is onto.
$D'$ is a functor out of $FQ_\Sigma$ because a quiver imposes no equations (Proposition 1). $\square$

## 6. Theorem 7 is an image factorization; $U_\Sigma$ is the image of the copairing (slides 14, 15)

Proof of Theorem 7. Let $\langle T\rangle\colon V\to W^{\Sigma}$, $v\mapsto(T_\sigma v)_\sigma$. Its
image factorization is $V\twoheadrightarrow\langle T\rangle(V)\rightarrowtail W^{\Sigma}$, and
$\langle T\rangle(v)=\langle T\rangle(v')$ iff $T_\sigma v=T_\sigma v'$ for all $\sigma$ iff $v\sim v'$;
so $\langle T\rangle(V)\cong V/{\sim}=\bar V$ with $q$ the quotient map, and $\bar G_\sigma$ is the
$\sigma$-th projection restricted to the image. A cut $(\hat V,F,G_\sigma)$ gives
$\langle T\rangle=\langle G\rangle\circ F$ with $\langle G\rangle\colon\hat V\to W^{\Sigma}$. Hence
$Fv=Fv'$ implies $v\sim v'$, so $\omega_F(Fv)\coloneqq[v]$ is well defined on $F(V)$, satisfies
$q=\omega_FF$, is unique because every point of $F(V)$ is some $Fv$, is onto because $q$ is, and
$\bar G_\sigma\omega_F(Fv)=T_\sigma v=G_\sigma(Fv)$. Uniqueness of the coarsest cut up to a unique
bijection is as in the decks. $\square$

Proof of the dual statement (decks' Proposition 8). $W_\Sigma=\bigcup_\sigma T_\sigma(V)$ is the image of
the copairing $[T]\colon\Sigma\times V\to W$, $(\sigma,v)\mapsto T_\sigma v$. If $T_\sigma=U\tilde G_\sigma$
with $U\colon\tilde V\to W$ injective, then $[T]=U\circ[\tilde G]$, so $W_\Sigma\subseteq U(\tilde V)$ and
$\omega_U\coloneqq U^{-1}|_{W_\Sigma}$ is the unique map with $U\omega_U=U_\Sigma$. $\square$

## 7. Proposition 11 by the Yoneda argument, with the size remark (slide 18)

Proof. Let $R\coloneqq(\mathsf C^{\mathrm{op}}\times\mathsf C)((c_1,c_2),-)$, so $R(x,y)=\mathsf C(x,c_1)
\times\mathsf C(c_2,y)$ and $R(c_0,c_3)=\mathsf C(c_0,c_1)\times\mathsf C(c_2,c_3)$. Given a rule $\alpha$,
put $(F,U)\coloneqq\alpha_R(\mathrm{id}_{c_1},\mathrm{id}_{c_2})\in R(c_0,c_3)$. For any bifunctor $P$ and
$x\in P(c_1,c_2)$, let $\kappa_x\colon R\Rightarrow P$ be the transformation classified by $x$
(Riehl, Theorem 2.2.4: $\kappa_x(f,g)=P(f,g)(x)$, so $\kappa_x(\mathrm{id},\mathrm{id})=x$). Naturality of
the rule at $\kappa_x$ gives $\alpha_P(x)=\alpha_P(\kappa_x(\mathrm{id},\mathrm{id}))=\kappa_x(\alpha_R
(\mathrm{id},\mathrm{id}))=\kappa_x(F,U)=P(F,U)(x)$. Uniqueness: $(F,U)$ is forced to be
$\alpha_R(\mathrm{id},\mathrm{id})$. The argument quantifies over natural transformations
$R\Rightarrow P$, which form a set by the Yoneda lemma, and never over all transformations between the
evaluation functors on $\mathsf{Set}^{\mathsf C^{\mathrm{op}}\times\mathsf C}$, so it does not need that
category to be locally small; $\mathsf C=\mathsf{Set}$ is admitted. $\square$

## 8. Checked against the decks, not reformulated

- Example C (Session C, "a two-decision chain"): the run between its two decisions has two edges, so
  there are three stagings, with the interior boundary at $a$, $k'$ or $m'$. Slide 10's footnote is
  correct.
- Example B (Session C): a deterministic chain $x_1\to x_2\to x_3$ with an unreached point, used to
  compute the coarsest admissible component of Proposition 6. Slide 13's footnote is correct.
- Example F (Session C, the stage file `buffer_stock.bl`): its arrival map is linear and positive but
  not unital ($U1\ne1$, from the permanent-income weight), so it is not the backward operator of a
  kernel; and it is not injective when $\sigma_\psi>0$. Slide 16 item 2 is consistent with this.
- Forward model: the decks send each edge to a kernel; only in Example A is the decision edge
  deterministic ($\delta_\sigma$). Slide 6 was corrected on 2026-10-07 to say so.
- Backward model: the affine form $v\mapsto r+\beta K^{*}v$ is the decks' "time-separable case".
  Slide 7 now says so.

## 9. Still to be checked by a human

- The positioning on slide 20 (how this relates to Myers/Spivak systems theory and to open games) is
  mine, from abstracts and first pages, not from reading those works in full.
- The questions on slide 22 are mine; items 3, 4 and 7 are not raised in the decks.
- Whether to keep the economics-first ordering (slides 2–3) or open with the signature; Riehl's own
  habit is definition first, examples after.
