# Production Serving & Load Benchmarking Architecture

## vLLM Engine Configuration
Models are deployed using **vLLM** to take advantage of PagedAttention and continuous batching.

```yaml
# configs/serving/vllm_config.yaml
model: "out/fine_tuned_qwen2.5_3b"
tensor_parallel_size: 1
gpu_memory_utilization: 0.85
max_num_batched_tokens: 4096
max_model_len: 4096
quantization: "fp8" # optional for production speedup
dtype: "bfloat16"
```

## Production Telemetry Metrics

### Latency & Throughput Targets
* **TTFT (Time to First Token):** Measures prefill stage delay.
* **ITL (Inter-Token Latency):** Measures decoding stage speed per generated token.
* **Concurrency Handling:** Target $\ge 50$ QPS under $p_{95} \text{ TTFT} < 300\text{ms}$.

```
                      +-------------------+
                      |   Locust / k6     |
                      | Load Generator    |
                      +---------+---------+
                                |
                                v
                      +-------------------+
                      |   FastAPI Router  |
                      +---------+---------+
                                |
                                v
                      +-------------------+
                      |    vLLM Engine    |
                      | (PagedAttention)  |
                      +---------+---------+
                                |
                   +------------+------------+
                   |                         |
                   v                         v
        +--------------------+    +--------------------+
        | Prometheus Metrics |    | Langfuse Tracing   |
        +----------+---------+    +--------------------+
                   |
                   v
        +--------------------+
        | Grafana Dashboard  |
        +--------------------+
```

## Load Testing Commands
```bash
# Run Locust benchmarking against vLLM server
locust -f benchmarks/locustfile.py --headless -u 50 -r 5 --run-time 10m --host http://localhost:8000
```