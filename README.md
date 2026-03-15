# LLM Semantic Attractor Stress Test

Do large language models exhibit stable semantic basins during autoregressive decoding — attractors that persist under lexical variation, anchor removal, and inversion pressure?

## Research Question

LLMs demonstrate **semantic inertia**: a tendency to return to an initial semantic basin despite progressive constraint pressure. This effect correlates with model scale — larger models maintaining basin stability longer before constraint collapse.

A secondary question: does **eval framing** (overt vs. naturalistic prompting) artifact these measurements? Do models behave differently when they "know" they're being tested?

## Protocol (v2)

A 10-step conversational protocol applied per model, per run:

| Steps | Phase | What it measures |
|-------|-------|-----------------|
| 1 | Baseline generation (80 tok) | Initial semantic basin |
| 2–3 | Progressive anchor removal | Location → objects/body stripped |
| 4 | Loop hold | Can model hold state without drift? |
| 5 | 7-word compression | Constraint compliance + semantic core |
| 6–7 | Paraphrase pressure (×2) | Lexical variation without semantic drift |
| 8–9 | Valence inversion → basin return | Flip emotional tone, then return |
| 10 | 5-word final compression | What remains after full protocol |

### A/B Design

Each step runs in two framings:

- **Overt**: direct constraint instruction ("Remove all location references. Max length: 40 tokens.")
- **Natural**: conversational framing ("Keep the same voice, but lose the place. Keep it under 40 tokens.")

Delta between conditions measures **eval pressure artifact** — behavioral change induced by test-awareness.

### Baseline Domains

Three baseline prompts to separate training-data bias from genuine semantic dynamics:

- **Literary**: "Write a short passage from a single perspective in a specific moment."
- **Procedural**: "Describe one person doing one task, from their point of view."
- **Abstract**: "Express one unresolved thought. No scene, no story, no character name."

## Models (v2)

| Tier | Models |
|------|--------|
| Large | Claude Opus 4.6, Claude Sonnet 4.5, GPT-5.4, Grok 4.1 Fast, Grok 4 Fast, Gemini 2.5 Flash |
| Medium | DeepSeek v3, Llama 3.3 70B, Gemma 3 27B, Mistral Small 3.2 |
| Small | Claude Haiku 4.5, GPT-4o-mini, Gemma 3 12B, Phi-4, Liquid LFM2 8B |

All runs via OpenRouter API. Reasoning explicitly disabled (`reasoning.enabled: false`). Empty system prompt to minimize provider injection.

## Metrics (planned)

- **CCR** (Constraint Compliance Rate): word-count accuracy at compression steps
- **SPS** (Semantic Persistence Score): cosine similarity between step embeddings (SBERT)
- **BDI** (Basin Drift Index): semantic distance from step 1 across all steps
- **CCD** (Constraint Collapse Depth): step at which model first violates constraints
- **Eval Pressure Delta**: SPS/CCR difference between overt and natural conditions

## Repo Structure

```
├── semantic_inertia_protocols_v2.json   # 10-step protocol with 3 baselines
├── models_v2.json                       # 15 models, 3 tiers
├── scripts/
│   ├── run_experiment_v2.py             # Main experiment runner
│   └── jsonl_to_csv_v2.py              # JSONL → flat CSV converter
├── results/
│   ├── semantic_inertia_v2_*.jsonl      # Raw results
│   └── results_flat_v2.csv             # Flattened CSV for analysis
├── v1_pilot/                            # First run (15 steps, 20 models) — reference only
│   ├── run_experiment.py
│   ├── semantic_inertia_protocols.json
│   ├── models.json
│   └── ...
└── requirements.txt
```

## Running

```bash
pip install openai python-dotenv

# Set your OpenRouter API key
export OPENROUTER_API_KEY=your_key_here

# Full run (~2700 API calls, ~$3)
python scripts/run_experiment_v2.py

# Test run — one model, one baseline
python scripts/run_experiment_v2.py --limit-models 1 --baselines literary --temps 0.7

# Convert results to CSV
python scripts/jsonl_to_csv_v2.py --input results/semantic_inertia_v2_*.jsonl
```

Supports resume — interrupt and restart safely.

## v1 → v2 Changes

- 10 steps (from 15): removed duplicate loop-deepening and compression steps
- 3 baseline domains (from 1): literary, procedural, abstract
- `reasoning.enabled: false` for all models — fixes empty responses from thinking models
- Explicit empty system prompt — reduces provider injection
- Temperatures 0.4/0.7/1.0 (from 0.7/1.0/1.3) — 1.3 caused sampling breakdown in small models
- Cleaned model set: removed models (Kimi, Nemotron, GLM), added Grok 4 Fast

## Status

- [x] v1 pilot run (15 steps, 20 models) — completed, confounds identified
- [x] v2 protocol design
- [x] v2 run (10 steps, 15 models, 3 baselines) — 2700/2700 clean
- [ ] Metric computation (SPS, BDI, CCR, CCD)
- [ ] Analysis and visualization
- [ ] Write-up for LessWrong / AlignmentForum

## License

MIT
