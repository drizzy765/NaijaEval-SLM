# Team Responsibilities & Workflow Protocol

## Team Members
* **Agoro Oluwatimilehin** (`@oluwatimilehin`)
* **Ajiboye Toluwalase** (`@toluwalase`)

## Model Ownership Matrix
| Model Architecture | Lead Engineer | Primary Objective |
| :--- | :--- | :--- |
| `Qwen/Qwen2.5-3B-Instruct` | Agoro Oluwatimilehin | High-performance instruction following & Pidgin adaptation |
| `deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B` | Agoro Oluwatimilehin | Reasoning trajectory analysis in customer support queries |
| `meta-llama/Llama-3.2-3B-Instruct` | Ajiboye Toluwalase | Standard English & Nigerian English baseline comparative |
| `microsoft/Phi-3.5-mini-instruct` | Ajiboye Toluwalase | Memory-constrained efficiency baseline |

## Component Breakdown

### Agoro Oluwatimilehin Focus Areas
* **Eval Engine:** Lead developer of `src/evaluation/llm_judge.py` and bias mitigation tools.
* **Qwen/DeepSeek Training:** Execute QLoRA scripts (`configs/models/qwen2.5_3b.yaml`).
* **vLLM Engine Configuration:** Lead deployment of `src/serving/vllm_engine.py` and PagedAttention settings.

### Ajiboye Toluwalase Focus Areas
* **Dataset & Tokenization:** Lead dataset curator for `data/processed/` and token fertility analyzer.
* **Llama/Phi Training:** Execute QLoRA scripts (`configs/models/llama3.2_3b.yaml`).
* **Load Testing & Observability:** Lead setup for `benchmarks/locustfile.py` and Prometheus/Grafana dashboard configurations.

## Git Workflow Standards
* **Branches:** 
  * `main`: Stable release code only.
  * `feature/eval-harness` (Oluwatimilehin)
  * `feature/sft-pipeline` (Toluwalase)
  * `feature/vllm-serving` (Joint)
* **Commit convention:** `feat:`, `fix:`, `docs:`, `eval:`, `perf:`.