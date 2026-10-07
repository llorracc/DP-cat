# Prompt: distill Akshay Shanker's DP-cat slides into a ≤30-slide deck for Emily Riehl's group

You are working for Christopher Carroll (economics, Johns Hopkins). Your job is to turn his
coauthor Akshay Shanker's reading-group slides on category theory and dynamic programming into a
short deck that a research category theorist, Emily Riehl, or a member of her group, can read in
one sitting, in her notation and at her level. Do the whole job: familiarize yourself with the
source and with Riehl, decide what to keep, reformulate it at her level, position it against the
literature she will think of, write the deck, build it, check it, and leave a verifiable record of
every change you made to the mathematics as stated. Work in a shell with git, node/npm, Python,
and the poppler tools (pdftotext, pdftoppm, pdfinfo); if any is missing, say so and do the
nearest thing you can.

## 0. Constraints that override everything else

1. **Your output is scaffolding, not finished notes.** Chris is preparing the distillation with
   Akshay's agreement, and neither of them wants AI-drafted text to reach Riehl's group as if it
   were finished work. Treat everything you produce as material for Chris's own text, to be
   vetted by him and by Akshay before anything goes out, and say so at the top of every file.
2. **Attribution.** The mathematics is Akshay's. Name him as its author on the title slide, link
   the original (https://akshayshanker.github.io/DP-cat/), and keep his result numbering so the
   deck can be read beside his slides. Where the deck claims novelty, claim it for the applied
   mathematics, not for the category theory, which is Akshay's own view of the work.
3. **Every reformulation is marked.** Wherever you say something in a way Akshay's slides do not,
   mark it in the deck source with an HTML comment beginning `CHECK:` that says what the slides
   say instead, and prove it in a separate verification-notes file. Never present your
   reformulation as his claim.
4. **Git hygiene.** Never commit to Akshay's repository. Work in a fork under Chris's GitHub
   account (`llorracc`) or in a folder Chris names; do not commit or push unless Chris says to.
   Keep working files (outline, notes, builds) in a gitignored folder; Akshay's `.gitignore`
   already ignores `AI/`, which is his convention for AI working material, so use
   `AI/riehl-distillation/`.
5. **Length.** At most 30 slides; aim for about 24.

## 1. Inputs

- **Source repo:** https://github.com/akshayshanker/DP-cat (public; the version this prompt was
  written against is commit `ca94dff`, 25 Sep 2026). Three Marp decks at the repo root:
  `Categorical types and AGI -1.md` (Session A, 67 slides), `-2.md` (Session B, 37 slides),
  `-3.md` (Session C, 64 slides). Marp theme `theme/econ-ark-cat.css`, a callout engine in
  `marp.config.js` (Obsidian `> [!definition]` / `> [!claim]` / `> [!callout]` blockquotes become
  styled boxes), about 70 SVG diagrams in `assets/`, and `tools/check-overflow.py`, a script that
  renders a deck and reports slides whose body runs into the footnote. Sessions B and C rest on a
  private note, `notes/Yoneda-stages/fields-and-stages.md`, which is gitignored and not available;
  Session C cites its Propositions 8, 9 and 11 without proof.
- **What the author regards as central:** the core idea is in the second deck, the free category
  of the dynamic program; the central challenge, summarized at the end of the first deck, is how
  to go from declarative first-order syntax (what economists write) to the higher-order
  functional operator a computer solves; branching is not covered, and it is where he expects
  initial diagrams to matter most.
- **Riehl's text:** *Category Theory in Context*, **second edition**, free at
  https://emilyriehl.github.io/files/context.pdf. Use this edition: Akshay's section numbers match
  it (e.g. Example 4.1.13 is the free category on a quiver, Theorem 4.2.7 the unit/counit
  characterizations) even though his slides cite "Riehl (2016), Dover".
- **Economics background,** in case you need it: the running example is the buffer-stock
  consumption–saving model (Carroll; Carroll and Shanker 2026), and the stage/factored-program
  material follows Sargent and Stachurski, *Dynamic Programming, Volume II: General States*
  (draft, 2026). Akshay's slides define everything you need.

