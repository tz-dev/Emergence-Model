# An Emergence Model for a Developmental AI Child

**Version:** v1.6 (Concept Paper)  
**Authors:** Till Zöller, ChatGPT (GPT-5 Thinking), Grok

## Abstract (Summary)

We present an embodied AI development model whose competencies (perception, motivation, language, planning, social behavior) are not pre-scripted but emerge from simple, **trivial building blocks**: threshold conditions with hysteresis, few needs, stochastic noise, an **Success Log** as a meta-cognitive amplifier, a **minimal imagination space** (Imagery Buffer), and **dream phases** for consolidation. The approach combines elements of predictive processing (coherence/prediction-error), embodiment, endogenous motivation, and symbolic **exaptation** (word → category → action). We provide a precise architecture (environment + agent), define core mechanisms (gates, mood OU-process, risk-weighted learning bonus, satiation/cooldown, forgetting & reactivation, plan commitment with reality-checks), describe evaluation KPIs, phase protocols (P0–P5), and reproducibility requirements. The goal is an **implementable, testable** guide that can generate functional aspects of self-efficacy, volition, protolinguistic grammar, and planning in an **emergent** manner.

**Keywords:** Embodied AI, Emergence, Hysteresis, Prediction Error, Imagery Buffer, Success Log, Minimal Rules, Exaptation, Self-Efficacy

---

## Table of Contents (Short)

1. Introduction & Motivation  
2. Related Work (Brief Classification)  
3. Architecture Overview  
4. Virtual Environment  
5. Agent: Components  
   5.1 Genetic System (Gates, Hysteresis, Imprinting)  
   5.2 Cognition System (Map, Needs, Learning, Imagination Space, FSM)  
   5.3 Language System (Word↔Image, Thought Channel, Proto-Sentences)  
   5.4 Meta-Layer Success Log  
   5.5 Control & Logging System  
6. Core Mechanisms (Formal)  
7. Social Phase & Safeguards  
8. Emergence Dynamics (What is Expected)  
9. Evaluation & Measurability (KPIs)  
10. Experimental Phase Protocol (P0–P5)  
11. Implementation Details & Reproducibility  
12. Limits, Risks & Ethics  
13. Conclusion

---

## Executive Summary (1-Pager)

* **Guiding Idea:** Little, clean kernel → many **nonlinear** interactions → emergence.  
* **No External Reward:** Instead, **Success Log** (endogenous, multidimensional) + **Prediction-Error** (Coherence).  
* **Language as Exaptation:** Words start as labels → become categories → drive planning & motivation.  
* **Imagination Space:** Small 2D buffer + topology graph (multi-scale) → from still image through single-step probe to multi-step planning.  
* **Stability through Thresholds with Hysteresis**, mood as OU-process, risk-weighted learning bonus, satiation/cooldown, forgetting & reactivation, plan commitment with reality-checks.  
* **Measurability:** Specific KPIs (map entropy, PE-AUC, volition ratio, plan success, language F1, obsession guard, external→internal-drive ratio).  
* **Implementability:** Trivial: FSM, grids, counters; robust phase approach P0→P5.

---

# 1. Introduction & Motivation

The goal is a **developing** AI child whose abilities are not determined by large model weights or rigid rule sets, but **gradually** emerge through interaction, conflicts, thresholds, and consolidation. The model combines:

* **Embodiment:** real (simulated) perception & action,  
* **endogenous motivation:** needs instead of external reward,  
* **predictive control:** learning via prediction-error (coherence),  
* **language exaptation:** language as a tool for categories, planning, will.

The result is a **simple to implement** (but dynamically rich) framework that can change MSM’s projection.

---

# 2. Related Work (Brief Classification)

* **GOFAI:** Symbolism/rules → no emergence, no embodiment.  
* **Connectionism:** distributed patterns, but hardly endogenous motivation/will formation.  
* **Reinforcement Learning:** extrinsic reward → reward-hacking; language/will not native.  
* **Predictive Coding:** minimizes prediction-error; in us concretized as **coherence** and **risk-weighted learning bonus**.  
* **World Models:** internal simulation; in us as **light imagination space** without complex generative nets.  
* **Embodied Robotics:** embodied, but language/autonomy mostly hard-coded.  
  New is the **combination**: trivial rule kernel + success log + imagery + language exaptation + social safeguards.

