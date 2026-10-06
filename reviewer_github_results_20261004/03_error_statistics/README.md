# Reliability and exact recovery

[Results overview](../README.md) · [Full methods](README.txt) · [Figure PDF](../figures/main_reliability.pdf)

SF-BP and BM-BP use the same frozen semantic model, T = 1.3, α = 0.8, a maximum of six semantic calls and 50 BP iterations per invocation. Evaluation uses fixed 130-byte payloads, the WiMAX (1248,1040) LDPC code and BPSK over real AWGN.

![Block and byte error rates of SF-BP, BM-BP and conventional BP across 2 to 4 dB.](../figures/main_reliability.png)

**Figure.** BLER and ByteER from the released aggregate counts. The conventional-BP curve uses the transmissions paired with SF-BP. The CSV also retains the separate BP cohort paired with BM-BP. BLER intervals are 95% beta-binomial mixture confidence sequences for each receiver and SNR; no ByteER intervals are supplied.

## Observed block errors

| SNR (dB) | SF-BP errors / blocks | SF-BP BLER | BM-BP errors / blocks | BM-BP BLER |
|---:|---:|---:|---:|---:|
| 2.0 | 200 / 20,992 | 9.52744 × 10⁻³ | 360 / 12,288 | 2.92969 × 10⁻² |
| 2.5 | 200 / 203,776 | 9.81470 × 10⁻⁴ | 303 / 81,920 | 3.69873 × 10⁻³ |
| 3.0 | 106 / 1,000,000 | 1.06000 × 10⁻⁴ | 202 / 434,176 | 4.65249 × 10⁻⁴ |
| 3.5 | 93 / 3,700,000 | 2.51351 × 10⁻⁵ | 100 / 1,208,320 | 8.27595 × 10⁻⁵ |
| 4.0 | 50 / 7,372,800 | 6.78168 × 10⁻⁶ | 50 / 1,835,008 | 2.72478 × 10⁻⁵ |

At 4 dB, the observed SF-BP BLER is about one-quarter of BM-BP's. The corresponding ByteER values are 7.35552 × 10⁻⁷ and 3.48353 × 10⁻⁶. Conventional BP on the SF-BP transmissions has BLER 0.999969.

## Metrics and sampling

A block error means any incorrect information bit; syndrome-valid incorrect outputs remain errors. Information BER divides the incorrect-bit count by 1,040 times the block count. ByteER compares raw payload bytes and divides their mismatch count by 130 times the block count. Exact recovery requires all 130 bytes to match and equals 1 − BLER.

Source blocks are sampled with replacement from the held-out test pool with independent AWGN. Runs stop at a target error count or sample limit, checked at completed batch boundaries. SF-BP reaches its sample limit at 3 and 3.5 dB; the other SF-BP points and all BM-BP points reach their error targets. Confidence-sequence coverage accommodates this stopping rule under the stated IID assumptions and applies separately to each receiver and SNR.

Conventional BP is paired with its assisted receiver within each run. The SF-BP and BM-BP runs at 3.5 and 4 dB use different seeds and are not transmission-paired with each other. Each high-SNR point comes from its designated completed run; earlier run counts are not added.

## Numerical records

| Record | Contents |
|---|---|
| [Per-SNR metrics](per_snr_metrics.csv) | 20 rows of block, bit and byte counts; uncertainty; exact recovery; syndrome-valid errors; stopping and provenance |
| [SF-BP aggregate summary](sf_bp_results_summary_public.json) | Detailed results and run-specific settings for SF-BP and its BP baseline |
| [BM-BP aggregate summary](bm_bp_results_summary_public.json) | Detailed results and run-specific settings for BM-BP and its BP baseline |
| [Semantic calls by SNR](semantic_calls_by_snr.csv) | Invocation and iteration averages from these error-test cohorts |

Dedicated receiver timings are reported in [resources and latency](../04_resources_latency/README.md). The [full methods](README.txt) give error targets, sample limits and the confidence-sequence definition.