## 2. Step one: familiarize yourself with the source

Clone the repo. Read all three decks in full, not the headings. Record: the slide-level structure
of each; which results carry numbers (Proposition 1, Lemma on the number of stagings, Propositions
4, 5, 6, Theorem 7, Propositions 8, 10, 11); which examples recur (A: buffer stock; B: a
deterministic chain with an unreached point; C: a two-decision chain; E: consumption–portfolio;
F: the `buffer_stock.bl` stage file); which SVG assets illustrate what; and how the decks are
built (`marp --no-stdin <deck> --theme-set theme/econ-ark-cat.css -o out.pdf --allow-local-files
--html`, run from the repo root so `marp.config.js` loads). Note what the decks do not cite: none
of the categorical systems theory or open-games literature (section 6 below).

## 3. Step two: familiarize yourself with Riehl, from her text, not from memory

Download the second-edition PDF, run `pdftotext -layout`, and read: the "Notational conventions"
and "Changes in this edition" sections of the preface; the Glossary of Notation; and every
passage Akshay cites (Definition 1.1.1; Example 1.1.4; Definitions 1.2.1, 1.3.11, 1.3.13, 1.4.1,
1.4.3, 1.5.7, 1.6.4, 1.6.14; Lemmas 1.6.5, 1.6.11, 1.7.4 and Remark 1.7.6; Examples 2.1.1 and
2.4.11; Theorem 2.2.4; Corollary 2.2.8; Proposition 2.3.1; Definition 2.3.3; Definitions 2.4.1–2;
Proposition 2.4.8; Example 3.6.11; Examples 4.1.13–14; Theorem 4.2.7; Example 5.1.5(iv);
Definition 5.2.10 and Example 5.2.11(iv); the Lawvere-theory exercise in §5.5; the restriction
functor $K^{*}$ in chapter 6). Build a notation table with three columns, Riehl / Akshay / what the
deck will use. Facts you should confirm there (they are true of the second edition):

- identities are `id_X` (the first edition had `1_X`); composites are juxtaposition `gf`,
  `g · f` only for typographical clarity; hom-sets `C(x, y)`; `Hom(F, G)` for natural
  transformations; categories in sans-serif (`Set`, `Cat`, `Quiver`, `Meas`, `End`);
- directed graphs are called **quivers** ("graph" is reserved for simple graphs); the free
  category is the left adjoint `F ⊣ U : Cat ⇄ Quiver` of Example 4.1.13, and functors `F(Q) → C`
  correspond to quiver maps `Q → U(C)`;
- ordinals: `𝟘, 𝟙, 𝟚, ω`; `𝕟+𝟙` is the ordinal freely generated by `0 → ⋯ → n` (Example 4.1.14,
  with cofaces `d^i`); `BG` is the one-object category of a monoid; `∗` a terminal object;
- the Yoneda embedding is the hiragana **よ** (Corollary 2.2.8); `Ψ(x)` is the transformation
  classified by an element; the category of elements is `∫F`; "universal" means initial or
  terminal in `∫F` (Proposition 2.4.8); comma categories `c ↓ G`, slices `c/C`;
- natural transformations `α : F ⇒ G`, whiskering `Jα`, `αI` with `(JαI)_b = Jα_{Ib}`
  (Remark 1.7.6); dinatural transformations are footnote 34 and Exercise 1.4.viii;
- the Kleisli category is `C_T` with arrows `A ⇝ B` (Definition 5.2.10); the Giry monad is
  `Prob` on `Meas` (Example 5.1.5(iv)); its Kleisli category "is the category of measurable spaces
  and Markov kernels" (Example 5.2.11(iv)). Consequently write `Prob(X)` for probability measures,
  never `Δ(X)` as Akshay does: `Δ` is her constant-diagram functor and the simplex category;
- functor categories `D^C`, presheaves `Set^{C^op}`; restriction along `K` is `K^*`; Kan
  extensions `Lan_K`, `Ran_K`; `≔` for definitions, `≅` for isomorphism, `≃` for equivalence,
  dashed arrows for the morphism whose existence is asserted; boldface for a term being defined.

