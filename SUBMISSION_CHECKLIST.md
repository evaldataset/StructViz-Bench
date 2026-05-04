## StructViz-Bench Submission Checklist

### Paper Scope
- Main claims rely on 4 core models only: GPT-4o, Gemini Flash, Qwen2.5-VL-7B, Claude Sonnet
- InternVL2.5-8B appears only as caveated supplementary evidence
- LLaVA-NeXT is excluded from quantitative interpretation due to degenerate generations

### Artifacts
- `paper/main.pdf` builds successfully
- `release/structviz-bench-arxiv.zip` builds successfully
- `results/full_analysis.md` is regenerated in core-only mode by default
- `results/ablation/prompt_sensitivity_summary.csv` exists
- `results/mixed_analysis_summary.csv` exists

### Pre-Submission Manual Checks
- Confirm author names/affiliations in `paper/main.tex`
- Confirm anonymization policy if submitting anonymous version
- Confirm dataset release URLs and repo URLs are correct or intentionally omitted
- Confirm appendix wording does not treat InternVL/LLaVA as confirmatory evidence

### Release Order
1. Final PDF check
2. arXiv zip validation
3. GitHub push
4. HuggingFace dataset upload
5. NeurIPS submission upload
6. arXiv submission

### Known Caveats
- Some support scripts are core-4 by default and require flags to include supplementary local results
- Existing result files for InternVL/LLaVA should not be used for headline comparisons
