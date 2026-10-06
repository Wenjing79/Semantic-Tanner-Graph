# How decoding improves across iterations

[← Supplement overview](../README.md)

These figures address Reviewer 3, Comment 3: how do the inner BP iterations and repeated language-model invocations improve decoding? The experiment follows the same 50,000 fixed-130-byte source blocks at 2 dB with the WiMAX (1248,1040) LDPC code, posterior temperature T = 1.3, and semantic-message scale α = 0.8. An initial BP stage is followed by up to six ByT5-assisted stages, each with a maximum of 50 inner iterations.

## R3-1 · Error rates throughout decoding

![Information-bit BER and block-error rate across the initial BP stage and six ByT5-assisted stages.](overall_error_evolution.png)

[Vector PDF](overall_error_evolution.pdf) · [Plotted error counts and rates](error_evolution.csv)

Within the first ByT5-assisted stage, the semantic probabilities remain fixed while the information-bit BER falls from 0.01517 to 0.001522 and BLER falls from 0.98986 to 0.06426. As the extrinsic beliefs change, the semantic factors recompute their messages and exchange information with the parity-check constraints. Correcting most bits takes fewer iterations than recovering every bit in a block.

| Decoding point | Inner iteration | Information-bit BER | BLER |
|---|---:|---:|---:|
| Initial BP endpoint | 50 | 0.103705 | 1.00000 |
| First ByT5-assisted stage | 1 | 0.015175 | 0.98986 |
| First ByT5-assisted stage | 5 | 0.003694 | 0.46876 |
| First ByT5-assisted endpoint | 50 | 0.001522 | 0.06426 |
| Sixth ByT5-assisted endpoint | 50 | 0.000244 | 0.00884 |

Further ByT5 invocations refresh the probabilities using the updated decoding estimate. Together with the subsequent BP iterations, they reduce the remaining errors to 442 erroneous blocks out of 50,000. Dashed separators mark a model invocation and BP restart; horizontal positions represent iteration budgets, not elapsed time.

## R3-2 · Message distributions within the first semantic stage

![Four panels show truth-aligned variable-to-semantic, semantic, summed check-node, and APP LLR distributions at inner iterations 1, 2, 5, and 50.](inner_message_densities.png)

[Vector PDF](inner_message_densities.pdf) · [Plotted histogram bins](inner_density_bins.csv) · [Per-iteration statistics](inner_density_statistics.csv)

The curves show how soft information changes while the semantic probabilities are held fixed. Each LLR is aligned with the transmitted bit: a positive value supports the correct bit, and a negative value supports an incorrect decision. From the first iteration to the stage endpoint, the negative fraction falls from 10.3711% to 0.9101% for variable-to-semantic information U, from 1.7006% to 0.8870% for the semantic log-ratio M, and from 48.3503% to 2.5746% for the summed check-node messages. The negative APP fraction falls from 1.5175% to 0.1522%.

These changes include corrections in message direction. Increasing an LLR's magnitude by a positive scale alone would leave its sign unchanged. The concurrent reductions across the four panels are consistent with useful information exchange between semantic and parity-check constraints.

## R3-3 · Message distributions across semantic-call budgets

![Truth-aligned variable-to-semantic and APP LLR distributions for maximum ByT5 invocation budgets from one to six, with insets enlarging the negative-LLR region.](outer_message_densities.png)

[Vector PDF](outer_message_densities.pdf) · [Plotted histogram bins](outer_density_bins.csv) · [Terminal statistics by budget](fixed_cohort_terminal_statistics.csv)

The main density curves overlap substantially. Later invocations primarily reduce the residual negative tail rather than shifting every reliable bit toward greater confidence. Between budgets N = 1 and N = 6, the negative fraction of U decreases from 0.9101% to 0.4025%, and the negative APP fraction decreases from 0.1522% to 0.02436%. The insets enlarge this remaining error region using the same normalization as the main panels.

Every curve uses the same 50,000 blocks. N is the maximum invocation budget.

### What changes when ByT5 refreshes the probabilities?

The following comparison uses the same 3,216 blocks entering the second ByT5 invocation. Evaluating the old and refreshed probabilities with identical semantic-factor inputs U separates the change in the semantic message mapping from changes in the BP inputs.

| Paired metric, first-to-second invocation | Old probabilities | Refreshed probabilities |
|---|---:|---:|
| Mean negative log-probability of the true byte | 0.6095 | 0.3890 |
| Wrong-direction semantic candidate messages at fixed U | 3.8102% | 1.7409% |

The refreshed probabilities assign greater geometric-mean probability to the true bytes and produce fewer wrong-direction candidate messages. This supports the contribution of the probability update at the message level. The subsequent error-rate improvement in R3-1 includes both that update and further BP decoding.

[Paired-refresh statistics for successive invocations](paired_refresh_summary.csv)

<details>
<summary>Measurement details</summary>

- These are empirical probability-density estimates from a finite-length experiment at one SNR, not an infinite-length density-evolution recursion or threshold prediction. Each information-bit distribution contains 50,000 × 1,040 = 52,000,000 observations. The results provide a mechanism study alongside the separate [main error-statistics experiment](../03_error_statistics/README.md).
- Truth alignment uses `(1 − 2c)L` for transmitted bit `c` and recorded LLR `L`. Truth is used only for alignment and error measurement, not for receiver updates or stopping. Negative APP fractions equal the information-bit BER here because the recorded APP distributions contain no zero LLRs. Marginal message distributions do not specify how errors cluster within blocks; BLER is computed from block-error counts.
- Inner-stage curves retain the actual terminal messages of blocks that stop early. Outer-budget curves also retain stopped states, including syndrome-valid incorrect outputs, so every curve keeps the same population. Budget slots do not represent the amount of computation actually performed by every block.
- The displayed U is `U_pre_info`, the channel LLR plus the sum of check-node messages before that iteration's U clipping. The displayed M is `Mraw_info`, the semantic log-ratio before gating, clipping, α scaling, and damping; it is not the actual injected semantic message. The sum of R uses the actual check-node messages. The displayed APP is `APP_pre_info`, the channel LLR plus the check-node sum and actual injected semantic message before that iteration's APP clipping. These are same-step observations; all earlier receiver clipping remains active.
- The density figures preserve the full occupied histogram support. Original width-0.5 bins are merged by exact addition into width-1 bins, and lines connect bin centers. No kernel-density fitting or tail trimming is applied. Insets retain the denominator of the full population; they are not conditional densities. Negative fractions are obtained from exact saved counts rather than by integrating the drawn lines.
- The paired-refresh population is conditional on reaching the next invocation. For example, the 3,216 blocks entering call 2 differ from the 3,213 erroneous blocks at the end of call 1: stopping is governed by parity checks, not by access to the transmitted payload. Each subsequent row in `paired_refresh_summary.csv` uses its own `paired_frames` cohort. Its before/after-stage BER and BLER columns describe that conditional cohort; R3-1 reports all 50,000 blocks. The fixed-U comparison isolates the effect of changing probabilities on candidate messages, not a complete counterfactual decoding trajectory.

</details>
