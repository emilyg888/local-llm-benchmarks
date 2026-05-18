# Issues Pending Review

## Summary

| ID | Severity | Area | Issue | Recommended action | Status |
|---|---|---|---|---|---|
| ISSUE-001 | Medium | CI/CD | GitHub Pages relies on browser-loaded CDN scripts, so offline or CDN-blocked environments cannot render charts. | Decide whether to vendor chart libraries or keep the CDN dependency documented. | Pending review |
| ISSUE-002 | Low | Docs | The PDF report is generated outside the repo and no rebuild workflow is documented. | Add PDF generation steps if the PDF remains a supported artifact. | Pending review |
| ISSUE-003 | Low | Data | `benchmarks/results.csv` and `docs/charts/points.csv` duplicate benchmark measurements in slightly different schemas. | Choose one canonical data file or document the sync process. | Pending review |

## SIT Results

| Command | Result | Notes |
|---|---|---|
| `python3 -c "import csv, pathlib; files=[pathlib.Path('benchmarks/results.csv'), pathlib.Path('docs/charts/points.csv'), pathlib.Path('charts/series_toksec_vs_context.csv'), pathlib.Path('charts/series_ttft_vs_context.csv')]; [print(f'{p}: {sum(1 for _ in csv.DictReader(p.open()))} rows') for p in files]"` | Passed | CSV files parsed successfully. |
| `python3 -c "import json, pathlib; [json.load(p.open()) for p in pathlib.Path('docs/charts').glob('*.vl.json')]; print('chart specs ok')"` | Passed | Vega-Lite JSON specs parsed successfully. |
| `python3 -c "import json, pathlib; base=pathlib.Path('docs/charts'); missing=[]; [missing.append(str(base / json.load(p.open())['data']['url'])) for p in base.glob('*.vl.json') if not (base / json.load(p.open())['data']['url']).exists()]; assert not missing, missing; print('chart data refs ok')"` | Passed | Chart spec data references resolve locally. |
| `python3 -c "from html.parser import HTMLParser; from pathlib import Path; html=Path('docs/charts.html').read_text(); assert 'charts/toksec_vs_context.vl.json' in html and 'charts/ttft_vs_context.vl.json' in html; print('html chart refs ok')"` | Passed | Static chart page references expected specs. |
| `python3 -m http.server 8765 --directory docs` plus `curl -s -I http://127.0.0.1:8765/` | Passed | Local static server returned `HTTP/1.0 200 OK`. |

## Archived Code Review

| Original path | Archived path | Reason | Review needed? |
|---|---|---|---|
| `benchmarks/Repo-Structure.md` | `src_archives/2026-05-18_housekeeping/benchmarks/Repo-Structure.md` | Stale suggested structure referenced files that do not exist and is superseded by `README.md` plus `design/architecture.md`. | No |

## Detailed Issues

### ISSUE-001 - CDN-dependent interactive charts

- Severity: Medium
- Area: CI/CD
- Evidence: `docs/charts.html` loads Vega, Vega-Lite, and Vega-Embed from `https://cdn.jsdelivr.net/`.
- Impact: The GitHub Pages chart page depends on client network access to jsDelivr. Offline demos or networks blocking CDN scripts will show an incomplete page.
- Recommended action: Decide whether the lightweight CDN dependency is acceptable or vendor static chart dependencies into `docs/`.
- Status: Pending review

### ISSUE-002 - PDF generation process is not documented

- Severity: Low
- Area: Docs
- Evidence: `docs/Local_LLM_Benchmark_Apple_Silicon.pdf` is present, but no command or source pipeline explains how to regenerate it.
- Impact: Future benchmark updates may leave the PDF stale.
- Recommended action: Add a documented PDF generation workflow or mark the PDF as a manually maintained export.
- Status: Pending review

### ISSUE-003 - Duplicate benchmark data schemas

- Severity: Low
- Area: Data
- Evidence: `benchmarks/results.csv` and `docs/charts/points.csv` contain the same six benchmark runs with different columns.
- Impact: Manual updates can drift if one file changes without the other.
- Recommended action: Choose one canonical source and generate the second file, or document the manual sync rule.
- Status: Pending review
