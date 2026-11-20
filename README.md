# Language Dynamics: Simulating and Formalizing Language Death, Multilingualism, and Language Formation
 
Version 1, 2026-11-20
 
## 1. Motivation
 
Roughly half of the world's approximately 7,000 languages are projected to lose their last native speakers within a century. Each loss follows a recognizable pattern: a prestige language enters a community, a bilingual generation appears, and the next generation is raised in the prestige language alone. The same contact that kills languages also produces stable multilingual societies in some places and entirely new languages (pidgins, creoles, diverged dialects) in others.
 
This project asks what conditions separate those three outcomes. The goals are to show:
 
1. **How multilingualism arises and when it persists** rather than serving as a one-generation bridge to monolingualism.
2. **How languages die**, and which levers (status, transmission in the home, geography, population size) change the outcome or only delay it.
3. **How new languages form** through isolation and drift, or through contact and mixing.
The project builds intuition through simulation first, then extracts the mathematics that explains what the simulation shows.
 
*[Personal motivation: add a short statement here.]*
 
## 2. Research Questions
 
| # | Question | Primary observable |
|---|----------|--------------------|
| Q1 | Under what conditions does a minority language go extinct, and how long does it take? | Extinction probability, time to extinction |
| Q2 | When is bilingualism a stable state rather than a transient? | Long-run bilingual fraction |
| Q3 | Which interventions change the outcome, and which only delay it? | Shift in fixed points versus shift in time scale |
| Q4 | When does one language split into two, or two merge into a third? | Number of mutually unintelligible clusters over time |
 
## 3. Key Design Decision
 
**Recommendation: build two models in sequence, not one.**
 
Death and multilingualism can be modeled with languages as fixed labels (an agent speaks A, B, or both). Language formation cannot: a new language can only appear if languages are objects that change. A single model covering all three from the start will be hard to validate and hard to formalize.
 
- **Model I (fixed languages):** answers Q1 to Q3. It has known analytical limits (Abrams-Strogatz), so the simulation can be checked against theory.
- **Model II (evolving languages):** answers Q4 and reproduces Q1 to Q3 as emergent behavior. Each agent carries an idiolect; a "language" is a cluster of mutually intelligible idiolects.
Trade-off: Model II is the more ambitious contribution but yields weaker closed-form results. Model I is well studied, so its value is as a validated foundation rather than as a novel result.
 
## 4. Part 1: Simulation
 
### Step 1. Define terms and observables
- Fix a definition of death. Recommended: track both *extinction* (zero speakers) and *moribundity* (zero child acquirers), since the second precedes the first by a generation.
- Observables: speaker fractions over time, bilingual fraction, time to extinction, spatial distribution, and (Model II) cluster count.
### Step 2. Reproduce the baseline (Abrams-Strogatz)
- Agents hold state $X$ or $Y$. An agent switches to the other language with probability proportional to that language's speaker fraction raised to a power $a$, times its status $s$.
- Confirm the simulated trajectory converges to the known ODE as population size $N$ grows. This is the correctness test for the engine.
### Step 3. Add bilinguals (Model I core)
- States $X$, $Y$, and $Z$ (bilingual). Monolinguals cannot switch directly; they must pass through $Z$.
- Separate **horizontal transmission** (adults acquiring a language through exposure) from **vertical transmission** (children inheriting languages from parents). Home transmission is the mechanism through which real languages die.
### Step 4. Add demography and structure
- Births, deaths, and generational turnover.
- Social network or spatial grid in place of full mixing.
- Migration between regions, with different status values per region.
- Optional: interlinguistic similarity parameter $k$ (similar languages make bilingualism cheaper).
### Step 5. Run the experiments for Q1 to Q3
 
| Experiment | Varied condition | Expected finding to test |
|------------|------------------|--------------------------|
| E1 | Status $s$ and initial minority share | Threshold below which extinction is certain |
| E2 | Vertical transmission rate from bilingual parents | Small drops in home transmission dominate all other levers |
| E3 | Geography and network clustering | Segregation sustains coexistence that full mixing destroys |
| E4 | Similarity $k$ | Stable bilingualism appears above a critical similarity |
| E5 | Intervention timing (status boost, schooling) | Late interventions delay but do not reverse |
| E6 | Population size $N$ | Small communities die from random fluctuation alone |
 
### Step 6. Build Model II (evolving languages)
- Each agent holds a feature vector $v_i \in \{0,1\}^F$ (lexicon or grammar features).
- Intelligibility between agents: $I_{ij} = 1 - d_H(v_i, v_j)/F$, with successful communication if $I_{ij} \geq \theta$.
- Dynamics: random mutation of features (innovation), copying from conversation partners (accommodation), inheritance from parents with error.
- A language is a connected cluster under the threshold $\theta$. Births, deaths, and bilingualism are then measured, not assumed.
### Step 7. Run the experiments for Q4
- **Divergence:** split one population with a migration barrier; measure time until the two halves fall below $\theta$.
- **Contact:** bring two unintelligible populations together; identify conditions under which a mixed third cluster forms (creolization) rather than one side absorbing the other.
### Step 8. Calibrate against data (optional but recommended)
- Candidate series: census data for Welsh, Scottish Gaelic, Irish, Quechua; three-generation heritage-language shift among immigrant communities.
- Sources: UNESCO Atlas of the World's Languages in Danger, Ethnologue, Glottolog, national censuses.
## 5. Part 2: Formalization
 
