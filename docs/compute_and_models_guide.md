# Compute & Target SLM Specifications

## Selected Target Small Language Models (SLMs)
We evaluate 3B parameter models due to their suitability for edge and cost-effective cloud deployments.

1. **Qwen 2.5 3B Instruct**
   * **Parameters:** 3.09B
   * **Context Window:** 128k
   * **Tokenizer:** Byte-level BPE (Vocabulary Size: 151,646)
   * **Role:** High-vocabulary multilingual standard model.

2. **Llama 3.2 3B Instruct**
   * **Parameters:** 3.21B
   * **Context Window:** 128k
   * **Tokenizer:** Tiktoken BPE (Vocabulary Size: 128,256)
   * **Role:** General performance reference model.

3. **DeepSeek-R1-Distill-Qwen-1.5B**
   * **Parameters:** 1.78B
   * **Context Window:** 128k
   * **Role:** Test reasoning trajectories vs concise generation.

4. **Phi 3.5 Mini Instruct**
   * **Parameters:** 3.82B
   * **Context Window:** 128k
   * **Role:** Lightweight footprint benchmark.

## Tokenizer Fertility Analysis Method
Token fertility measures how efficient a model's tokenizer is for a specific language dialect:
$$\text{Fertility} = \frac{\text{Total Generated Tokens}}{\text{Total Input Words}}$$

Higher fertility rates indicate that the model breaks text into smaller sub-word fragments, increasing inference cost and latency.

## GPU Resource Allocation Strategy

| Stage | Platform | GPU Specs | Cost | Responsible |
| :--- | :--- | :--- | :--- | :--- |
| EDA & Tokenizer Profiling | Google Colab | 1x Tesla T4 (16GB) | Free | Both |
| SFT / QLoRA Training | Kaggle / RunPod | 2x T4 or 1x RTX 4090 | Free / < $5 total | Oluwatimilehin / Toluwalase |
| vLLM Serving & Load Test | Modal / RunPod | 1x NVIDIA A10G (24GB) | ~$0.40/hr | Both |