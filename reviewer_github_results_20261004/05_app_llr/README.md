# APP-LLR distributions

[Results overview](../README.md) · [Full methods](README.txt) · [Figure PDF](../figures/bp_sfbp_app_densities.pdf)

Conventional BP and SF-BP with one- and six-call budgets are compared on the same 50,000 received blocks at 2 dB. Each distribution contains 52,000,000 information-bit APP-LLRs from fixed 130-byte payloads and the WiMAX (1248,1040) LDPC code.

![Truth-aligned APP-LLR distributions for conventional BP and SF-BP with one and six semantic-call budgets at 2 dB.](../figures/bp_sfbp_app_densities.png)

**Figure.** Truth-aligned APP-LLRs, defined as (1 − 2c)L for transmitted bit c. Positive values support the correct bit. Ground truth is used only for this offline alignment. The histograms use width-0.5 bins over [−120, 120), normalized by the full 52,000,000-bit cohort, with no smoothing, underflow or overflow.

## Terminal statistics

| Receiver | Maximum semantic calls | Negative APP mass | Incorrect bits | Incorrect blocks / 50,000 | BLER |
|---|---:|---:|---:|---:|---:|
| LDPC-BP | 0 | 10.3705% | 5,392,635 | 50,000 | 1.00000 |
| SF-BP | 1 | 0.152173% | 79,130 | 3,213 | 0.06426 |
| SF-BP | 6 | 0.0243558% | 12,665 | 442 | 0.00884 |

Semantic assistance shifts the APP distribution toward support for correct decisions. No exactly-zero APP-LLRs occur in these distributions. Block errors are measured separately from decoded payloads; the histogram describes marginal soft information.

Conventional BP is observed at its 50-iteration budget position. SF-BP is observed at the terminal state for the specified call budget, retaining terminal values for blocks that stop early. Recorded stage-entry and stopped-terminal statistics are combined with boundary stages reconstructed from cached model probabilities, as described in the full methods. This fixed-size study uses T = 1.3, α = 0.8 and seed 20260916 and is separate from the main Monte-Carlo reliability runs.

## Numerical records

| Record | Contents |
|---|---|
| [APP density bins](app_density_bins.csv) | 1,440 rows: 480 bins per call budget, exact counts and density normalization |
| [Terminal metrics](terminal_metrics.csv) | Bit/block errors, BER, BLER, negative mass, zero counts and mean aligned APP |

See the [full methods](README.txt) for observation and reconstruction details, and [reliability and exact recovery](../03_error_statistics/README.md) for the main BLER study.