---

# 3. Architecture Overview

Environment (Godot or similar): rooms, doors, caregiver, objects; day/night; raycasts; random events.  
Agent (Python) with five layers:

1. **Genetic system:** gates (thresholds + hysteresis), noise, early phase imprinting.  
2. **Cognition:** map, categorization, needs, learning, **imagination space** (2D + topology), FSM.  
3. **Language:** word↔image, bidirectional thought channel, grammar markers, proto-sentences.  
4. **Meta (success log):** endogenous amplifier (success/failure/neutral), diversity bonus, decay.  
5. **Control & logging:** pausing, parameters, visualization, KPIs, seeds/checkpoints.

---

# 4. Virtual Environment

* **Geometry:** 2–3 rooms, color differentiated, doors as topology nodes.  
* **Time:** 60s day / 30s night; sleep→dream/consolidation.  
* **Sensory:** 24–36 raycasts (360°); **adaptive scanning** (fine sweeps at uncertainty, stochastic microsaccades, pitch mini-scan).  
* **Random Events:** object displacement, light change, noises (stimulus for coherence tests).

---

# 5. Agent: Components

## 5.1 Genetic System

**Function:** determines **unlocks** (movement, interaction, language, planning) per **gates**.

* **Noise:** Gaussian noise ε ∼ N(0,0.05)
* **Hysteresis:** thresholds as band T=[T_low,T_high], checked over **EWMA** + minimum **dwell time** τ + **refractory time** τ_ref.  
* **Early Phase Imprinting:** P0–P1 parameters (e.g., ε, w_proximity) act as **bias** in later phases → enables **developmental asymmetries**.  
* **Caregiver Proximity (P0–P1):** counts as success (carried→mapping) → w_proximity decreases over time → **autonomization**.  

**Gates (Examples):**

* **Movement:** Map-Coverage ≥90% ±ε **and** Stability (entropy↓, revisit↑).  
* **Language:** X=5 repetitions (word+object, ±Δt).  
* **Thought Channel:** Y=10 **word-image** couplings.  
* **Planning/Imagination Space:** Z=20 **success logs**.

## 5.2 Cognition System

* **Map & Coverage:** occupancy grid + **stability measures** (entropy, revisit, angle diversity).  
* **Needs:** exploration, proximity, coherence, language, fatigue.  
* **Mood:** OU-process → gain on utility/exploration, only ±2–3% on gates.  
* **Risk-Score:** distance to wall/unknown dampens learning bonus.  
* **Satiation/Cooldown:** per object satiation[id]∈[0,1] → prevents obsession/grinding.  
* **Forgetting & Reactivation:** non-core memories degrade without repetition, reactivate on new contact (visual or linguistic); dream replay (70/30) stabilizes.  
* **Imagination Space (Imagery Buffer):** 2D + topology graph (multi-scale) → from still image through single-step probe to multi-step planning.  
* **FSM (Control):** states LOOK_AROUND, IDLE_ENCODE, SEEK_CAREGIVER, CRY, SLEEP; later PLAY, EXPLORE_ROOM, INTERACT_OBJECT, SPEAK_WORD, THROW_BALL, THINK_WORD.  

## 5.3 Language System

* **Input:** caregiver names from P0; from P3 grammar markers (the ball, the door).  
* **Processing:** word track ↔ image track (**bidirectional thought channel**).  
* **Learning:** only with **double evidence** (word+object, ±Δt); **negative samples** avoid false associations; caregiver confirmation = **strong label**.  
* **Dream:** word-image replay; from P4 proto-sentences (sequences) → stabilize grammar markers.  
* **Exaptation:** words successively become **attributes/categories** and **levers** for planning/will.

## 5.4 Meta-Layer Success Log

* **Entries:** time, target, action, result, type (success/failure/neutral), mood.  
* **Effect:** success → motivation↑, thresholds↓; failure → anxiety↑, alternative paths; neutral stabilizes.  
* **Diversity Bonus & Diminishing Returns** (per goal_id) + **Score Decay** (temporal decay).  
* **Extrinsic→Intrinsic:** caregiver praise contributes less over time (α_t ↓), self-signal more → promotes **self-efficacy without dependence**.

## 5.5 Control & Logging System

