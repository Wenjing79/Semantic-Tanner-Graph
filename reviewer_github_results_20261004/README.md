# Experimental results

**Semantic-factor belief propagation for fixed 130-byte payloads.**

[Project overview](../README.md) · [Figure gallery](FIGURES.md) · [Reviewer guide](REVIEWER_GUIDE.md) · [Figure sources](figure_sources.json)

This collection brings together the numerical evidence, experimental settings, and response figures for SF-BP. Start with a plot, follow its interpretation, then download the exact CSV or JSON values. The original data filenames remain unchanged.

![Joint semantic-factor and LDPC decoding overview.](figures/receiver_overview.svg)

## Six views of the experiment

| Section | What it explains | Principal data |
| :--- | :--- | :--- |
| **[01 · Data and training](01_data_training/README.md)** | Source construction, split separation, supervised pairs, checkpoint selection, random seeds | Dataset sizes, training settings, eight-epoch history |
| **[02 · Parameter studies](02_parameter_studies/README.md)** | Validation-only selection of T and α, followed by test sensitivity | All 46 temperatures, alpha screening, per-SNR validation and test sweeps |
| **[03 · Error statistics](03_error_statistics/README.md)** | Reliability and exact source-block recovery | SF-BP/BM-BP JSONs; counts, BLER, BER, ByteER, confidence sequences, stopping rules |
| **[04 · Resources and latency](04_resources_latency/README.md)** | Model cost, end-to-end latency, actual semantic calls | Hardware/software, memory/FLOPs, 240,000 timing observations, component records |
| **[05 · APP-LLR distributions](05_app_llr/README.md)** | How semantic assistance changes posterior soft information | Same-cohort BP and one-/six-call SF-BP histograms and terminal errors |
| **[06 · Decoding dynamics](06_decoding_dynamics/README.md)** | How inner iterations and later invocations reduce errors | Reviewer 3 Figures R3-1–R3-3; trajectories, densities and paired probability refresh |

## Main reliability result

![BLER and ByteER on the main 2–4 dB evaluation range.](figures/main_reliability.png)

**At 4 dB:** SF-BP has 50 block errors in 7,372,800 transmissions (BLER 6.78 × 10⁻⁶), compared with 50 in 1,835,008 for BM-BP (2.72 × 10⁻⁵). Both use the same ByT5 model and permit at most six semantic calls per block. Error bars are per-point 95% confidence sequences; ByteER has no interval shown.

[Read the analysis](03_error_statistics/README.md) · [Download PDF](figures/main_reliability.pdf) · [Download CSV](03_error_statistics/per_snr_metrics.csv)

## Iteration-level evidence

![BER and BLER evolution through BP and successive semantic stages on the 50,000-block mechanism cohort.](06_decoding_dynamics/overall_error_evolution.png)

The mechanism study uses a separate, fixed cohort of 50,000 blocks at 2 dB. It shows both improvement while the semantic probabilities stay fixed and further improvement after later model invocations. Message-distribution plots explain how these two processes affect the decoder's soft information.

[Explore all three figures](06_decoding_dynamics/README.md) · [Download PDF](06_decoding_dynamics/overall_error_evolution.pdf) · [Download trajectory CSV](06_decoding_dynamics/error_evolution.csv)

## Keep the experimental cohorts distinct

| Study | Sampling / measurement design | Interpretation |
| :--- | :--- | :--- |
| Main reliability | SNR-specific error targets and sample caps | Precise error counts, rates, and stopping-aware confidence sequences |
| Parameter sensitivity | Common transmission prefix within each sweep and SNR | Change one parameter at a time; parameters are not selected on these test results |
| APP and decoding dynamics | Same 50,000 blocks at 2 dB | Finite-length empirical distributions and stage-wise decoding trajectories |
| Primary latency | 4,000 distinct received blocks per SNR, timed three times | Runtime observations, not 12,000 independent transmissions |
| Component profiling | Separate instrumented subset, 100 blocks per method/SNR | Component costs; not a replacement for primary latency |

All SNRs use the signal-power / real-noise-variance convention, equivalent to 2 Eₛ/N₀ in linear units. A correct block means all 130 payload bytes match; a zero syndrome alone does not establish correct recovery.

## Files and reproducibility

- **Markdown** pages explain the results with embedded figures and linked data.
- **PNG** files provide immediate previews; **PDF** files preserve vector figures for closer inspection.
- **CSV** tables provide exact numerical values. **JSON** summaries retain detailed aggregate statistics and experimental settings.
- **CSV.gz** stores the full per-block timing table without discarding observations.
- Original **TXT** methods notes remain available in each of the five original sections.
- [Figure provenance](figure_sources.json) maps the figures to their numerical inputs; [SHA256SUMS](SHA256SUMS) verifies files in this directory.

This release contains results and explanatory material, not receiver, training, evaluation, or benchmark implementations. These scripts are planned for release after acceptance. Model checkpoints and source-text datasets are not included here.
