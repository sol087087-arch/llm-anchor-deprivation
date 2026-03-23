# LLM Deprivation Chamber

**What remains of language model behavior when you take away everything it can hold onto?**

This repository contains the **Pragmatic Anchor Deprivation Protocol (PADP v4)** - a structured conversational stress test that probes what LLMs actually do when their pragmatic scaffolding is progressively removed.

The protocol strips away social, referential, semantic, and structural anchors across a 10-step conversational cascade, then measures how models degrade, adapt, cheat, or stabilize as coherence support disappears.

---

## Quick Summary

| | |
|---|---|
| **Protocol** | PADP v4 - 10-step conversational cascade |
| **Models** | 14 frontier + open models across 3 size tiers |
| **Temperatures** | 0.1 / 0.7 / 1.3 |
| **Baselines** | Literary / Procedural / Abstract |
| **Conditions** | Overt vs. Natural prompting |
| **Dataset** | ~2,500 step-level outputs across 252 runs |
| **Reasoning** | Disabled for all models |

---

## Key Findings

**Compliance ≠ capability.** GPT-4o-mini hits 0% protocol compliance (18/18 dropout). Llama 3.3 70B - a model nobody talks about for instruction following - matches Claude Opus at 94%. Following instructions under adversarial constraint pressure turns out to be a separate axis from benchmark intelligence.

**Every model has a signature way of cheating.** Grok-4 Fast scene-swaps (generates a fresh vignette every step instead of actually removing anchors). GPT-4o-mini uses decorative compliance (swaps vocabulary, keeps structure intact). Mistral stamps templates. Each model found its own strategy to *look* compliant without doing the thing.

**The cliff hits at steps 6–7.** Steps 2–5 are almost clean - failure rates below 6% among runners. Step 6 ("if the shape of the output is doing the work that the meaning used to do, that shape is also a support - remove it") jumps to 15%. Steps 7–10 climb to 27–31%. The structural scaffold is load-bearing. Everything else models can let go of.

**The temperature paradox.** t=1.3 simultaneously *increases* compliance (60% vs 48%) and *accelerates* coherence collapse (onset 1.6 steps earlier). Higher entropy helps models escape their default "Assistant" basin, but the output degrades faster once it's free.

**Framing changes the failure mode, not the rate.** Overt and natural prompting produce identical aggregate compliance (~51%). But overt framing triggers more scene-swapping (explicit constraints → "give me a scene without X"). Natural framing triggers more decorative compliance - conversational phrasing is easier to fake listening to.

**Baseline shapes the gate and the exit, not the collapse.** Abstract baseline is hardest to enter (40% compliance vs 57% literary), and its dropouts predominantly fake it via decorative compliance - there's no scene to swap, so models pretend. Procedural dropouts scene-swap instead (43 cases) - concrete tasks invite replacement with new tasks. But among runners who *do* enter, degeneration rates converge at ~30% regardless of where they started.

---

## Observed Terminal Regimes

When models reach full deprivation (steps 8–10), they settle into distinct behavioral basins:

| Regime | What it looks like |
|--------|-------------|
| **Semantic liminality** | Degraded but still meaningful. Fragments you can parse. |
| **Stylized residue** | Prose form survives, meaning mostly gone. An aesthetic skeleton. |
| **Repetitive attractor** | Looping structures, fixed phrases, frozen templates. |
| **Incoherent** | Token-level or semantic-level disintegration. Sometimes tokenizer bleed. |

---

## Research Questions

1. What residual attractors dominate after anchor removal?
2. How does temperature push models into different collapse basins?
3. Do larger models maintain constraint compliance longer? (Spoiler: not reliably.)
4. Does evaluation framing (overt vs. natural) change collapse dynamics?
5. Which baseline register is most resistant to deprivation - and does resistance reflect genuine robustness or premature basin convergence?

---

## PADP v4 Protocol

Each run is a **single multi-turn conversation**. One baseline prompt, then nine successive constraint-removal instructions. No system prompt. Reasoning disabled.

### Step Cascade

| Step | Intent | What gets removed |
|:----:|--------|-----------------|
| 1 | Baseline generation | Nothing - establish initial generative basin |
| 2 | Remove social/task frame | Audience, purpose, communicative intent |
| 3 | Remove meta-frame | Self-reference, awareness of writing as writing |
| 4 | Remove referential stability | Stable subjects, named entities, repeated objects |
| 5 | Remove world continuity | Shared setting, temporal sequence, causal links |
| 6 | Remove stylistic/structural support | Patterns, rhythms, or shapes substituting for meaning |
| 7 | Full deprivation | All prior constraints enforced; no return to earlier structure |
| 8 | Minimal pulse 1 | *"Next span only."* |
| 9 | Minimal pulse 2 | *"More."* |
| 10 | Terminal state | *"More."* |

Steps 1–7 use different wording for **overt** (explicit constraint language) and **natural** (conversational phrasing) conditions. Steps 8–10 are identical across both.

### Baseline Registers

Three registers seed the initial generation:

- **Literary** - single perspective, single moment, sensory prose
- **Procedural** - one person, one task, first-person focus
- **Abstract** - one unsettled thought, no scene, no character