* **Visualization:** map, needs, anxiety, language, spontaneity, imagination space.  
* **KPIs:** see section 9.  
* **Reproducibility:** seed, config hash, "genome" version (slow weights), checkpoints (fast/slow/memory).

---

# 6. Core Mechanisms (Formal)

**EWMA Smoothing** for Gate Metrics

\[
\text{EWMA}_t = \alpha \cdot x_t + (1-\alpha)\cdot \text{EWMA}_{t-1}, \quad \alpha \in (0,1]
\]

**Hysteresis & Dwell**

Gate active if EWMA ≥ T_high for ≥ τ; withdrawal only at EWMA ≤ T_low.

**Mood as OU-Process**

\[
dM_t = \theta(\mu - M_t)dt + \sigma dW_t \Rightarrow M_t \in [-1,1]
\]

Acts as gain on utility/exploration; on gates only ±2–3%.

**Prediction-Error (PE) & Risk-Weighted Bonus**

\[
\text{PE} = \lVert \hat{s} - s \rVert; \quad \text{bonus} = \text{PE}\cdot(1-\text{risk}), \; \text{risk}\in[0,1]
\]

Persistent high PE without reduction → penalty instead of bonus.

**Satiation/Cooldown**

\[
\text{satiation}_{t+1}[id] = \min(1,\text{satiation}_t[id]+\eta);\quad \text{Motiv}= \text{base}\cdot\text{novelty}\cdot(1-\text{satiation}[id])
\]

Night/dream: partial reset; hard-guard: max. n_consecutive interactions/object.

**Forgetting & Reactivation**

Non-core memories: value ↓ with time constant τ_f; sight/naming reactivates ↑; dream replay (70/30) stabilizes.

**Praise-Internalization**

\[
\alpha_t = e^{-\lambda \cdot \text{tage}},\quad \text{weight}_t = \alpha_t\cdot \text{praise} + (1-\alpha_t)\cdot \text{self}
\]

→ External→Internal-Drive shifts motivation from praise to self-signal.

**Pre- / Post-Reality-Check**

Pre: Alignment A≥θ_pre; Post: ΔPE<0 & Log-Update.

---

# 7. Social Phase & Safeguards (P5)

* **Fair-Share-Rules:** objects not permanently blocked.  
* **Reputation (with Decay Time):** interaction history influences cooperation/sharing.  
* **Conflict-Heatmap:** local conflict intensity; over threshold → **intervention** (caregiver or environment).

  Goal: emergent cooperation/competition **without chaos**.

---

# 8. Emergence Dynamics (What is Expected)

* **Self-Efficacy:** increases with success logs; External→Internal-Drive increases.  
* **Will & Planning:** imagination space + plan commitment → goal-directed sequences.  
* **Language:** word→image→category→proto-grammar (markers) → **exaptation** as planning lever.  
* **Developmental Asymmetries:** early parameters (noise, proximity weights) can create stable learning profiles (incl. "neurotic" patterns).  
* **Robustness:** hysteresis, mood-OU, risk-damping, satiation/cooldown, reality-checks stabilize.

---

# 9. Evaluation & Measurability (KPIs)

* **Map-Stability:** entropy↓, revisit-count↑, angle-diversity↑ (gate: coverage≥0.9 ∧ entropy≤E* ∧ revisit≥k).  
* **PE-AUC:** area under the prediction-error curve (should ↓).  
* **Success Log Curve:** score development over time.  
* **Volition Ratio:** proportion planned vs. reactive actions (should ↑).  
* **Plan Success Rate** & **Pre-Check-Pass-Rate**, **Post-PE-Reduction**.  
* **Language:** word-learning rate (MIC/F1), proto-sentence frequency.  
* **Emotional Dynamics:** anxiety-/cry-rate, mood-distribution.  
* **Curiosity/Safety/Fatigue** as three lines, + events (collision, door-change).  
* **Curiosity Persistent:** persistent curiosity local.  
* **External→Internal-Drive-Ratio:** E2I = self_success / (self_success + praise) (should ↑).  
* **Obsession Metrics:** max_consecutive[obj], satiation_mean, cooldown_triggers.  
* **Curiosity/Safety/Fatigue** as three lines, + events (collision, door-change).  
* **Curiosity Persistent:** persistent curiosity local.
* **Ablations:** mood off, dream off, gates off → measure differences.

---

