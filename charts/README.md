# charts/

Datasets for plotting benchmark results.

## Files
- `points.csv` — canonical tidy dataset (one row per run, includes tok/sec, TTFT, tokens).
- `series_toksec_vs_context.csv` — simplified view for plotting tok/sec vs context.
- `series_ttft_vs_context.csv` — simplified view for plotting TTFT vs context.

## Notes
All values are from second-run (warm) measurements in LM Studio on Apple Silicon (Metal).
