# Architecture

## 1. Purpose

This project publishes a local LLM benchmark for Apple Silicon machines running LM Studio with the llama.cpp Metal backend. It serves readers who want reproducible, hardware-specific evidence about context length, quantization, latency, throughput, and output-length trade-offs for local coding models.

## 2. Current System Shape

The repository is a static documentation and data project. It does not contain an application runtime, package manifest, or model inference code. Benchmark measurements are stored as CSV and Markdown, and the publishable site is served from `docs/` through GitHub Pages.

The primary publication artifact is `docs/charts.html`, with `docs/index.html` acting as the Pages entry point. The charts are rendered in-browser with Vega-Lite specifications stored in `docs/charts/`.

## 3. Component Map

| Component | Path | Responsibility | Key dependencies |
|---|---|---|---|
| Project overview | `README.md` | User-facing summary, run instructions, repository map, documentation links | `design/architecture.md` |
| Benchmark results | `benchmarks/results.csv`, `benchmarks/results.md` | Source benchmark table and narrative findings | Manual LM Studio measurements |
| Chart source data | `docs/charts/points.csv` | Tidy dataset used by the Vega-Lite specs | CSV-compatible tools, browser chart rendering |
| Chart specs | `docs/charts/*.vl.json` | Vega-Lite chart definitions for throughput and TTFT | Vega, Vega-Lite, Vega-Embed |
| Static site | `docs/index.html`, `docs/charts.html` | GitHub Pages entry point and interactive chart page | Browser, jsDelivr CDN |
| PDF artifact | `docs/Local_LLM_Benchmark_Apple_Silicon.pdf` | Shareable static report | Generated outside this repo |
| Methodology notes | `notes/Test Methodology.md` | Hardware, runtime, sampling, and measurement rules | Manual benchmark process |
| Benchmark prompt | `prompts/coder_dedup_text.txt` | Fixed prompt used across model runs | LM Studio |
| Deployment workflow | `.github/workflows/pages.yml` | Publishes `docs/` to GitHub Pages on `main` pushes | GitHub Actions, GitHub Pages |
| Archive | `src_archives/2026-05-18_housekeeping/` | Stores stale files moved during housekeeping | Git history |

## 4. Runtime Flow

```text
Manual LM Studio benchmark runs
  -> benchmark result tables and tidy chart CSV
  -> Vega-Lite chart specifications
  -> docs/charts.html rendered in browser
  -> GitHub Pages publishes docs/
```

## 5. Data Flow

Benchmark measurements are manually captured from LM Studio warm runs. The canonical tabular results are stored in `benchmarks/results.csv` and mirrored for chart rendering in `docs/charts/points.csv`.

The Vega-Lite specs in `docs/charts/` reference `points.csv` using relative URLs. Because the specs are loaded from `docs/charts/`, the browser resolves those data references against the chart spec location.

The `charts/series_*.csv` files are simplified plotting views kept for analysis or alternate chart generation. The live Pages charts currently use `docs/charts/points.csv`.

## 6. Configuration

There are no runtime environment variables, profiles, feature flags, or secrets. Deployment configuration is limited to `.github/workflows/pages.yml`, which uploads `docs/` as the Pages artifact when `main` is pushed.

The chart page depends on public CDN scripts from jsDelivr:

- Vega 5
- Vega-Lite 5
- Vega-Embed 6

## 7. Testing and SIT

There is no unit test framework because the repository is static documentation and data. Practical SIT consists of:

- validating CSV parsing for benchmark data;
- validating chart JSON parsing;
- verifying chart spec data references exist;
- verifying HTML chart spec references exist;
- serving `docs/` locally and checking that the root page is reachable.

The latest SIT commands and outcomes are recorded in `design/issues-pending-review.md`.

## 8. Deployment / Execution

Local execution:

```bash
python3 -m http.server 8000 --directory docs
```

Production deployment:

1. Push to `main`.
2. `.github/workflows/pages.yml` uploads the `docs/` directory.
3. GitHub Pages serves the static site.

## 9. Governance / Operational Notes

The project is manually curated. Benchmark integrity depends on keeping methodology, prompt, runtime settings, and result tables aligned. No secrets should be committed; `.gitignore` excludes `.env`, Python caches, Node dependencies, and macOS `.DS_Store` files.

Known operational limitations:

- results are hardware-specific to the recorded Apple Silicon environment;
- interactive charts require external CDN access for Vega libraries;
- the PDF artifact appears generated externally and should be regenerated whenever major benchmark data changes.

## 10. Known Gaps

Known gaps and review items are tracked in `design/issues-pending-review.md`.
