## StructViz-Bench

### Project Overview
StructViz-Bench — Unified Benchmark for MLLM Reasoning over Visualized Structured Data. First benchmark that systematically evaluates how visualization format affects MLLM reasoning across Tabular, Time Series, and Graph data. Three difficulty levels: single-type, mixed-type, and reasoning depth (1-hop to counterfactual).

StructViz-Bench focuses on a controlled evaluation setting: each benchmark item is rendered in multiple visualization styles while preserving the same underlying data and question semantics. This enables direct measurement of visualization sensitivity in model reasoning.

### Build, Lint, and Test Commands
```bash
# Setup (optional local venv)
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# Lint and format checks
ruff check .
ruff format .

# Type check
mypy src/ --ignore-missing-imports

# Run full tests
pytest tests/ -q

# Run key scripts
python scripts/generate_benchmark.py --config configs/generation.yaml
python scripts/render_all.py --config configs/generation.yaml
python scripts/run_full_eval.py --config configs/evaluation.yaml
python scripts/generate_leaderboard.py --results results/
```

### Code Style Guidelines
- Python 3.10+ with explicit type hints on public APIs.
- Use `from __future__ import annotations` in source modules.
- Prefer dataclasses for structured records.
- Keep functions single-purpose and deterministic where possible.
- Use Google-style docstrings for public classes and methods.
- String formatting via f-strings.
- Format and lint with ruff (line length 100).
- Test critical generation, rendering, and metric paths.

### Key Dependencies
- Core ML/runtime: `torch`, `transformers`, `vllm`, `accelerate`
- Data and viz: `pandas`, `numpy`, `matplotlib`, `seaborn`, `plotly`, `networkx`, `Pillow`
- Time-series transforms: `pyts`
- Evaluation and tooling: `scikit-learn`, `pytest`, `mypy`, `ruff`, `tqdm`, `tabulate`, `jsonlines`, `pyyaml`
- API model interfaces: `openai`, `anthropic`

### Research Context
Existing benchmarks (ChartQA, VisualTimeAnomaly, VGCURE) each cover one data type. StructViz-Bench tests visualization sensitivity across all three modalities — same question, different visual format.

This benchmark is designed for controlled comparisons across:
- Data modality: tabular, time series, graph, and mixed-type composites.
- Visualization style: multiple renderings per modality.
- Reasoning depth: one-hop retrieval to counterfactual reasoning.

### Key References
- ChartQA
- VisualTimeAnomaly (WWW 2026)
- VGCURE (ACL 2025)
- GRAB (ICCV 2025)
- Visual-TableQA
- CaTS-Bench
