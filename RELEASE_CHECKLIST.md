# Release Checklist (Code + Dataset Upload)

> Pre-flight checklist before pushing the code repository (e.g., GitHub) and the dataset (e.g., Hugging Face Datasets).
> Last updated: 2026-05-05

---

## 0. Quick status

- [x] `paper/` is self-contained: `main.tex`, `neurips_2026.sty`, `references.bib`, `qualitative_examples.tex`, `figures_full/`
- [x] No hard-coded API keys or absolute paths in committed code (verified by `grep`)
- [x] `.gitignore` excludes credentials, caches, large artifacts, generated logs
- [x] Croissant metadata (Core 1.0 + RAI 1.0) at `croissant.json`
- [x] LICENSE present (Apache-2.0 for code, CC-BY-4.0 for data — declared in `DATASET_CARD.md`)
- [x] `tests/` 67/67 passing (unit-level)
- [x] Paper compiles cleanly (24 pages: 9 content + references + 14 appendix + checklist)

---

## 1. Files to commit (code repo)

### Required
```
README.md
REPRODUCTION.md
DATASET_CARD.md
DATASET_CARD_HF.md
HUMAN_EVAL_PROTOCOL.md
LICENSE
requirements.txt
croissant.json
.gitignore
pyrightconfig.json
AGENTS.md            # repo conventions (optional but useful)

paper/main.tex
paper/neurips_2026.sty
paper/references.bib
paper/qualitative_examples.tex
paper/checklist_2026_template.tex   # reference template
paper/figures_full/*.pdf

src/                 # all source modules
scripts/             # all entry-point scripts
configs/             # YAML configs
tests/               # unit tests
benchmark/base_items.jsonl
benchmark/mixed_items.jsonl
benchmark/realworld_test.jsonl
```

### Conditionally committed (large but useful)
```
results/full_gpt4o.jsonl
results/full_gemini.jsonl
results/full_qwen.jsonl
results/full_qwen32b.jsonl
results/full_claude.jsonl
results/confidence_intervals.txt
results/full_analysis.md
```
Each result JSONL is ~6 MB. If GitHub repo size is a concern, push results to the HF dataset repo instead and link from README.

---

## 2. Files NOT to commit (excluded by `.gitignore`)

| Path | Reason |
|---|---|
| `.venv/`, `__pycache__/`, `*.py[cod]` | Python build artifacts |
| `.pytest_cache/`, `.ruff_cache/` | Tool caches |
| `paper/*.aux *.bbl *.blg *.log *.out *.synctex.gz *.fdb_latexmk *.fls *.toc` | LaTeX build artifacts |
| `paper/main.pdf` | Compiled PDF (uploaded separately to OpenReview) |
| `logs/` (7.5 MB) | Run logs (qwen32b_eval.log, etc.) |
| `.omc/` | Developer agent state |
| `notebooks/` | Empty / scratch notebooks |
| `data/` | Raw external data (SciTabAlign, ETT, NetworkX caches) — re-download via REPRODUCTION.md |
| `benchmark/rendered/` | Pre-rendered images — regenerate via `scripts/render_all.py` or distribute via HF |
| `results/pilot/`, `results/.archive/`, `results/*_extracted.jsonl`, `results/*_v2.jsonl` | Intermediate / archived outputs |
| `results/human_eval/images/` (28 MB) | Annotation rendering, regenerable |
| `release/huggingface/hf_dataset/` (57 MB) | HF-generated dataset cache, regenerated on push |
| `release/huggingface/human_eval_space/.git/` | Nested git from HF Space |
| `release/arxiv/` (264 KB) | **Obsolete** (used `neurips_2025.sty`); safe to remove from disk |
| `release/structviz-bench-arxiv.zip` | Stale arxiv staging zip |
| `*.env`, `**/hf_token*`, `**/api_key*`, `*.pem`, `*.key` | Credentials (never commit) |

---

## 3. Manual deletions recommended (disk cleanup)

These can be physically deleted (not just git-ignored). Review then:

```bash
# Obsolete arxiv staging area (older neurips_2025.sty)
rm -rf release/arxiv/
rm -f  release/structviz-bench-arxiv.zip

# Generated logs (regenerable via run_fullscale_eval.py)
rm -rf logs/

# Tool caches (regenerable)
rm -rf .pytest_cache/ .ruff_cache/

# OMC / agent state (developer-only, not part of release)
rm -rf .omc/

# Empty or scratch notebooks
rm -rf notebooks/

# HF-generated dataset cache (recreated on push)
rm -rf release/huggingface/hf_dataset/

# Human-eval image renders (regenerable from prepare_human_eval.py)
rm -rf results/human_eval/images/

# Intermediate metric-recompute outputs (final values are in main results)
rm -f results/full_*_v2.jsonl results/full_*_extracted.jsonl results/mixed_*_extracted.jsonl
```