### Step 9. State the model as a stochastic process
- Model I under full mixing is a continuous-time Markov chain on counts $(N_X, N_Y, N_Z)$ with $N_X + N_Y + N_Z = N$. Write the transition rates explicitly.
- Identify absorbing states (all-$X$, all-$Y$). With finite $N$ and no inflow, absorption occurs with probability 1; the meaningful questions are *which* state and *how long*.
### Step 10. Derive the mean-field limit
- By the law of large numbers for density-dependent Markov chains (Kurtz), as $N \to \infty$ the fractions follow an ODE. For the two-state case this recovers Abrams-Strogatz:
$$\frac{dx}{dt} = c\left[(1-x)\,x^{a}\,s - x\,(1-x)^{a}\,(1-s)\right]$$
 
- Fixed points: $x = 0$, $x = 1$, and the interior point
$$x^{*} = \left[1 + \left(\frac{s}{1-s}\right)^{\frac{1}{a-1}}\right]^{-1}$$
 
- For $a > 1$ the interior point is unstable and one language always dies. For $a < 1$ it is stable and both coexist. The empirical fit $a \approx 1.3$ is why the original paper predicts death.
### Step 11. Analyze the bilingual system
- One standard three-state form, with $b = 1 - x - y$ the bilingual fraction:
$$\frac{dx}{dt} = c\left[s\,(1-y)^{a}\,b - (1-s)\,y^{a}\,x\right], \qquad \frac{dy}{dt} = c\left[(1-s)\,(1-x)^{a}\,b - s\,x^{a}\,y\right]$$
 
- Tasks: locate fixed points on the simplex, compute the Jacobian, classify stability, and draw the bifurcation diagram in $(s, a)$ and, with similarity added, in $k$.
- Target result: explicit conditions under which a fixed point with $b^{*} > 0$ is stable (Q2).
### Step 12. Quantify finite-population effects
- Diffusion approximation: $dx = \mu(x)\,dt + \sqrt{\sigma^{2}(x)/N}\,dW_t$.
- Mean time to extinction $T(x)$ from the backward equation:
$$\mu(x)\,T'(x) + \frac{\sigma^{2}(x)}{2N}\,T''(x) = -1$$
 
- Target results: extinction probability as a function of initial share; extinction time scaling in $N$ (logarithmic when the deterministic flow drives extinction, exponential when coexistence is deterministically stable). This explains E6 and separates "delays" from "reverses" in Q3.
### Step 13. Add space
- Reaction-diffusion form: $\partial_t u = D\,\nabla^{2}u + F(u)$. Language replacement becomes a traveling front; derive front speed and conditions for a pinned (stationary) boundary.
- On networks: pair approximation as the first correction to mean field.
### Step 14. Formalize language formation
- Treat each feature as a neutral allele under mutation, copying, and drift (Moran or Wright-Fisher analog).
- Target results: expected intelligibility between two isolated populations as a function of separation time $t$, mutation rate $\mu$, and population size; the critical migration rate below which a split occurs; conditions for a mixed cluster under contact.
- Expect partial results here. State clearly which claims are proven, which are heuristic, and which are simulation-only.
### Step 15. Close the loop
- Overlay theoretical predictions on simulation output for every experiment.
- Verify that simulation error shrinks at rate $N^{-1/2}$.
- Document where theory fails (strong network structure, small $N$) as findings.
## 6. Deliverables
 
1. Simulation codebase (Python recommended: `numpy`, `networkx`, `matplotlib`; optionally `mesa` for the agent framework).
2. Experiment notebook with one figure per experiment in Sections 4 and 5.
3. Mathematical write-up (LaTeX): model definition, derivations, stability results, extinction-time results, formation results.
4. Short non-technical summary tied to the motivation in Section 1.
## 7. Risks and Trade-offs
 
- **Scope.** Model II can absorb unlimited time. Complete Steps 1 to 5 and 9 to 12 before starting it; those alone form a finished project.
- **Parameter proliferation.** Each added mechanism adds parameters. Add one mechanism per step and rerun the baseline check.
- **Realism versus tractability.** Every feature that improves realism (networks, age structure, evolving lexicons) weakens the closed-form results. Decide per step which side matters more.
- **Novelty.** Steps 2, 3, 10, and 11 reproduce published work. Original contributions are most likely in Steps 12 and 14 and in the unified treatment under Model II.
## 8. Core References
 
Citations are listed from memory; verify details before citing.
 
- Abrams, D. M., and Strogatz, S. H. (2003). Modelling the dynamics of language death. *Nature* 424, 900.
- Patriarca, M., and Leppänen, T. (2004). Modeling language competition. *Physica A* 338.
- Mira, J., and Paredes, Á. (2005). Interlinguistic similarity and language death dynamics. *Europhysics Letters* 69.
- Castelló, X., Eguíluz, V. M., and San Miguel, M. (2006). Ordering dynamics with two non-excluding options: bilingualism in language competition. *New Journal of Physics* 8.
- Minett, J. W., and Wang, W. S.-Y. (2008). Modelling endangered languages: the effects of bilingualism and social structure. *Lingua* 118.
- Kandler, A., Unger, R., and Steele, J. (2010). Language shift, bilingualism and the future of Britain's Celtic languages. *Phil. Trans. R. Soc. B* 365.
- Schulze, C., and Stauffer, D. (2005). Monte Carlo simulation of the rise and the fall of languages. *Int. J. Mod. Phys. C* 16.
- Nowak, M. A., Komarova, N. L., and Niyogi, P. (2002). Computational and evolutionary aspects of language. *Nature* 417.
- Baronchelli, A., et al. (2006). Sharp transition towards shared vocabularies in multi-agent systems. *J. Stat. Mech.*
- Castellano, C., Fortunato, S., and Loreto, V. (2009). Statistical physics of social dynamics. *Rev. Mod. Phys.* 81.
- Kurtz, T. G. (1970). Solutions of ordinary differential equations as limits of pure jump Markov processes. *J. Appl. Probab.* 7.
 
