# The Cost of Keeping a Basis: Quality–Resource Tradeoffs in Graph-Filter Recommendation

We studied the cost of retaining a Lanczos basis when a recommendation request evaluates one fixed, low-degree graph filter. We compared the released Mem-GF predictor with direct polynomial recurrence that applies the implicit graph operator and accumulates each weighted response as it is computed. The routes use the same graph, filter coefficients, and evaluation criteria. They produce different scores, so we measured resource use alongside changes in recommendation quality.

We collected 30 controlled and 14 exploratory complete-user CPU runs on Yelp2018 and Amazon-book. The matched-batch (64) comparison used five independent runs per route on each dataset.

| Dataset | Median runtime reduction | Peak memory reduction | Recall@20 retained |
| --- | ---: | ---: | ---: |
| Yelp2018 | 20.03% | 24.78% | 98.58% |
| Amazon-book | 24.99% | 18.06% | 99.74% |

On Yelp2018, Recall@20 decreased by 0.10 percentage points. On Amazon-book, the released route at batch size 32 used less peak memory than the direct route at batch size 64, which still finished sooner. The final-factor screening result is a theoretical bound on edge work. The measured runs evaluated both complete scoring routes.

## Set up the environment

We used Python 3.12 with CPU PyTorch. The measurements used single precision, four Torch compute threads, and one inter-op thread on a Windows Intel CPU. The full protocol is in [configs/formal_environment.json](configs/formal_environment.json).

Create and activate a Python 3.12 environment, then run the commands below from the repository root.

```sh
python -m venv .venv
```

Activate with `.venv\Scripts\Activate.ps1` in Windows PowerShell or `source .venv/bin/activate` on Linux.

```sh
python -m pip install -r requirements.txt
python scripts/download_reference.py
python main.py --author-source external/memgf --self-check --threads 1
```

We pin the upstream Mem-GF source and verify its SHA-256 hashes. The self-check uses synthetic matrices to verify scoring, sparse multiplication counts, and ranking metrics. [Baseline source and attribution](baselines/README.md)

## Prepare the data

We used the published LightGCN interaction splits. Obtain the four `train.txt` and `test.txt` files described in [DATA_AVAILABILITY.md](DATA_AVAILABILITY.md) and place them under `data/lightgcn_text/yelp2018/` and `data/lightgcn_text/amazon-book/`.

```sh
python prepare_data.py --raw-root data/lightgcn_text --output data/processed
python scripts/verify_inputs.py --data-root data/processed --author-source external/memgf
```

The preparation script checks the source text and preserves the split and identifiers. The verification script checks the prepared matrices and the pinned reference files.

## Evaluate the full catalog

```sh
python main.py --author-source external/memgf --train data/processed/yelp2018/R_train_full_official.npz --test data/processed/yelp2018/R_test.npz --config yelp_f5 --route streaming_direct --batch-size 64 --threads 4 --output runs/yelp_direct
python main.py --author-source external/memgf --train data/processed/amazon-book/R_train_full_official.npz --test data/processed/amazon-book/R_test.npz --config amazon_f2 --route streaming_direct --batch-size 64 --threads 4 --output runs/amazon_direct
```

For the released baseline, repeat each command with `--route author_snapshot` and a new output directory, such as `runs/yelp_released`. We mask training interactions and break score ties by ascending item ID. We compute Recall and NDCG at cutoffs 10 and 20 for each user, assign zero to users with empty test sets, and average over all users. To recompute quality differences and Top20 set agreement from completed runs, use:

```sh
python experiments/compare_quality.py runs/yelp_released runs/yelp_direct
```

## Replay the timing schedule

```sh
python experiments/run_schedule.py --data-root data/processed --author-source external/memgf --output runs/schedule --dry-run
```

Remove `--dry-run` to replay all 44 runs. Use `benchmark.py` in place of `main.py` for a single timed run. The replay measures wall time from process launch to exit and tracks peak resident memory. We computed the paper's resource results from the original run summaries. New timings reflect the replay procedure, hardware, and system load.

## Rebuild the paper's tables and figures

```sh
python experiments/build_tables.py --output runs/tables
python experiments/plot_figures.py --output runs/figures
```

We rebuild Tables 1–6 from 44 archived run summaries and the quality statistics in [results/](results/). The figure script builds the three submission figures from the archived plot coordinates, run values, and paired quality statistics. It uses Arial and Cambria Math from the Windows font directory by default. On other systems, pass `--font-dir` with a directory containing `arial.ttf`, `arialbd.ttf`, and `cambria.ttc`. Use a fresh output directory for each command.

[Code availability](CODE_AVAILABILITY.md) · [Data availability](DATA_AVAILABILITY.md)