Also read enough of her prose to reproduce the register: definitions stated once and tersely;
claims as Lemma/Proposition/Theorem with short diagram-chase proofs or "Exercise"; examples from
across mathematics; the universal property is the motivation, so no motivation speeches; "the
Yoneda lemma is arguably the most important result in category theory". Note from the preface
that she used LLMs only for LaTeX bugs and accessibility and a Lean agent to verify an example, and
that "All of the writing in the second edition remains my own": her stance on AI-written
mathematics is the same as Akshay's. Check her current programme (synthetic ∞-category theory,
homotopy type theory, formalization in Lean; ICM 2026 invited) only to calibrate the register;
none of it goes into the deck.

## 4. Step three: decide what to keep, cut and add

**Keep (Akshay's mathematics, in his numbering):** signature, declaration, the policy quiver
$Q_\Sigma$, models as functors out of $FQ_\Sigma$ (Proposition 1), the forward model into Markov
kernels and the backward model (a presheaf of value functions), the pairing
$\langle\mu K,v\rangle=\langle\mu,K^{*}v\rangle$ and its (di)naturality, chains, groupings, stages,
stagings and the counting lemma, the stage form $T_\sigma=U\cdot D_b(e,\sigma)\cdot F$,
Sargent–Stachurski's factored programs as naturality squares (Proposition 5), moving a boundary
(Proposition 4), partial transformations (Proposition 6), the coarsest cut (Theorem 7) and the
canonical stage form (Proposition 8), the backward-to-forward discussion (kernel characterization),
policies as sections (Proposition 10), rules and the stage form (Proposition 11), the slide on what
the Yoneda lemma does and does not do, and the open problems (several inputs, branching, dimension,
choice of staging). Use one running example, A (buffer stock); mention C only for the count of
stagings and E only for a non-deterministic decision edge.

**Cut, because she wrote it or it is not for her:** Session A §§2.1–2.3 (her chapter 1); §2.4,
the universal property of $(\mathbb N,s,0)$, which is her Example 2.1.1 / 2.4.11 (one-line
callback); the Pāṇini/Backus/Python-AST slides; the AGI and Mahadevan framing; "speaking
deterministically to AI"; the economic functor examples (chain rule, iterated expectations,
discounting); the proofs of Proposition 1 and Theorem 7 (one line each at her level); the stage-file
and application examples.

**Add:** a slide positioning the work in the literature a category theorist will think of
(section 6), a slide of honest open problems, a slide of questions for her, and the economics
defined once on one slide, since she may not know it.

## 5. Step four: reformulate at her level, and prove each reformulation

Make these reformulations; mark each with a `CHECK:` comment in the deck and prove it in
`verification-notes.md` (a few lines each; all are standard):

1. $Q_\Sigma$ is the pullback $Q\times_{\mathcal S}\mathcal S_\Sigma$ in $\mathsf{Quiver}$ of the
   typing $\tau\colon Q\to\mathcal S$ along the projection $\mathcal S_\Sigma\to\mathcal S$ that
   forgets the policy. (Pullbacks in a presheaf category are computed on vertices and edges.)
2. Proposition 1 is the adjunction $F\dashv U$ of Example 4.1.13; "an algebra induces a model" is
   restriction along $F(\mathrm{pr}_2)\colon FQ_\Sigma\to F\mathcal S_\Sigma$. Models form the
   functor category $\mathsf C^{FQ_\Sigma}$; in Lawvere's terms this is functorial semantics with no
   equations and no products, i.e. representations of a quiver.
3. $\mathrm{Prob}(X)=\mathsf{Stoch}(\ast,X)$, so push-forward of laws along a path is
   postcomposition: the represented functor $\mathsf{Stoch}(\ast,-)$ restricted along $D_f$.
4. With one policy fixed per decision edge, the free category of a chain is the ordinal $[N]$;
   a grouping $j_0<\cdots<j_n$ is a monomorphism $\pi\colon[n]\rightarrowtail[N]$ (a composite of
   cofaces); the grouped model is $\pi^{*}D$. Proposition 4 then has a one-line proof: a pointwise
   inequality $\pi\le\pi'$ is the unique 2-cell $\pi\Rightarrow\pi'$ between functors into a poset;
   whisker it with $D$.
