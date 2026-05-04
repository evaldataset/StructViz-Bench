## StructViz-Bench Reviewer Q&A

### Q1. Why are the main claims based on only four models?
The paper's main conclusions are anchored to four stable representative models: GPT-4o, Gemini Flash, Qwen2.5-VL-7B, and Claude Sonnet. Additional local-model runs were explored for completeness, but they were intentionally not given equal evidentiary weight because they were substantially weaker and less stable.

### Q2. Did you evaluate open-weight local models?
Yes. InternVL2.5-8B was retained as caveated supplementary evidence only. LLaVA-NeXT was excluded from quantitative interpretation because the completed run showed frequent degenerate long-form generations.

### Q3. Why should reviewers trust the benchmark conclusions if some local-model runs were unstable?
The core claims do not rely on those unstable runs. The main comparative evidence comes from the four core models, whose experiments, ablations, and paper tables are internally consistent and fully built into the main pipeline.

### Q4. What is the main empirical takeaway?
Visualization format alone can change correctness substantially even when the underlying data and question semantics are fixed. The benchmark shows large within-modality best--worst gaps and consistent fragility across tabular, time-series, and graph reasoning.

### Q5. Why include prompt sensitivity and mixed-type experiments?
These experiments test whether the benchmark phenomenon generalizes beyond the primary visualization-sensitivity setup. Prompt sensitivity shows that instruction phrasing can also move accuracy materially, while mixed-type evaluation probes cross-modal reasoning beyond single-format questions.

### Q6. Are the supplementary local-model runs reproducible?
Only partially. InternVL2.5-8B required compatibility workarounds and should be treated as caveated supplementary evidence. LLaVA-NeXT should not be treated as reliable quantitative evidence.

### Q7. What should not be claimed in rebuttal or camera-ready discussion?
- Do not say the results are consistent across all evaluated model families.
- Do not use LLaVA-NeXT as confirmatory evidence.
- Do not present InternVL2.5-8B as equally reliable support for fine-grained conclusions.

### Q8. What is the strongest reviewer-facing framing?
StructViz-Bench's central contribution is the controlled benchmark design and the robust four-model evidence that visualization sensitivity is a real failure mode in MLLM reasoning over structured data.
