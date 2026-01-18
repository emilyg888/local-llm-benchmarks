Test Methodology
Hardware

Device: MacBook Pro (14-inch)

Chip: Apple M5

Memory: 32 GB unified memory

Operating System: macOS Tahoe 26.2

Architecture: Apple Silicon (ARM64)

All benchmarks were executed locally on a single Apple-silicon machine.
Results reflect unified-memory behavior and Metal GPU acceleration characteristics specific to this platform.

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

Tokens per second (tok/sec) — steady-state decode throughput

Time to first token (TTFT) — perceived latency

Total output tokens — verbosity / reasoning cost

Reproducibility Notes

Absolute numbers are hardware-dependent

Relative trends (context vs quantization vs architecture) are the primary signal

Results are representative of Apple-silicon + Metal inference, not CUDA-based GPUs