5. Proposition 6's condition says $\varphi_i\,D(e,\sigma)$ coequalizes the kernel pair of the
   surjection $\varphi_j$, i.e. $D(e,\sigma)$ descends to the quotient.
6. Theorem 7: the coarsest cut is the image factorization of
   $\langle T_\sigma\rangle_\sigma\colon V\to W^{\Sigma}$ (Example 3.6.11); "coarser" is the order
   on quotients of $V$. Dually, the inclusion $U_\Sigma$ of the canonical stage form is the image
   of the copairing $[T_\sigma]_\sigma\colon\Sigma\times V\to W$, which proves the decks' unproved
   Proposition 8. In Example A the quotient $q$ is the income expectation and $\bar V$ the
   economists' post-decision value function; it is a genuine quotient when the income law has a
   density.
7. Proposition 11 by the Yoneda argument on the representable
   $(\mathsf C^{\mathrm{op}}\times\mathsf C)((c_1,c_2),-)=\mathsf C(-,c_1)\times\mathsf C(c_2,-)$ and
   its element $(\mathrm{id},\mathrm{id})$; note that the argument never needs
   $\mathsf{Set}^{\mathsf C^{\mathrm{op}}\times\mathsf C}$ to be locally small, so $\mathsf C=\mathsf{Set}$
   is admitted. Proposition 10 is Corollary 2.2.8 with $c=E$, $c'=S$.

Also fix two places where a hasty distillation goes wrong: the forward model sends **every** edge
to a kernel; only in Example A is the decision edge the deterministic kernel $\delta_\sigma$ (in
Example E the portfolio decision integrates the returns). And the backward model's affine form
$v\mapsto r+\beta K^{*}v$ is the decks' *time-separable* case; say so.

## 6. Step five: position the work against the literature she will think of

Search for, and verify by reading at least the abstracts of, the works below; cite them on one
slide and in the references. Say plainly that Akshay's decks cite none of them.

- Lawvere's functorial semantics (models as functors) and Jacobs's classifying categories
  (*Categorical Logic and Type Theory*, 1999), which Akshay does cite.
- Markov categories: Fritz (2020), *A synthetic approach to Markov kernels…*, which Akshay cites.
- Categorical systems theory: David Jaz Myers, *Categorical Systems Theory* (book draft,
  davidjaz.com/Papers/DynamicalBook.pdf; Myers did his PhD with Riehl at Johns Hopkins);
  Vagner, Spivak and Lerman, *Algebras of open dynamical systems on the operad of wiring diagrams*
  (TAC 2015); Schultz, Spivak and Vasilakopoulou, *Dynamical systems and sheaves* (Appl. Categ.
  Structures 2020; arXiv:1609.08086); Spivak, *Poly: an abundant categorical setting for
  mode-dependent dynamics* (arXiv:2005.01894); Niu and Spivak, *Polynomial Functors* (CUP 2025);
  Libkind, Baas, Patterson and Fairbanks, *Operadic modeling of dynamical systems* (ACT 2021,
  EPTCS 372) and AlgebraicDynamics.jl.
- Open games and optics: Ghani, Hedges, Winschel and Zahn, *Compositional game theory*
  (LICS 2018); Hedges and Rodríguez Sakamoto, *Value iteration is optic composition* (ACT 2022,
  arXiv:2206.04547) and *Reinforcement learning in categorical cybernetics* (EPTCS 429, 2025,
  arXiv:2404.02688); Bakirtzis, Savvas and Topcu, *Categorical semantics of compositional
  reinforcement learning* (JMLR 2025).
- Mahadevan, *Universal decisions with Kan extensions*, which Akshay cites.

State the relation in one sentence each: the systems-theory frameworks compose systems along
interfaces, whereas here the composite is fixed by the declaration and the question is which
functors out of it, and which transformations between them, carry the economics; the optics work
makes Bellman updates parametrised optics with a representable functor doing the work.

