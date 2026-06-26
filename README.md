# Why Two-Column Grid Navigation Stays Flat

Code for the MSc thesis *"Why Two-Column Grid Navigation Stays Flat: User Intent,* 

*Session Depth, and Ads in Mobile Feeds"* (Information Studies — Information
Systems, University of Amsterdam).

The analysis studies how users navigate two-column content grids on the mobile
UGC platform Xiaohongshu (RedNote), using the **Qilin** dataset. It measures
*column engagement* — whether a request's clicks span both columns or stay within
one — and models it against user intent, session depth, and advertising exposure
with a binary logistic regression (HC3 standard errors).

## Repository contents

* `Qilin\_dataset\_analysis.ipynb` — the full, self-contained analysis notebook.
It rebuilds the dataset **from the raw Qilin release** and reproduces every
table and figure in the thesis: the preprocessing summary, SQ1–SQ3, the joint
logistic regression, and the three figures.
* `results/` — the generated outputs, committed for convenience so they can be
viewed without re-running the notebook: the three figures (PNG), every results
table (CSV), and a `summary.txt` of the headline numbers. These are produced by
the final cell of the notebook on a full run.

No raw data files are included (see below); the notebook downloads what it needs.

## Data

The Qilin dataset is **not** redistributed here. The notebook fetches it directly
from its original public release on first run:

> Jia Chen et al. \*Qilin: A Multimodal Information Retrieval Dataset with
> APP-level User Sessions\* (2025). https://arxiv.org/abs/2503.00501
> HuggingFace: `THUIR/Qilin`

On first run the notebook downloads these Qilin configs from HuggingFace and
caches them locally: `search\_train`, `search\_test`, `recommendation\_train`,
`recommendation\_test`, `user\_feat`, and `notes`. This requires internet access
and may take a while (several GB). The built request log is cached as
`qilin\_combined\_sessions.csv` and the commercial-flag table as
`qilin\_notes\_flags.csv`, so subsequent runs are fast.

> \*\*Figure 1 is intentionally not reproducible from raw.\*\* It is a descriptive,
> per-interaction plot generated from `qilin\_spatial\_trajectories.csv`, a derived
> file from earlier processing that is not part of the public Qilin release and is
> not rebuilt by this notebook. Every other table and figure regenerates fully
> from the raw dataset. If the trajectory file is not present, Figure 1 is skipped
> with a message and all other results still run.

## How to run

1. Clone the repo and launch Jupyter **from the repository folder**.
2. Run the first ("Bootstrap") cell once to install dependencies, then
**Cell → Run All**.

The notebook is controlled by a `REBUILD\_FROM\_RAW` flag near the top: leave it
`True` for the first run to build everything from raw; set it to `False`
afterwards to reuse the cached combined log and skip the download.

Alternatively, install dependencies up front:

```
pip install -r requirements.txt
```

## The data pipeline

The combined request log is built from the raw Qilin splits as follows:

1. Load and concatenate the **search** splits (`search\_train` + `search\_test`)
and the **recommendation** splits (`recommendation\_train` +
`recommendation\_test`).
2. Merge each module with the user-feature table (`user\_feat`) on `user\_idx`.
3. Filter **both** modules to mobile platforms (iOS / Android).
4. Concatenate the two filtered modules and label intent by origin
(Search → goal-oriented, Recommendation → exploratory).

This yields the analysis set: **139,927 requests across 87,030 sessions from
11,935 users**, of which **96,835 are multi-click requests** — the unit on which
column engagement is computed. The notebook prints these counts at every stage
so the pipeline is fully transparent.

## Notes on the reported counts

The notebook reproduces the thesis's headline figures exactly (139,927 requests;
96,835 multi-click; 85.4% baseline cross-column engagement; and the SQ1–SQ3 and
regression results). The user and session totals are the raw merged counts; the
analysis treats the two modules as disjoint intent conditions via the module
label rather than deduplicating session identifiers across modules.

Figure 1 is descriptive context counted *per interaction*; the statistical
analysis (Table II onward) is computed on the 96,835 multi-click requests.

