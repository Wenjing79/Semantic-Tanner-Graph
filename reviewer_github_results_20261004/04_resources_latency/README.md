# Model resources and receiver latency

[Results overview](../README.md) · [Full methods](README.txt) · [Figure PDF](../figures/latency_calls.pdf)

Receiver latency is measured on an NVIDIA GeForce RTX 3060 (12 GB) and Intel Core i7-12700F using batch-one FP32 inference. All methods receive the same 4,000 held-out blocks and channel realizations at each SNR. Three timing passes reuse these blocks, giving 12,000 timing observations per method/SNR without increasing the number of independent transmissions.

![Mean end-to-end receiver latency and actual mean semantic invocations across SNR.](../figures/latency_calls.png)

**Figure.** Primary end-to-end latency and actual model calls for the dedicated timing cohort at 2, 3, 4, 5 and 6 dB. BM-BP and SF-BP permit six calls, but syndrome-based stopping usually uses fewer. These measurements are separate from the larger [error-rate cohorts](../03_error_statistics/README.md).

## Measured receiver cost

| SNR (dB) | LDPC-BP (ms) | LM-Top1 (ms) | BM-BP (ms) | SF-BP (ms) | SF-BP calls/block |
|---:|---:|---:|---:|---:|---:|
| 2 | 7.259 | 56.411 | 92.449 | 94.569 | 1.13375 |
| 3 | 7.416 | 57.136 | 67.123 | 71.064 | 1.00050 |
| 4 | 7.253 | 56.350 | 65.521 | 68.775 | 1.00000 |
| 5 | 5.385 | 35.481 | 40.835 | 42.337 | 0.61200 |
| 6 | 1.193 | 1.348 | 1.368 | 1.377 | 0.00275 |

At 4 dB, SF-BP takes 68.775 ms/block, approximately 4.97% more than BM-BP. At 6 dB, only 11 of the 4,000 blocks trigger semantic assistance, bringing mean SF-BP latency to 1.377 ms/block.

Timing begins with received symbols and ends with final information-bit decisions, with GPU synchronization at both boundaries. It includes LLR construction, BP, model pipelines, transfers and semantic updates. Channel generation, accuracy evaluation, file writing, model loading and warmup are excluded. Each method/SNR group has 50 separate warmup blocks. Stored standard deviations and percentiles describe timing variability, not confidence intervals. Error counts in this timing cohort are sanity checks, not precise estimates of low BLER.

## Model resources

| Quantity | Measured or counted value |
|---|---:|
| Encoder and byte-head parameters | 218,034,560 |
| FP32 parameter tensor storage | 831.736 MiB |
| Matrix-operation cost per inference | 60.3562 GFLOPs |
| Matrix-operation cost for six inferences | 362.1374 GFLOPs |
| Steady-state peak allocated GPU memory | 878.236 MiB |
| Steady-state peak reserved GPU memory | 950 MiB |

FLOP counts use two operations per multiply-accumulate and cover the dominant model matrix operations. They exclude CPU BP, semantic updates and non-matrix operations. The same resident model is reused across calls. Memory values describe warmed inference and exclude model-loading and training peaks, host RAM and non-PyTorch allocations.

## Numerical records

| Record | Contents |
|---|---|
| [Latency summary](latency_summary.csv) | Means, SDs, medians, P95s, repeat means and call statistics |
| [All timing observations](per_block_latency.csv.gz) | 240,000 compressed CSV records |
| [Semantic-call histogram](semantic_call_histogram.csv) | Counts for zero through six calls, with distinct-block and repeated-observation totals |
| [Resource summary](resource_summary.csv) · [FLOPs by component](flops_by_component.csv) | Parameter, memory and operation counts |
| [Component summary](component_summary.csv) · [Component records](component_timings.csv) | Separate instrumented profiling of 100 blocks per method/SNR |
| [Initialization timing](initialization_summary.csv) | One-time setup costs excluded from block latency |
| [Environment](environment.csv) · [Package versions](environment_packages.csv) | Hardware, software and execution settings |

Component profiling uses a smaller cohort and an instrumented execution path; its totals do not replace the primary latency measurements. The [full methods](README.txt) specify all timing boundaries and resource-counting conventions.
