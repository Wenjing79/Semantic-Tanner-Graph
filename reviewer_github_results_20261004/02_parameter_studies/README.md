# Parameter selection and sensitivity

[Results overview](../README.md) · [Full methods](README.txt) · [Figure PDF](../figures/parameter_sensitivity.pdf)

Posterior temperature T controls byte-posterior concentration, and semantic-message scale α controls the strength of the messages supplied to BP. Both are selected on validation data before the separate test sensitivity study.

![Test BLER as semantic-message scale and posterior temperature vary at 2 and 2.5 dB.](../figures/parameter_sensitivity.png)

**Figure.** One-factor test sweeps around the validation-selected setting, T = 1.3 and α = 0.8. The α sweep holds T fixed; the T sweep holds α fixed. Each SNR and sweep uses a common transmission prefix across its three settings. Intervals are the supplied 95% confidence-sequence endpoints for each configuration and SNR, without simultaneous coverage across the sweep.

## Validation selection

The frozen epoch-6 checkpoint is calibrated on 115,915 validation sequences. The minimum pooled byte NLL across T = 0.5, 0.6, …, 5.0 occurs at T = 1.3. See the [training and calibration figure](../01_data_training/README.md).

With T fixed, an initial α screen at 2 dB is followed by complete-receiver comparisons at 2, 2.5 and 3 dB. Candidates share 19,968, 164,352 and 1,000,000 transmissions, respectively. The selection criterion weights the three SNRs equally:

| α | Equal-SNR mean validation BLER |
|---:|---:|
| 0.6 | 0.00335735 |
| 0.8 — selected | 0.00314704 |
| 1.0 | 0.00384308 |

## Test sensitivity

The selected setting has the lowest observed BLER among the three tested values in each sweep at both SNRs. At 2 dB, the α sweep gives BLER 0.0195313, 0.0093750 and 0.0140625 for α = 0.4, 0.8 and 1.2. These comparisons describe sensitivity; they do not reselect either parameter on test data.

| Sweep | Fixed setting | Blocks at 2 dB | Blocks at 2.5 dB |
|---|---|---:|---:|
| α = 0.4, 0.8, 1.2 | T = 1.3 | 10,240 | 68,096 |
| T = 0.8, 1.3, 2.0 | α = 0.8 | 9,216 | 70,656 |

The two sweeps reuse the same reference run at each SNR but evaluate different prefix lengths. Their reference BLERs therefore differ: for example, 96/10,240 and 85/9,216 at 2 dB. Comparisons should use the reference from the corresponding sweep.

## Numerical records

| Record | Contents |
|---|---|
| [Temperature calibration](validation_temperature.csv) | Pooled and per-SNR NLL, ECE and byte accuracy |
| [Coarse α screen](validation_alpha_coarse.csv) | Seven α values at 2 dB |
| [Final α comparison](validation_alpha_per_snr.csv) | Counts and BLER at each validation SNR |
| [Equal-SNR α means](validation_alpha_mean.csv) | The three values used for selection |
| [Test α sensitivity](sensitivity_alpha.csv) | BLER, information BER, ByteER, intervals and call counts |
| [Test temperature sensitivity](sensitivity_temperature.csv) | Corresponding metrics for the T sweep |
| [Experiment settings](settings.json) | Grids, selection criteria and fixed receiver settings |

All decoding comparisons use at most six semantic calls and 50 BP iterations per invocation. The [full methods](README.txt) specify stopping and common-prefix construction.
