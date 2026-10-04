Parameter selection and sensitivity

These experiments examine how posterior calibration and semantic-message
strength affect the SF-BP receiver. They use the fixed-130-byte payload
with the (1248,1040), rate-5/6 WiMAX LDPC code over the BPSK-AWGN channel.
Temperature T controls the concentration of the 256-class byte posterior
through softmax(logits/T); alpha scales the semantic messages supplied
to BP. We select the two parameters using held-out validation data, then
examine their sensitivity on the separate test split.

Validation-only selection

The model is trained at T=1. We select the checkpoint with the lowest
validation cross-entropy, freeze its weights, and evaluate temperatures
0.5, 0.6, ..., 5.0. Calibration uses 115,915 validation sequences
(15,068,950 bytes) spanning 2.0, 2.5, 3.0, 3.5, and 4.0 dB. The selection
criterion is global mean byte negative log-likelihood (NLL), using natural
logarithms. Expected calibration error (ECE), computed with 15 confidence
bins, is the frequency-weighted absolute difference between confidence
and byte accuracy; it provides an additional calibration diagnostic.

The selected temperature is T=1.3. Relative to T=1, mean byte NLL falls
from 0.163907 to 0.155437 and ECE from 0.014153 to 0.002794. Byte accuracy
remains 0.954061 because positive temperature scaling preserves the byte
argmax. The temperature table includes all 46 grid points for the pooled
validation data (group ALL) and each of the five SNR groups. The same
global T is used for every SNR.

With T=1.3 fixed, we screen alpha=0.2, 0.4, ..., 1.4 at 2 dB, using a
common prefix of 768 blocks. We then compare alpha=0.6, 0.8, and 1.0 with
the complete receiver at 2.0, 2.5, and 3.0 dB. The respective common-prefix
lengths are 19,968, 164,352, and 1,000,000 blocks. Candidates use the same
ordered transmissions within each SNR. Their equally weighted mean BLERs
across the three SNRs are 0.0033573501976994964, 0.0031470441329179647, and
0.0038430821551242115, selecting alpha=0.8. The alpha=1 setting is the
unscaled semantic-message reference. The checkpoint, T=1.3, and alpha=0.8
are fixed before test evaluation.

One-factor test sensitivity

At 2.0 and 2.5 dB, we vary alpha over 0.4, 0.8, and 1.2 with T=1.3,
then vary T over 0.8, 1.3, and 2.0 with alpha=0.8. All other receiver
settings are held fixed. These test results describe sensitivity around
the validation-selected operating point; neither parameter is reselected.

Within each sweep and SNR, all settings use the same ordered transmissions
and are compared over their common prefix. The alpha sweep uses N=10,240
and 68,096 blocks at 2.0 and 2.5 dB; the temperature sweep uses N=9,216
and 70,656. Both sweeps reuse the same reference run at each SNR. Thus,
the reference BLER differs between the two tables because the evaluated
prefix lengths differ. At 2.0 dB, the reference has 96 errors among 10,240
blocks in the alpha sweep and 85 among 9,216 in the temperature sweep;
at 2.5 dB, it has 58 errors among 68,096 blocks and 64 among 70,656,
respectively.

The validation-selected operating point has the lowest observed BLER
among the three settings in each sweep at both SNRs. For example, at
2.0 dB, changing alpha from 0.8 to 0.4 or 1.2 changes BLER from 0.009375
to 0.01953125 or 0.0140625. These results motivate explicit calibration
and validation-based selection of the semantic-message strength.

Metrics and experiment settings

Each decoding row reports the number of evaluated blocks N, erroneous
blocks E, and BLER=E/N. In the sensitivity tables, information_BER counts
erroneous information bits divided by 1040*N, and ByteER counts erroneous
payload bytes divided by 130*N. All metrics, including mean semantic
calls per block, refer to the same common prefix. BLER_CS95_low/high are
the reported 95% confidence-sequence endpoints, with pointwise coverage
for each configuration and SNR under IID sampling from the fixed test
pool. This interval scope does not give simultaneous coverage of the
whole sweep.

Decoding permits at most 50 BP iterations per invocation and six semantic
calls. Runs target a block-error count with a cap of 1,000,000 blocks and
check stopping at batch boundaries. Coarse validation targets 200 errors
with batches of 64; final validation uses targets 200/200/100 and batches
512/512/1024 at 2.0/2.5/3.0 dB. Test sensitivity targets 200 errors with
batches of 512. Matched-prefix comparisons use the lengths reported in
the tables. The remaining fixed settings are listed in settings.json.

Files

validation_temperature.csv: 276 rows covering all temperatures and groups;
  is_selected_temperature marks the single global choice T=1.3.
validation_alpha_coarse.csv: 7 screening results at 2.0 dB.
validation_alpha_per_snr.csv: 9 final validation results, one per alpha/SNR.
validation_alpha_mean.csv: 3 equal-SNR means used to select alpha.
sensitivity_alpha.csv: 6 test results with T=1.3 fixed.
sensitivity_temperature.csv: 6 test results with alpha=0.8 fixed.
settings.json: parameter grids, selection criteria, and receiver settings.

The CSV files retain the reported numerical precision. The sensitivity
tables include BLER, information BER, and byte error rate for every
evaluated setting.