---

## 4. Upload sequence

### A. Code repository (GitHub or Bitbucket, anonymous mirror)

1. Verify `git status` shows only intended files.
2. `git rm --cached -r logs/ .omc/ release/arxiv/ release/huggingface/hf_dataset/` (if any were tracked previously).
3. Confirm no API keys/tokens in `git log` (last 50 commits).
4. Push to anonymous mirror; tag release `v1.0.0`.
5. Verify clone-and-run from a fresh directory:
   ```bash
   git clone <anon-url> sb-test
   cd sb-test
   python -m venv .venv && source .venv/bin/activate
   pip install -r requirements.txt
   export PYTHONPATH=.
   pytest tests/                                      # expect 67 passed
   python scripts/run_fullscale_eval.py --help       # CLI sanity
   ```

### B. Hugging Face Datasets (anonymous account)

1. Create dataset repo `anonymous/structviz-bench` (Public + Anonymous).
2. Upload:
   - `benchmark/base_items.jsonl`, `benchmark/mixed_items.jsonl`, `benchmark/realworld_test.jsonl`
   - `croissant.json`
   - `DATASET_CARD_HF.md` → as `README.md` in HF root
   - `LICENSE`
   - Optional: `benchmark/rendered/` zipped as `rendered_images.zip` (if hosting)
3. Add SHA256 checksums to `croissant.json` `distribution[*].sha256` fields:
   ```bash
   sha256sum benchmark/base_items.jsonl benchmark/mixed_items.jsonl benchmark/realworld_test.jsonl
   ```
   Update Croissant accordingly and re-upload.
4. Validate Croissant with the MLCommons validator:
   ```bash
   pip install mlcroissant
   mlcroissant validate --jsonld croissant.json
   ```

### C. Results / supplementary (zip for OpenReview)

1. Zip the supplementary bundle:
   ```bash
   zip -r supplementary.zip \
     scripts/ src/ configs/ tests/ benchmark/*.jsonl \
     results/full_*.jsonl results/full_analysis.md \
     croissant.json README.md REPRODUCTION.md \
     DATASET_CARD.md LICENSE requirements.txt
   ```
2. Verify zip < 100 MB (OpenReview limit).
3. Upload `supplementary.zip` and `paper/main.pdf` separately on OpenReview submission portal.

---

## 5. Anonymity audit (double-blind submission)

- [ ] `git log` author email scrubbed for review (`git config user.email anonymous@example.com` for new commits; consider `git filter-repo` for history if author info is sensitive).
- [ ] Repository name and URL anonymized on OpenReview supplementary link.
- [ ] No author affiliation in `paper/main.tex` (\author block uses `Anonymous Authors`).
- [ ] HF dataset under `anonymous/structviz-bench` (not personal handle).
- [ ] No internal-only paths or institution names in `README.md`, `REPRODUCTION.md`, comments.
- [ ] `\acks{}` block empty.
- [ ] Self-citations use anonymous language (no "our prior work [Author 20XX]").

---

## 6. Final pre-submission gates

| Gate | Status |
|---|---|
| `pytest tests/` passes 67/67 | ✅ |
| `pdflatex paper/main.tex` (× 2) compiles cleanly | ✅ |
| `bibtex` resolves all citations | ✅ |
| Body fits 9 content pages (NeurIPS 2026 ED Track) | ✅ |
| Croissant JSON parses + conforms to Core 1.0 + RAI 1.0 | ✅ |
| `grep -rE "sk-|hf_[A-Za-z0-9]{30,}|AIza"` returns nothing | ✅ |
| `grep -rE "/home/|/mnt/WorkSpace"` (excluding results/) returns nothing | ✅ |
| `.gitignore` covers credentials, caches, generated artifacts | ✅ |
| `LICENSE` present and consistent with DATASET_CARD | ✅ |
| Anonymous URLs and HF handle prepared | ⏳ user action |

---

## 7. Quick commands

```bash
# Run final test sweep
pytest tests/ -q

# Re-render all paper figures (deterministic)
PYTHONPATH=. python scripts/generate_paper_figures.py \
  --split full --results-dir results/ --output-dir paper/figures_full/

# Recompile paper
cd paper && pdflatex main.tex && bibtex main && pdflatex main.tex && pdflatex main.tex

# Check what would be committed (after disk cleanup)
git status --short

# Test fresh clone reproducibility
cd /tmp && rm -rf sb-test && git clone <anon-url> sb-test && cd sb-test \
  && python -m venv .venv && source .venv/bin/activate \
  && pip install -r requirements.txt && export PYTHONPATH=. \
  && pytest tests/ -q
```
