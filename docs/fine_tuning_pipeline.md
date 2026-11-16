# Fine-Tuning Pipeline Architecture

## Technique: QLoRA (Quantized Low-Rank Adaptation)
To fine-tune 3B models within 16GB VRAM, we load base models in 4-bit NormalFloat (NF4) and train low-rank adapter matrices.

### Hyperparameter Configuration (`qlora_sft_config.yaml`)
* **Quantization:** 4-bit NF4 with Double Quantization (`bitsandbytes`)
* **Compute Data Type:** `bfloat16`
* **LoRA Rank ($r$):** 16
* **LoRA Alpha ($\alpha$):** 32
* **LoRA Dropout:** 0.05
* **Target Modules:** `q_proj`, `k_proj`, `v_proj`, `o_proj`, `gate_proj`, `up_proj`, `down_proj`
* **Learning Rate:** $2 \times 10^{-4}$ with cosine scheduler
* **Batch Size:** 4 per device with Gradient Accumulation Steps = 4 (Effective Batch Size = 16)
* **Optimizer:** `paged_adamw_8bit`

## Dataset Format Standard (`train_sft.jsonl`)
```json
{
  "id": "pidgin_support_001",
  "messages": [
    {"role": "system", "content": "You are a helpful customer support assistant."},
    {"role": "user", "content": "My order never reach since last week Friday, wetin dey happen?"},
    {"role": "assistant", "content": "Sorry about the delay! Make I check the tracking status for you now now. Wetin be your order number?"}
  ]
}
```

## DPO (Direct Preference Optimization) Pipeline
Used post-SFT to align output tone and eliminate hallucinations in localized dialects.
* **Loss Function:** Sigmoid DPO loss with $\beta = 0.1$.
* **Logged Artifacts:** Model checkpoints, loss curves, and evaluation scorecards synced directly to MLflow/Weights & Biases.