# Guide to the supporting evidence

[Project overview](../README.md) · [Results index](README.md) · [Figure gallery](FIGURES.md) · [CSV index](reviewer_index.csv)

This guide connects the experimental topics raised during review to the public results. It summarizes the evidence without reproducing the full manuscript or review correspondence.

| Review reference | Topic | Evidence and entry point |
| :--- | :--- | :--- |
| Reviewer 1 · Comment 2 | Block reliability and soft-information distributions | [Counts and uncertainty](03_error_statistics/README.md); [BP/SF-BP APP figure](05_app_llr/README.md) |
| Reviewer 1 · Comment 3 | Dataset and model training | [Splits, training history and seeds](01_data_training/README.md); [validation selection](02_parameter_studies/README.md) |
| Reviewer 1 · Comment 4 | Main coding and experimental configuration | [Data and receiver settings](01_data_training/README.md); [per-SNR statistics](03_error_statistics/README.md) |
| Reviewer 1 · Comment 6 | Parameter sensitivity | [Alpha and temperature test sweeps](02_parameter_studies/README.md) |
| Reviewer 1 · Comment 7 | Calibration and parameter selection | [Full temperature grid and alpha validation results](02_parameter_studies/README.md) |
| Reviewer 1 · Comment 8 | Byte errors and exact source-block recovery | [ByteER, exact recovery and error counts](03_error_statistics/README.md) |
| Reviewer 1 · Comment 9 | Model resources and measurement procedure | [Parameters, FLOPs, memory and environment](04_resources_latency/README.md) |
| Reviewer 1 · Comment 10 | Latency and timing protocol | [Summary and complete per-block timing](04_resources_latency/README.md) |
| Reviewer 2 · Comment 4 | Calibration and sensitivity | [Validation selection and independent test sweeps](02_parameter_studies/README.md) |
| Reviewer 2 · Comment 6 | Error counts, confidence sequences, and syndrome-valid errors | [Error statistics and stopping rules](03_error_statistics/README.md) |
| Reviewer 2 · Comment 7 | Actual invocations and component timing | [Call histograms and profiling records](04_resources_latency/README.md) |
| Reviewer 2 · Comment 9 | Reproducibility of construction, training and selection | [Dataset and training](01_data_training/README.md); [parameter-selection settings](02_parameter_studies/README.md) |
| Reviewer 3 · Comment 3 | Inner iterations, outer invocations, and refreshed probabilities | [R3-1, R3-2, R3-3, plus paired refresh measurements](06_decoding_dynamics/README.md) |

## Three direct entry points for Reviewer 3

1. **Within-stage improvement:** [R3-1 trajectories](06_decoding_dynamics/overall_error_evolution.pdf) and [R3-2 message densities](06_decoding_dynamics/inner_message_densities.pdf) show how message passing improves the decoder while ByT5 probabilities remain fixed.
2. **Between-stage improvement:** [R3-3 densities](06_decoding_dynamics/outer_message_densities.pdf) compare maximum semantic-call budgets over the same full cohort.
3. **Effect of refreshing the probabilities:** [Paired refresh CSV](06_decoding_dynamics/paired_refresh_summary.csv) compares old and new probabilities at identical semantic-factor inputs for blocks entering the next call. This isolates the message-level change, not a complete receiver-level causal ablation.

The release includes settings, results, figures, and numerical timing observations. Receiver, training, evaluation, and benchmark scripts remain outside the current public release and are planned for release after acceptance.
