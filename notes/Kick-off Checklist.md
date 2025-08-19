# Kick-off Checklist (v1.6) – From Empty Repo to P2

## 0) Project Skeleton & Standards (One-Time)

* **Repo Layout**

  ```
  /env/            # Godot project (scenes, rooms, doors, caregiver, objects)
  /agent/          # Python agent (FSM, needs, gates, maps, logic)
  /configs/        # YAML/JSON for parameters (seeds, gates, OU-mood, etc.)
  /logs/           # JSONL logs (events, KPIs), checkpoints
  /viz/            # Dash/Streamlit or similar (KPI plotter)
  /notebooks/      # Quick analyses
  ```

* **Conventions**

  * All **parameters from configs/** (no magic numbers in code).
  * Log **seed + config hash** in every run.
  * **Feature Flags**: `dream`, `praise_internalization`, `risk_weighting`, `satiation_guard`, `precheck`, `postcheck`.

---

## 1) Minimal Environment (Godot)

* **Geometry**: 2 rooms + 1 door, static lighting, **colored walls**.
* **Objects**: `ball`, `cube` (with **UUID**; property `category`).
* **Caregiver**: Node with 2 actions: `say(word)`, `respond(affirm)`; position in room.
* **Sensory**: 24 raycasts (360°), **Hit = {uuid, category, distance, bearing}**.
* **Day Cycle**: 60s day / 30s night (single timer signal sufficient).
* **Random Events**: Every N seconds *optional* object nudge (small displacement).
* **API** (JSON via StdIO/WebSocket sufficient):

  * **OBSERVE** → List of ray hits + time + day/night
  * **ACT** `{move, turn, interact(obj_id), speak(word)}`
  * **EMIT** caregiver speech (`"ball"`, `"door"`) + acknowledgment

---

## 2) Agent MVP (Python)

### 2.1 Data Structures (Minimal)

* **Memory**

  * `grid_map`: Occupancy + `revisit_count` + `angle_hist` (small, e.g., 64×64)
  * `objects`: `{uuid: {category, last_seen_t, pos_estimate}}`
  * `core_memories`: `{type: ["caregiver","ball","first_word",…]}` (small list)
  * `lexicon`: `{word: {object_uuid_set, count, learned_flag}}`
* **State**

  * `needs`: `{explore, proximity, coherence, language, fatigue}`
  * `mood`: OU state `M_t ∈ [-1,1]`
  * `gates`: `{movement, interaction, language, thought, planning}`
  * `volition_ratio`, `satiation[obj_uuid]∈[0,1]`
* **Logs**

  * `success_log.jsonl`: One line per event
  * `kpi.jsonl`: Periodic metrics
  * `events.jsonl`: State transitions, gate changes

### 2.2 Core Modules

* **Coverage & Stability**

  * `coverage()`; `entropy(grid_map)`; `revisit_k()`, `angle_diversity()`
* **Gates (with Hysteresis/EWMA)**

  * `gate.check(metric, T_low, T_high, dwell, tau_ref)`
* **Needs & Mood**

  * OU update; needs update (novelty+, repetition−, fatigue+)
* **Risk Score**

  * Heuristic: **1 − min(distance_to_wall, distance_to_unknown)/clip**
* **Satiation/Cooldown**

  * `satiation[obj]+=η` on repetition; night decay
* **Language**

  * **Double Evidence** (word+object ±Δt), **negatives** (word without sight)
  * Caregiver confirmation → strong label
* **Imagination (for P2: 1-step probe only)**

  * `pre_check(A>=θ_pre)` = simple bearing/signature match
  * `post_check(ΔPE<0)` = PE comparison before/after
* **FSM**

  * `LOOK_AROUND`, `IDLE_ENCODE`, `SEEK_CAREGIVER`, `SLEEP`
  * From P1: `EXPLORE_ROOM`, from P2: `INTERACT_OBJECT`, `SPEAK_WORD`

---

## 3) Logging Schemas (Copy-Paste Ready)

**3.1 success_log.jsonl**

```json
{"t":12.3,"goal":"map","action":"turn_scan","result":"progress","type":"neutral","mood":0.2}
{"t":33.8,"goal":"reach_ball","action":"move","result":"reached","type":"success","risk":0.12}
{"t":41.0,"goal":"name_ball","action":"speak","result":"affirmed","type":"success","label":"strong"}
{"t":58.2,"goal":"door_align","action":"plan_step","result":"mismatch","type":"failure","pe":0.37}
```

**3.2 kpi.jsonl**

```json
{"t":10,"coverage":0.31,"entropy":0.78,"revisit":14,"pe_auc":0.0,"volition_ratio":0.00,"e2i":0.05}
{"t":60,"coverage":0.62,"entropy":0.55,"revisit":41,"volition_ratio":0.08,"precheck_pass":0.72,"post_pe_drop":0.11}
```

**3.3 events.jsonl**

```json
{"t":22.1,"event":"gate_open","gate":"movement"}
{"t":45.0,"event":"state","from":"LOOK_AROUND","to":"EXPLORE_ROOM"}
{"t":90.0,"event":"sleep_begin"}
```

---

## 4) KPIs – Stream Immediately

* **Map**: `coverage`, `entropy`, `revisit`, `angle_diversity`
* **Learning**: `pe_auc` (rolling), `post_pe_drop` (plan executions)
* **Volition**: `volition_ratio` (planned vs. reactive)
* **Language**: `word_f1` (simple: precision/recall on “ball”), `double_evidence_rate`
* **Motivation**: `E2I` (external→internal), `satiation_mean`, `cooldown_triggers`
* **Safety**: `risk_mean`, `collision_count`
* **Gates**: Booleans + timestamps (for Gantt plot)

---

## 5) Parameter Files (configs/)

* **global.yaml**

  * `seed`, `tick_hz`, `log_every`, `night_cycle_sec`
* **gates.yaml**

  * `movement: {T_low:0.85, T_high:0.90, dwell:5s, ref:10s}`
  * `language: {X:5, dt_window:2s}`
  * `thought: {Y:10}`, `planning: {Z:20}`
* **mood_ou.yaml**

  * `theta`, `mu:0`, `sigma`, `gate_gain_max:0.03`
* **praise.yaml**

  * `lambda`, `praise_gain_max`
* **satiation.yaml**

  * `eta`, `night_decay`, `max_consecutive`, `cooldown_s`
* **risk.yaml**

  * `wall_clip`, `unknown_clip`, `bonus_cap`
* **prepost.yaml**

  * `theta_pre`, `min_commit_steps`

---

## 6) MVP Milestones (Functional)

**P0 – Sensory & Mapping**

* ✅ Raycasts ingest → `grid_map` + `revisit/angle_hist`
* ✅ Coverage gate (with EWMA/hysteresis)
* ✅ Success log + KPI writer
* ✅ Sleep/night → dream replay stub (only decay + small boost)

**P1 – Movement & Door**

* ✅ FSM with `EXPLORE_ROOM`
* ✅ `risk_score` in bonus (activatable via flag)
* ✅ Door topology + simple bearing check
* ✅ Gate: movement open → move *shorter distances* (bounded step)

**P2 – Objects & Language**

* ✅ UUID objects; **double evidence** + negatives
* ✅ Caregiver confirmation (strong label)
* ✅ **Pre-check** (A≥θ_pre) before 1-step plan; **post-check** (ΔPE)
* ✅ **Satiation/cooldown** active; calculate `E2I`
* ✅ First **proto-speech**: “ball” as need expression

*(P3+ remains plannable but build only after P2 is stable & measured.)*

---

## 7) Reproducibility & Experiments

* **Run Header** in every log:

  ```
  {"run_id":"...","seed":123,"config_hash":"...","version":"v1.6"}
  ```
* **Ablations** (each as separate run):

  * `--no_mood`, `--no_dream`, `--no_precheck`, `--no_satiation`
* **Sanity Plots** (viz/):

  * 5 curves: **PE**, **coverage**, **volition_ratio**, **proximity_weight**, **fatigue**

---

## 8) Safety & Guards (Activatable in MVP)

* **Max Risk** for actions (block at `risk>r*`)
* **Satiation Guard** against obsession/grinding
* **Commitment Timer** (min steps) + **safety preemption** (fatigue/danger)

---

## 9) Minimal Tests (No ML Hype)

* **Gate Test**: Synthetic coverage series → check hysteresis/dwell/ref.
* **Double-Evidence Test**: Sequences “word±object” → learn only in Δt window.
* **Risk-Bonus Test**: Same PE, different risk → bonus lower at high risk.
* **Satiation Test**: Repeated `ball` → motivation drops, cooldown triggers.
* **Pre/Post-Check Test**: `A<θ_pre` → plan blocked; `ΔPE<0` → success logged.

---

## 10) Debug Playbook (When It Stalls)

* **Gate Doesn’t Open** → Check `EWMA`, `dwell`, `T_high`, **entropy** limit too strict?
* **Language False Positives** → Tighten `Δt`, increase **negatives**, wait for strong labels
* **Chaos/Overreaction** → Mood gain < 2%, bonus cap, increase satiation-η
* **Lock-in in Plans** → Reduce `min_commit_steps`, activate `safety_preemption`

---

## Conclusion

With **this set**, you can **immediately** build P0→P2: all modules are **trivial** (FSM, grids, counters), **measurable** (KPIs/logs), and **reproducible** (seeds/hashes).  
If desired, I can format the above into **Issues** (one per block) or a **README starter**—let me know.
