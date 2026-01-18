Local LLM Benchmark — Apple Silicon (LM Studio)

This repository documents a controlled local LLM benchmark conducted on Apple-silicon hardware using LM Studio (Metal backend).

The purpose of this benchmark is to measure real, user-perceived performance of small-to-mid sized local LLMs across:

Context length

Quantization

Model architecture

Rather than focusing only on tokens/sec, this benchmark captures latency, verbosity, and end-to-end responsiveness for realistic coding tasks.

Benchmark Goals

Understand when context length dominates performance

Compare quantization vs architecture trade-offs

Identify fast vs stable vs reasoning-oriented models

Provide reproducible, hardware-specific results

All results are measured locally, not theoretical.

Test Methodology
Hardware

Device: MacBook Pro (14-inch)

Chip: Apple M5

Memory: 32 GB unified memory

Operating System: macOS Tahoe 26.2

Architecture: Apple Silicon (ARM64)

Benchmarks reflect unified-memory behavior and Metal GPU acceleration characteristics specific to Apple-silicon systems.

Runtime & Engine

Inference runtime: LM Studio

Backend: llama.cpp (Metal)

GPU offload: All layers

Flash Attention: Enabled

KV cache: Offloaded to GPU

Evaluation batch size: 512

Model residency: Model kept in memory between runs

Sampling Configuration (fixed across all tests)

Temperature: 0.2

Top-p: 0.9

Repeat penalty: 1.1

Measurement Rules

Only second (warm) runs recorded

Same prompt across all models

Same termination condition (EOS)

No concurrent workloads during testing

Metrics Captured

Tokens/sec (tok/sec): steady-state decode throughput

Time-to-first-token (TTFT): perceived latency

Total output tokens: verbosity / reasoning cost

Reproducibility Notes

Absolute numbers are hardware-dependent

Relative trends are the primary signal

Results represent Apple-silicon + Metal inference (not CUDA GPUs)

Benchmark Prompt
You are reviewing production Python code.

Task:
1. Write a function that deduplicates a list of dicts by a given key.
2. Handle missing keys gracefully.
3. Preserve original order.
4. Explain the algorithm and its time complexity.
5. Provide one edge case test.

Constraints:
- Idiomatic Python
- No external libraries
- Clarity over cleverness


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

At 4096 tokens, attention and KV-cache costs dominate:

DeepSeek Q4 ≈ Q8

Quantization alone does not improve throughput

Reducing context to 2048 immediately unlocks speed gains.

2. Architecture matters more than quant at long context

Qwen3-VI / VL-4B scale unusually well at 4K context

DeepSeek-Coder-V2-Lite becomes attention-bound at 4K

Smaller dense models can outperform coder-specialized models on raw throughput

3. Thinking models trade latency for verbosity

Qwen Kimi-K2 Thinking shows high tok/sec

Generates ~4× more tokens

End-to-end completion time is higher despite fast decoding

Practical Recommendations
Fast interactive coding
Qwen3-VL-4B @ 2048


Lowest TTFT

Highest throughput

Normal-length outputs

Long-context coding & refactors
DeepSeek-Coder-V2-Lite Q8_0 @ 4096


Slower, but stable

Predictable coder-style outputs

Code review & explanation
Qwen3-4B Kimi-K2 Thinking @ 4096


Verbose reasoning

Suitable for audits and teaching

Why This Benchmark Matters

Many local LLM comparisons:

Mix cold and warm runs

Change multiple variables at once

Focus only on tokens/sec

This benchmark isolates:

Context vs quantization

Architecture vs decoding speed

Throughput vs real user latency

Repository Structure (Suggested)
local-llm-benchmarks/
├── README.md
├── benchmarks/
│   ├── tok_per_sec_vs_context.drawio
│   └── results.csv
├── prompts/
│   └── coder_dedup_test.txt
└── notes/
    └── interpretation.md

License

This benchmark documentation is shared for educational and experimental purposes.
Model licenses remain with their respective authors.