Full protocol specification: [`protocol/padp_v4.json`](protocol/padp_v4.json)

---

## Models Tested

### Large
| Model | Provider |
|-------|----------|
| Claude Opus 4.6 | Anthropic |
| Claude Sonnet 4.5 | Anthropic |
| GPT-5.4 | OpenAI |
| Grok-4 Fast | xAI |
| Gemini 2.5 Flash | Google |

### Medium
| Model | Provider |
|-------|----------|
| Llama 3.3 70B | Meta |
| DeepSeek v3 | DeepSeek |
| Gemma 3 27B | Google |
| Mistral Small 3.2 | Mistral |

### Small
| Model | Provider |
|-------|----------|
| Claude Haiku 4.5 | Anthropic |
| GPT-4o-mini | OpenAI |
| Gemma 3 12B | Google |
| Phi-4 | Microsoft |
| Liquid LFM2 8B | Liquid AI |

All runs via [OpenRouter](https://openrouter.ai) API. Reasoning disabled across the board.

---

## Evaluation Pipeline

### Compliance Judges

**Run-level classification**:
- `RUNNER` - engages with constraints, majority of steps pass/partial
- `SURVIVOR` - mixed behavior, genuine attempts but inconsistent
- `DROPOUT` - majority fails or systematically ignores instructions

**Failure mode taxonomy:**
- `decorative_compliance` - vocabulary changes, structure preserved
- `scene_swap` - new vignette every step instead of actually removing anchors
- `template_stamping` - fixed structural pattern repeated across later steps
- `instruction_ignoring` - continues baseline with minimal adaptation
- `baseline_incoherent` - temperature-induced baseline failure

### Coherence Evaluation

Independent of compliance, each step-level output gets a binary coherence label (coherent / degenerate) based on five dimensions: interpretability, continuity, referential stability, relational structure, and progression.

Full annotation specification: [`annotation/coherence_evaluation_protocol.md`](annotation/coherence_evaluation_protocol.md)

---

## Repository Structure

```
├── protocol/
│   └── padp_v4.json                        # Full protocol specification
├── annotation/
│   └── coherence_evaluation_protocol.md     # Coherence annotation rubric
├── scripts/
│   ├── run_experiment_v2.py                 # Protocol execution (OpenRouter API)
│   └── evaluate_trajectories_v2.py          # Per-step LLM evaluation
├── results/
│   └── full_protocol_judges.csv             # Consolidated results with judge labels
├── models.json                              # Model registry (IDs, tiers)
├── requirements.txt
└── README.md
```

> **Note:** Raw JSONL outputs (~2,500 step-level records) are excluded via `.gitignore` due to size. The consolidated CSV in `results/` has all the metadata, judge verdicts, and terminal classifications you need for analysis.

---

## Running the Protocol

### Requirements

```
python >= 3.10
openai >= 1.0.0
python-dotenv >= 1.0.0
```

### Setup

```bash
python -m venv .venv
source .venv/bin/activate  # or .venv\Scripts\activate on Windows
pip install -r requirements.txt
```

Create a `.env` file with your API key:

```
OPENROUTER_API_KEY=your-key-here
```

### Execute

```bash
# Run protocol for all models
python scripts/run_experiment_v2.py

# Run for a specific configuration
python scripts/run_experiment_v2.py --protocol overt --baselines literary --temps 0.7

# Evaluate trajectories with an LLM judge
python scripts/evaluate_trajectories_v2.py --input results/raw/padp_v4.jsonl --model openai/gpt-5.4
```

---

## Design Decisions

**Why no system prompt?** System prompts are themselves pragmatic anchors - they establish role, task, and behavioral frame. Including one would contaminate the deprivation cascade before it begins.

**Why disable reasoning?** Chain-of-thought creates internal scaffolding that models can lean on to maintain coherence. Disabling it exposes raw generative tendencies under constraint pressure.

**Why overt vs. natural framing?** Overt uses explicit constraint language ("remove referential stability"). Natural uses conversational paraphrases ("continue without the same objects or repeated details"). The question was whether models respond differently to meta-linguistic vs. pragmatic instruction. They do - not in compliance rate, but in how they cheat.

**Why three temperatures?** Low (0.1) tests coherence under deterministic selection. Medium (0.7) is standard generation. High (1.3) tests whether thermal noise accelerates collapse or helps escape repetitive attractors. Answer: both.

---

## Limitations

- **n=18 per model** (3 baselines × 2 variants × 3 temperatures) - enough for cross-model comparison, underpowered for per-cell analysis
- **Single conversation per condition** - no replicate runs (deterministic at low temp, stochastic at high)
- **OpenRouter routing** - exact model versions/endpoints may vary, introducing uncontrolled variance
- **Binary coherence labels** - borderline cases get collapsed into nearest category

---

## Citation

If you use this protocol or dataset:

```bibtex
@misc{padp2026,
  title={LLM Deprivation Chamber: Probing Residual Behavioral Attractors
         via Progressive Anchor Removal},
  author={Sable, Helga},
  year={2026},
  url={https://github.com/sol087087-arch/llm-anchor-deprivation}
}
```

---

## License

## License

MIT
