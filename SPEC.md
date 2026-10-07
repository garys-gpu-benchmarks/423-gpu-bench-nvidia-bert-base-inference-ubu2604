# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Runs gpu-bench-bert-transformers-inference.py with Hugging Face bert-base-uncased on synthetic token batches (PyTorch, not TensorFlow). model_name, sequence_len, batch_size, warmup_iters, num_iterations, and precision mixed_float16 / f16_r come from yaml. Collector runs the script twice (pass1/pass2/summary) Sweep dimensions: model_name, dtype, precision, num_gpus, prompt_source, sequence_len, batch_size, warmup_iters.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| model_name | `--model-name` | smoke=bert-base-uncased, baseline=bert-base-uncased, extended=bert-base-uncased | bert-base-uncased | From Parameter list; see Execution Description With Parameters. |
| dtype | `--dtype` | smoke=f16_r, baseline=f16_r, extended=f16_r | f16_r | From Parameter list; see Execution Description With Parameters. |
| precision | `--precision` | smoke=mixed_float16, baseline=mixed_float16, extended=mixed_float16 | mixed_float16 | From Parameter list; see Execution Description With Parameters. |
| num_gpus | `--num-gpus` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| prompt_source | `--prompt-source` | smoke=synthetic, baseline=synthetic, extended=synthetic | synthetic | From Parameter list; see Execution Description With Parameters. |
| sequence_len | `--sequence-len` | smoke=32, baseline=128, extended=256 | 128 | From Parameter list; see Execution Description With Parameters. |
| batch_size | `--batch-size` | smoke=1, baseline=4, extended=8 | 4 | From Parameter list; see Execution Description With Parameters. |
| warmup_iters | `--warmup-iters` | smoke=2, baseline=2, extended=5 | 2 | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=5, baseline=37100, extended=102000 | 37100 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run gpu-bench-bert-transformers-inference.py via collect_bert_infer.py
```

## Raw Output Format

raw_results.csv with pass1, pass2, and summary from two runs of the BERT inference script

check,model_name,sequence_len,batch_size,status,sequences_per_sec,mean_latency_ms,latency_p50_msec,latency_p99_msec,tokens_per_sec,peak_gpu_memory_gb
pass1,bert-base-uncased,128,8,ok,40,200,190,220,5120,4

## Metrics

- **#1: Inference throughput** — stored as `sequences_per_sec`.
- **#2: Inference latency, mean step time, ms** — stored as `mean_latency_ms`.
- **#3: Token throughput** — stored as `tokens_per_sec`.
- **#4: Peak GPU memory** — stored as `peak_gpu_memory_gb`.

## Framework

Runs gpu-bench-bert-transformers-inference.py with Hugging Face bert-base-uncased on synthetic token batches (PyTorch, not TensorFlow). model_name, sequence_len, batch_size, warmup_iters, num_iterations, and precision mixed_float16 / f16_r come from yaml. Collector runs the script twice (pass1/pass2/summary)

## Installation and Execution Summary

Run gpu-bench-bert-transformers-inference.py twice with Hugging Face Transformers bert-base-uncased on synthetic token batches (yaml sequence_len/batch_size, mixed_float16), parse RESULT sequences/s, tokens/s, and p50/p95/p99 latency, and average a summary row, to measure PyTorch BERT inference. This is not TensorFlow

## Platform Portability

- **AMD (primary):** ```bash
Run gpu-bench-bert-transformers-inference.py via collect_bert_infer.py
```
- **NVIDIA:** Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

raw_results.csv with pass1, pass2, and summary from two runs of the BERT inference script

check,model_name,sequence_len,batch_size,status,sequences_per_sec,mean_latency_ms,latency_p50_msec,latency_p99_msec,tokens_per_sec,peak_gpu_memory_gb
pass1,bert-base-uncased,128,8,ok,40,200,190,220,5120,4

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Runs gpu-bench-bert-transformers-inference.py with Hugging Face bert-base-uncased on synthetic token batches (PyTorch, not TensorFlow). model_name, sequence_len, batch_size, warmup_iters, num_iterations, and precision mixed_float16 / f16_r come from yaml. Collector runs the script twice (pass1/pass2/summary)
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Runs gpu-bench-bert-transformers-inference.py with Hugging Face bert-base-uncased on synthetic token batches (PyTorch, not TensorFlow). model_name, sequence_len, batch_size, warmup_iters, num_iterations, and precision mixed_float16 / f16_r come from yaml. Collector runs the script twice (pass1/pass2/summary)

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
