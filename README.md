# Local LLM Benchmarks

## Overview

This repository documents a controlled local LLM benchmark run on Apple Silicon with LM Studio and the llama.cpp Metal backend. It compares small-to-mid sized local models across context length, quantization, throughput, latency, and output length for a realistic coding prompt.

The benchmark emphasizes user-perceived performance, especially time-to-first-token and end-to-end responsiveness, rather than tokens/sec alone.

## Architecture Summary

The project is a static benchmark publication:

- benchmark source data lives in `benchmarks/` and `docs/charts/`;
- chart specifications are Vega-Lite JSON files under `docs/charts/`;
- GitHub Pages serves the generated site from `docs/`;
- methodology and prompt files are kept in `notes/` and `prompts/`;
- deeper system notes live in `design/architecture.md`.

## Repository Structure

```text
.
├── .github/workflows/pages.yml
├── README.md
├── benchmarks/
│   ├── results.csv
│   └── results.md
├── charts/
│   ├── README.md
│   ├── series_toksec_vs_context.csv
│   └── series_ttft_vs_context.csv
├── design/
│   ├── architecture.md
│   └── issues-pending-review.md
├── docs/
│   ├── index.html
│   ├── charts.html
│   ├── Local_LLM_Benchmark_Apple_Silicon.pdf
│   └── charts/
│       ├── points.csv
│       ├── toksec_vs_context.vl.json
│       └── ttft_vs_context.vl.json
├── notes/
│   └── Test Methodology.md
├── prompts/
│   └── coder_dedup_text.txt
└── src_archives/
    └── 2026-05-18_housekeeping/
```

## Setup

No package install is required to read the benchmark results or serve the static site. The interactive charts load Vega, Vega-Lite, and Vega-Embed from jsDelivr at browser runtime.

## Run

Serve the Pages site locally from the `docs/` directory:

```bash
python3 -m http.server 8000 --directory docs
```

Then open `http://localhost:8000/`.

## Test / SIT

The housekeeping SIT validates that:

- benchmark CSV files parse successfully;
- Vega-Lite chart specs parse as JSON and reference existing local data files;
- the static HTML page references existing chart specs;
- a local static server can serve the `docs/` site.

Current SIT status is recorded in `design/issues-pending-review.md`.

## Configuration

There are no required environment variables or secrets. GitHub Pages deployment is configured in `.github/workflows/pages.yml` and publishes the `docs/` directory from the `main` branch.

## Documentation

- Architecture: `design/architecture.md`
- Pending review issues: `design/issues-pending-review.md`
- Test methodology: `notes/Test Methodology.md`
- Benchmark prompt: `prompts/coder_dedup_text.txt`

## Current Status

The repository is ready for static review and GitHub Pages publication. Remaining review items are tracked in `design/issues-pending-review.md`.