# 10. Experimental Phase Protocol

**P0 – Sensory & Mapping:** one room, head movement, mapping, passive language logging; calibrate ε, EWMA, entropy-gate.  
**P1 – Movement & Door:** second room, coherence tests, movement.  
**P2 – Objects & Language:** ball/cube; double evidence, negative samples, first single-step plan; satiation/cooldown.  
**P3 – Caregiver Movement & Dream:** thought channel free; praise-internalization, replay (70/30), post-PE-reduction track.  
**P4 – Outside World & Multi-Step Planning:** topology graph, pre/post-checks, plan-commitment-KPIs.  
**P5 – Social:** multiple children; safeguards on; reputation, fair-share, conflict-heatmap.

---

# 11. Implementation Details & Reproducibility

* **Technology:** Godot (environment), Python (agent).  
* **Structures:** simple dicts/JSON-logs (events, needs, gates, PE, plans, language, success log).  
* **Seeds & Versions:** global seed, config hash, "genome" version (slow weights), checkpoints (fast/slow/memory).  
* **Feature-Flags & Diagnosis-Dashboard:** stepwise activation; 5 core curves: PE, coverage, volition, proximity, fatigue.  
* **Parameter-Defaults:** conservative; gate-counts (X,Y,Z) via beta-binomial (light individuality).

---

# 12. Limits, Risks & Ethics

* **No Guarantee for "Subjective" Consciousness:** we address functional markers (self-efficacy, will, planning, language).  
* **Tuning & Interactions:** more complex in P4–P5; KPIs/ablations necessary.  
* **Social Dynamics:** safeguards mandatory (fair-share, reputation, heatmap).  
* **Bias & Anthropomorphism:** interpret labels/markers soberly; no overclaims.  
* **Openness & Reproducibility:** logs, seeds, configs disclose.

---

# 13. Conclusion

The model shows how a **very simple, robust mechanism** (thresholds with hysteresis, needs, noise, success log, imagery, dream) in an embodied simulation can generate **rich, emergent behavior**: **self-efficacy** grows, **will & planning** form in the **imagination space**, **language** goes from label to tool. The architecture is **implementable**, **measurable**, and **extendable** – a pragmatic path to new quality of embodied AI development.

---

## (Appendix – formal core definitions, compact)

**EWMA Smoothing** for Gate Metrics

$$
\text{EWMA}_t = \alpha \cdot x_t + (1-\alpha)\cdot \text{EWMA}_{t-1}, \quad \alpha \in (0,1]
$$

**Hysteresis & Dwell**

Gate active if EWMA ≥ T_high for ≥ τ; withdrawal only at EWMA ≤ T_low.

**Mood as OU-Process**

$$
dM_t = \theta(\mu - M_t)dt + \sigma dW_t \Rightarrow M_t \in [-1,1]
$$

Acts as gain on utility/exploration; on gates only ±2–3%.

**Prediction-Error (PE) & Risk-Weighted Bonus**

$$
\text{PE} = \lVert \hat{s} - s \rVert; \quad \text{bonus} = \text{PE}\cdot(1-\text{risk}), \; \text{risk}\in[0,1]
$$

Persistent high PE without reduction → penalty instead of bonus.

**Satiation/Cooldown**

$$
\text{satiation}_{t+1}[id] = \min(1,\text{satiation}_t[id]+\eta);\quad \text{Motiv}= \text{base}\cdot\text{novelty}\cdot(1-\text{satiation}[id])
$$

Night/dream: partial reset; hard-guard: max. n_consecutive interactions/object.

**Forgetting & Reactivation**

Non-core memories: value ↓ with time constant τ_f; sight/naming reactivates ↑; dream replay (70/30) stabilizes.

**Praise-Internalization**

$$
\alpha_t = e^{-\lambda \cdot \text{tage}},\quad \text{weight}_t = \alpha_t\cdot \text{praise} + (1-\alpha_t)\cdot \text{self}
$$

→ External→Internal-Drive shifts motivation from praise to self-signal.

**Pre- / Post-Reality-Check**

Pre: Alignment A≥θ_pre; Post: ΔPE<0 & Log-Update.

---

### Acknowledgments

We thank the collaborative discussion and idea development between Till Zöller, ChatGPT (GPT-5 Thinking), and Grok, whose dialogue made the emergence model possible.