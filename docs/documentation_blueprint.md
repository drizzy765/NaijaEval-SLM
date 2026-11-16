# NaijaEvalSLM: Project 2 & 3 SLM Evaluation, Fine-Tuning Lab & High-Throughput Serving Engine

**Authors & Lead Machine Learning Engineers:**
* Agoro Oluwatimilehin (ML Infrastructure & Model Evaluation Lead)
* Ajiboye Toluwalase (MLOps & Model Fine-Tuning Lead)

---

## Table of Contents
1. [Repository Directory Architecture](#1-repository-directory-architecture)
2. [Team Workload & Model Assignment Matrix](#2-team-workload--model-assignment-matrix)
3. [Target SLMs & Free/Budget Cloud Compute Stack](#3-target-slms--freebudget-cloud-compute-stack)
4. [Project 2: SLM Evaluation & Fine-Tuning Specification](#4-project-2-slm-evaluation--fine-tuning-specification)
5. [Project 3: High-Throughput Serving & Benchmarking Specification](#5-project-3-high-throughput-serving--benchmarking-specification)
6. [Complete `docs/` Markdown File Contents](#6-complete-docs-markdown-file-contents)
   * `docs/README.md`
   * `docs/TEAM_RESPONSIBILITIES.md`
   * `docs/COMPUTE_AND_MODELS.md`
   * `docs/EVALUATION_PROTOCOL.md`
   * `docs/FINE_TUNING_PIPELINE.md`
   * `docs/SERVING_AND_BENCHMARKING.md`

---

## 1. Repository Directory Architecture

To support automated scaffolding by autonomous developer agents, the repository structure is strictly formatted below:

```text
naijaevalslm/
├── .github/
│   └── workflows/
│       ├── lint-and-test.yml
│       └── eval-pipeline-ci.yml
├── docs/
│   ├── README.md
│   ├── TEAM_RESPONSIBILITIES.md
│   ├── COMPUTE_AND_MODELS.md
│   ├── EVALUATION_PROTOCOL.md
│   ├── FINE_TUNING_PIPELINE.md
│   └── SERVING_AND_BENCHMARKING.md
├── data/
│   ├── raw/
│   │   ├── standard_english_prompts.json
│   │   ├── nigerian_english_prompts.json
│   │   └── pidgin_prompts.json
│   ├── processed/
│   │   ├── train_sft.jsonl
│   │   ├── train_dpo.jsonl
│   │   └── frozen_test_set.jsonl
│   └── annotations/
│       ├── human_ground_truth.json
│       └── judge_outputs/
├── configs/
│   ├── models/
│   │   ├── qwen2.5_3b.yaml
│   │   ├── llama3.2_3b.yaml
│   │   ├── deepseek_r1_distill_3b.yaml
│   │   └── phi3.5_mini.yaml
│   ├── fine_tuning/
│   │   ├── qlora_sft_config.yaml
│   │   └── dpo_config.yaml
│   └── serving/
│       ├── vllm_config.yaml
│       └── tensorrt_config.json
├── src/
│   ├── __init__.py
│   ├── data_pipeline/
│   │   ├── __init__.py
│   │   ├── tokenizer_fertility.py
│   │   └── dataset_formatter.py
│   ├── evaluation/
│   │   ├── __init__.py
│   │   ├── llm_judge.py
│   │   ├── kappa_spearman.py
│   │   └── bias_detector.py
│   ├── agent_eval/
│   │   ├── __init__.py
│   │   ├── tool_definitions.py
│   │   └── tau_bench_runner.py
│   ├── training/
│   │   ├── __init__.py
│   │   ├── sft_trainer.py
│   │   └── dpo_trainer.py
│   └── serving/
│       ├── __init__.py
│       ├── fastapi_app.py
│       └── vllm_engine.py
├── benchmarks/
│   ├── locustfile.py
│   ├── stress_test.sh
│   └── parse_metrics.py
├── dashboards/
│   ├── grafana_dashboard.json
│   └── prometheus.yml
├── tests/
│   ├── test_evaluation.py
│   ├── test_tokenizer.py
│   └── test_serving.py
├── Dockerfile
├── docker-compose.yml
├── pyproject.toml
├── requirements.txt
└── README.md
```

---

## 2. Team Workload & Model Assignment Matrix

To prevent code overlap and encourage modular collaboration, work is partitioned by SLM model families and system components:

| Area / Module | **Agoro Oluwatimilehin** | **Ajiboye Toluwalase** |
| :--- | :--- | :--- |
| **Assigned Model Families** | **Qwen & DeepSeek Family**<br>• `Qwen/Qwen2.5-3B-Instruct`<br>• `deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B` | **Llama & Phi Family**<br>• `meta-llama/Llama-3.2-3B-Instruct`<br>• `microsoft/Phi-3.5-mini-instruct` |
| **Data & Tokenization** | Tokenizer Fertility script & Standard/Nigerian English prompt curation. | Dataset schema validation, Pidgin localization & synthetic augmentation. |
| **Eval Infrastructure** | LLM-as-a-Judge execution framework & Position/Verbosity bias checks. | Human annotation scoring interface, Cohen's $\kappa$ & Spearman $\rho$ calculators. |
| **Fine-Tuning Execution** | QLoRA SFT/DPO pipeline implementation for Qwen & DeepSeek models. | QLoRA SFT/DPO pipeline implementation for Llama & Phi models. |
| **Agent Benchmarking** | Tau-bench style tool evaluation setup (Order & Refund API simulation). | Failure taxonomy parser (wrong tool, invalid args, hallucinated parameters). |
| **Serving & Infrastructure** | vLLM Engine configuration, PagedAttention setup & Docker build. | Locust load test suite, Prometheus/Grafana metric collectors (TTFT, ITL, KV Cache). |

---

## 3. Target SLMs & Free/Budget Cloud Compute Stack

### Target SLMs
1. **`Qwen/Qwen2.5-3B-Instruct`** (Primary - Oluwatimilehin)
2. **`meta-llama/Llama-3.2-3B-Instruct`** (Primary - Toluwalase)
3. **`deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B`** (Reasoning Baseline - Oluwatimilehin)
4. **`microsoft/Phi-3.5-mini-instruct`** (Efficiency Baseline - Toluwalase)

### Cloud Compute & Free Tier GPU Strategy
* **Google Colab (Free Tier):** T4 GPU (16GB VRAM) — Ideal for Tokenizer profiling, EDA, small LLM-as-a-judge batches, and basic testing.
* **Kaggle Code Kernels (Free Tier):** 2x T4 GPUs (30 hours/week free) — Used for QLoRA fine-tuning 3B models in 4-bit quantization (`bitsandbytes`).
* **Modal Labs / RunPod (Budget Tier):** 
  * $25 free credits on signup for Modal.
  * 1x NVIDIA A10G (24GB VRAM) or A100 (40GB VRAM) at ~$0.40 - $0.79/hr for high-concurrency vLLM serving, TensorRT compilation, and final evaluations.
* **Open-Weight Baseline APIs (Judge Reference):**
  * Together AI / Groq API free tiers for baseline scoring and judge comparison (e.g., Llama-3.3-70B-Instruct as standard judge).

---

## 4. Project 2: SLM Evaluation & Fine-Tuning Specification

* **Objective:** Determine if a 3B quantized SLM (fine-tuned with QLoRA/DPO) can match/exceed a 70B/Proprietary API model on customer support queries containing Standard English, Nigerian English, and Pidgin text.
* **Key Metrics:**
  * Task Correctness, Helpfulness, Faithfulness, Tone, Safety (1-5 Scale).
  * Inter-Annotator Agreement (Cohen's $\kappa \ge 0.65$, Spearman $\rho \ge 0.70$).
  * Token Fertility Rate: $\text{Fertility} = \frac{\text{Total Tokens}}{\text{Total Words}}$.
  * Tool Call Success Rate (Agent Eval).

---

## 5. Project 3: High-Throughput Serving & Benchmarking Specification

* **Objective:** Package the winning fine-tuned SLM into a production-grade containerized serving engine capable of maintaining strict SLA under concurrent traffic.
* **Key Metrics:**
  * **Time-to-First-Token (TTFT):** $p_{95} < 200\text{ms}$.
  * **Inter-Token Latency (ITL):** $p_{95} < 30\text{ms/token}$.
  * **Throughput:** Tokens per second ($\text{tok/s}$) per GPU under 10, 50, 100 concurrent requests.
  * **KV Cache Utilization Percentage.**

---

## 6. Complete `docs/` Markdown File Contents

### File 1: `docs/README.md`
```markdown:Documentation Readme:docs/README.md
# NaijaEvalSLM Documentation

Welcome to the central documentation hub for **NaijaEvalSLM** — a multilingual SLM evaluation, fine-tuning, and high-throughput serving project for Nigerian English and Pidgin customer support scenarios.

## Project Directors
* **Agoro Oluwatimilehin** (Model Evaluation & Qwen/DeepSeek Pipelines)
* **Ajiboye Toluwalase** (Fine-Tuning Pipelines & Llama/Phi Infrastructure)

## Documentation Structure
1. [`TEAM_RESPONSIBILITIES.md`](./TEAM_RESPONSIBILITIES.md): Ownership breakdown, workflow policies, and git branch strategy.
2. [`COMPUTE_AND_MODELS.md`](./COMPUTE_AND_MODELS.md): Model selection justification, tokenizer profiling metrics, and GPU allocation details.
3. [`EVALUATION_PROTOCOL.md`](./EVALUATION_PROTOCOL.md): LLM-as-a-judge rubrics, bias detection, human agreement formulas, and agent evaluation.
4. [`FINE_TUNING_PIPELINE.md`](./FINE_TUNING_PIPELINE.md): QLoRA SFT and DPO hyperparameter specs, dataset format, and MLflow/W&B logging standards.
5. [`SERVING_AND_BENCHMARKING.md`](./SERVING_AND_BENCHMARKING.md): vLLM optimization, Docker configurations, Locust load testing, and Prometheus/Grafana monitoring.

## Quick Start
```bash
# Clone and setup virtual environment
git clone https://github.com/your-username/naijaevalslm.git
cd naijaevalslm
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```