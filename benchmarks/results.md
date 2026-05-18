| Model                          | Quant  | Context | Tokens/sec | TTFT (s) | Output Tokens |
| ------------------------------ | ------ | ------: | ---------: | -------: | ------------: |
| DeepSeek-Coder-V2-Lite         | Q8_0   |    4096 |      ~15.4 |     ~6.4 |          ~596 |
| DeepSeek-Coder-V2-Lite         | Q4_K_M |    4096 |      ~16.0 |     ~6.2 |          ~597 |
| DeepSeek-Coder-V2-Lite         | Q4_K_M |    2048 |   **22.0** |  **3.5** |          ~596 |
| Qwen3-4B-2507 Kimi-K2 Thinking | Q8     |    4096 |   **24.4** |     ~4.9 |      **2295** |
| Qwen3-VI-4B                    | Q4     |    4096 |   **39.8** |     ~3.0 |          ~651 |
| Qwen3-VL-4B                    | Q4     |    2048 |   **41.8** |  **2.2** |          ~651 |


Key Findings
1. Context length dominates performance

At 4096 tokens, attention + KV cache cost dominates:

DeepSeek Q4 ≈ Q8

Quantization alone does not improve throughput

Reducing context to 2048 immediately unlocks speed gains.

2. Model architecture matters more than quant at long context

Qwen3-VI / VL-4B scale unusually well at 4K context

DeepSeek-Coder-V2-Lite becomes attention-bound at 4K

Small, dense models outperform larger coder-specialized models on raw throughput

3. Thinking models trade latency for verbosity

Qwen Kimi-K2 Thinking shows high tok/sec

But generates ~4× more tokens

End-to-end latency is higher despite fast decoding


Practical Recommendations

Fast interactive coding

Qwen3-VL-4B @ 2048

 - Lowest TTFT

 - Highest throughput

 - Normal-length outputs


Long-context coding & refactors

DeepSeek-Coder-V2-Lite Q8_0 @ 4096

 - Slower, but stable

 - Predictable coder-style outputs

Code review & explanation

Qwen3-4B Kimi-K2 Thinking @ 4096

 - Verbose reasoning

 - Better for audits and teaching


Why This Benchmark Matters

Most local LLM comparisons:

Change multiple variables at once

Compare cold vs warm runs

Ignore token volume

This benchmark isolates:

Context vs quantization

Architecture vs decoding speed

Throughput vs real user latency