## 7. Step six: write the deck

**Format.** Marp markdown at the repo root of the fork, named
`free-category-of-a-dynamic-program.md`, with front matter `marp: true`, `theme: econ-ark-cat`,
`paginate: true`, `math: katex`, reusing Akshay's theme, callout engine and SVG assets
(`assets/fs-period-chain.svg`, `fs-triangle.svg`, `fs-three-rows.svg`, `fs-stage-sandwich.svg`,
`fs-canonical.svg`, and the staging diagrams). Slide anatomy as in his decks: a `kicker` line,
an `##` heading, boxed definitions and claims via the callout syntax, a `footnote` div for
citations. Start the file with an HTML comment giving provenance, the `CHECK` convention and the
build command; do not nest `<!-- … -->` inside that comment, which closes it early and prints the
rest on the title slide.

**Style rules.** Her notation throughout (section 3). Everything in CTiC chapters 1–6 assumed:
never define category, functor, natural transformation, representable, Yoneda. Define each new
term once, in boldface, and never again. State results as Proposition/Theorem with her citation
form "Riehl, Example 4.1.13". Proofs are one line or omitted. No motivation paragraphs; the
universal property is the motivation. Cite the second edition by number. At most about nine lines
of body text per slide at the theme's default size; wrap denser slides in `<div class="small">`.
Keep Akshay's result numbers.

**Slide plan (24 slides; adjust within 20–30).**

1. Title: *The free category of a dynamic program. Dynamic programming as functors out of a free
   category.* Mathematics: Akshay Shanker (DP-cat, 2026); distilled by C. Carroll. Link.
2. The question: first-order equations economists write versus the higher-order Bellman
   operator; a syntax tree cannot type $\mathbb T$ or carry binders. Boxed proposal: the operator is
   the image of an arrow of a free category under a functor; stages, post-decision states and
   factored programs are natural transformations, restriction along ordinal inclusions, and an
   image factorization; Yoneda says which operations are natural.
3. The economics, once: kernels, $K^{*}$, value functions $L^{X}$, policies, $T_\sigma v=r_\sigma+
   \beta K_\sigma^{*}v$, $\mathbb Tv=\sup_\sigma T_\sigma v$; boxed Example A with its Bellman
   equation.
4. Signature and declaration (definitions; $Q_\Sigma$ as pullback; Example A's types, symbols and
   chain).
5. Models: $F\dashv U$; algebra and model; $\mathsf C^{FQ_\Sigma}$; the functorial-semantics remark.
6. The forward model into $\mathsf{Stoch}=\mathsf{Meas}_{\mathrm{Prob}}$; table for Example A;
   laws as $\mathsf{Stoch}(\ast,-)$.
7. The backward model as a presheaf; the policy operator is the image of a path; the three-row
   diagram (no vertical arrows, so no equation asserted).
8. Not naturally isomorphic, but paired: the pairing, naturality / dinaturality, the affine
   case with rewards, the finite-matrix form.
9. Chains, ordinals, groupings as monomorphisms $[n]\rightarrowtail[N]$, grouped model
   $\pi^{*}D$, stage structures.
10. Runs, stages, stagings; the counting lemma; the stage form (arrival–decision–continuation);
    one wide diagram.
11. Factored programs as naturality squares; $\mathsf B\mathbb N\to\mathsf{Set}$; Proposition 5 and
    the reduced model.
12. Proposition 4 with the whiskering proof; reversed order for $D_b$; Example A.
13. Proposition 6 with the descent gloss.
14. Theorem 7 with the image-factorization proof; Example A's post-decision value function.
15. The canonical stage form; the two linking equations; the dual (Proposition 8); $q$ coarsest,
    $U_\Sigma$ finest; order preservation.
16. Forward-to-backward and back: the kernel characterization; $q$ as a backward operator on
    (policy, state) pairs; bijective but non-deterministic $K^{*}$.
17. Policies are sections; Proposition 10; expectation not natural in the value set (the
    $z\mapsto z^{2}$ example), natural for affine maps.
18. Rules; Proposition 11 with the Yoneda argument and the size remark; the stage form is the form
    of every rule.
