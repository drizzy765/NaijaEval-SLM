# NaijaEvalSLM Documentation

Welcome to the central documentation hub for **NaijaEvalSLM** — a multilingual small language model evaluation, fine-tuning, and serving project focused on Nigerian English and Pidgin customer support use cases.

## Project Overview
NaijaEvalSLM covers two main milestones:

1. Project 2: SLM Evaluation & Fine-Tuning Lab
2. Project 3: High-Throughput Serving & Benchmarking Engine

The project evaluates 3B-class open-weight models, measures multilingual quality and safety, and packages the best-performing model into a production-style serving stack.

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
