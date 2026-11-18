# Results: 423 BERT Inference Sweep

**Main goal:** Measure PyTorch BERT inference performance  
**Tools:** Run gpu-bench-bert-transformers-inference.py via collect_bert_infer.py

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_146 · recorded 2026-11-18). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 NVL (95830 MiB) · AMD EPYC 9374F 32-Core Processor · Ubuntu 26.04.1 LTS · kernel 6.8.0-138-generic · driver 595.112.07
- **Run:** 2026-11-18T11:09:22Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 01:15 · benchmark 00:01 · whole run 01:24
- **Source:** run 423_20261118_110922
- **Command:** `bash run_benchmark.sh --smoke --model-name bert-base-uncased --dtype f16_r --sequence-len 32 --batch-size 1 --warmup-iters 2 --num-iterations 5 --prompt-source synthetic`

| # | Metric | Result |
|---|---|---|
| 1 | Inference throughput (sequences_per_sec) | 312.093026 |
| 2 | Inference latency, mean step time, ms (mean_latency_ms) | 3.204173 |
| 3 | Token throughput (tokens_per_sec) | 9986.976827 |
| 4 | Peak GPU memory (peak_gpu_memory_gb) | 0.260582 |
<!-- /profile:smoke -->
