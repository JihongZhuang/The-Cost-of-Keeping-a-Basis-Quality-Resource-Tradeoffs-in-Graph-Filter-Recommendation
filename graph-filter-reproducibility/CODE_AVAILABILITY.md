# Code availability

We provide the direct polynomial evaluator, the loader for the pinned Mem-GF baseline, input preparation and verification tools, experiment configurations, and scripts to reconstruct Tables 1–6 and Figures 1–3. We include aggregate results from 44 complete-user runs.

The baseline loader verifies the original Mem-GF source against the pinned commit and file hashes. We obtained these files from the upstream project. [Baseline source and attribution](baselines/README.md)

We use the archived run summaries to reconstruct the reported resource results. The replay command runs the same configuration schedule under its own output and measurement procedure. Per-user rankings and raw run logs remain local to the evaluation. [Reproduction commands](README.md)