19. What the lemma does and does not do here.
20. Where this sits (section 6), ending with Akshay's own assessment.
21. Not yet done: several inputs (products; $\mathsf{Stoch}$ has no categorical products), branching,
    dimension, choice of staging (define "rotation" where used).
22. Questions for a category theorist: sketch or finite-product theory from the start?; a clean
    statement of the backward model (Kleisli extension against a barycentre algebra, profunctor,
    extranatural) carrying rewards?; $\mathbb T=\bigvee_\sigma T_\sigma$ as a join in a
    $\mathsf{Pos}$-enriched setting?; economic meaning of $\mathrm{Lan}_\pi$, $\mathrm{Ran}_\pi$?; in
    which categories of value functions the image factorization still gives the reduction?;
    branching via tree-shaped quivers, colimits or polynomial functors?; is $\int D_b$ useful?
23–24. References (Riehl 2nd ed.; Fritz; Sargent–Stachurski; Goguen–Thatcher–Wagner–Wright 1977;
    Jacobs; Backus 1978; Lawvere 1963; the section-6 works; Shanker's decks and private notes;
    Carroll–Shanker 2026).

## 8. Step seven: build, check, look, fix

Install Marp (`npm install -g @marp-team/marp-cli`; PDF export needs an installed Chrome). From
the repo root run

    marp --no-stdin free-category-of-a-dynamic-program.md --theme-set theme/econ-ark-cat.css \
         -o AI/riehl-distillation/build/deck.pdf --allow-local-files --html

and the same with `-o …/deck.html`. Run Akshay's overflow checker (it needs numpy and pillow;
make a venv):

    python tools/check-overflow.py free-category-of-a-dynamic-program.md theme/econ-ark-cat.css \
           AI/riehl-distillation/build/overflow

Require **0 overflows**. Then render pages with `pdftoppm -r 72 -png` and actually look at a
third of them, including the title slide, a definition slide, a two-column slide, the theorem
slide and the references: check that callout boxes rendered, that KaTeX symbols such as `≔`,
`↣`, `⇝`, `よ` rendered (keep よ out of math mode), that diagrams are legible (a wide SVG in a
narrow column is not), and that nothing printed that should be a comment. Fix and rebuild until
clean.

## 9. Step eight: deliverables and record

In `AI/riehl-distillation/` of the fork clone (gitignored):

- `outline.md`: the notation table, the keep/cut/add decisions, the slide plan, and a status line
  (ACTIVE / EXECUTED, with date and what remains).
- `verification-notes.md`: a proof of each `CHECK` item, a list of what was checked against the
  decks without reformulation, and a list of what still needs a human (the positioning slide and
  the questions slide are yours, not Akshay's).
- `build/`: `deck.pdf`, `deck.html`.

At the repo root, untracked until Chris says otherwise: `free-category-of-a-dynamic-program.md`.

Finish with a short report to Chris: what you produced and where; the facts about Riehl that bear
on the deck (second edition, notation changes, her AI stance); the list of `CHECK` items; what you
cut; and the three decisions only he can make: how the draft becomes his own text (your
recommendation: he rewrites from your scaffolding, credits Akshay, and tells Akshay an AI drafted
the skeleton), whether to commit to the fork, and when Akshay sees the draft. Do not send
anything to anyone.

## 10. Acceptance checks

- ≤ 30 slides; builds; 0 overflows; every rendered page inspected or spot-checked as above.
- No definition of a concept in CTiC chapters 1–6; every new term defined exactly once.
- Riehl's notation throughout; `Prob` not `Δ`; `id` not `1`; `quiver` not `graph`; citations to
  the second edition by number, and each cited number verified against the PDF.
- Every `CHECK` item has a proof in the notes; the forward-model and time-separable corrections
  of section 5 are in place.
- The positioning slide cites the works of section 6 and says the decks do not.
- Title slide credits Akshay and links the original; every file states that it is scaffolding
  to be vetted by Chris and Akshay before it goes anywhere.
- Nothing committed to Akshay's repo; nothing pushed; nothing sent.
