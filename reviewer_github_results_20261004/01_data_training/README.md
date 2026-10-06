# Data, training and calibration

[Results overview](../README.md) · [Full methods](README.txt) · [Figure PDF](../figures/training_calibration.pdf)

The semantic model learns to recover fixed 130-byte source blocks from conventional BP outputs. Training, validation and test source blocks are separated before channel realizations are generated. The experiment uses the WiMAX (1248,1040) LDPC code, BPSK and real AWGN at 2, 2.5, 3, 3.5 and 4 dB.

![Training and validation cross-entropy across eight epochs, and validation byte negative log-likelihood across posterior temperatures.](../figures/training_calibration.png)

**Figure.** The checkpoint is selected at epoch 6 by minimum logged validation cross-entropy. With its weights frozen, posterior temperature is selected at T = 1.3 by minimum pooled validation byte NLL. The training log averages batch-mean losses; calibration uses byte-weighted NLL, so these are distinct quantities.

## Data and selected model

The corpus contains 248,084 distinct 130-byte blocks after truncation and deduplication. The retained source and pair counts are:

| Split | Retained source blocks | BP-output/clean pairs |
|---|---:|---:|
| Training | 185,418 | 927,090 |
| Validation | 23,183 | 115,915 |
| Test | 23,133 | 115,665 |

Each retained source contributes one pair per SNR. All channel realizations of a source remain in the same split. The source filtering counts and four retained training pairs with no residual errors are recorded in the data table.

The ByT5-small encoder and 256-class byte head are jointly fine-tuned for eight epochs with AdamW, a learning rate of 0.0002, 1,000 warmup steps followed by cosine decay, and BF16 precision. Training and validation batch sizes are 16 and 128. The selected epoch has validation cross-entropy 0.163918 and byte accuracy 95.4054%.

Temperature calibration evaluates 115,915 validation sequences, or 15,068,950 bytes. Changing T from 1 to 1.3 reduces byte NLL from 0.163907 to 0.155437 and 15-bin ECE from 0.014153 to 0.002794. Positive temperature scaling preserves byte argmax predictions. The checkpoint, T = 1.3 and semantic-message scale α = 0.8 are fixed before test evaluation; [parameter selection and sensitivity](../02_parameter_studies/README.md) give the supporting comparisons.

## Numerical records

| Record | Contents |
|---|---|
| [Dataset summary](dataset_summary.csv) | Source filtering, retained counts and pair construction |
| [Training history](training_history.csv) | All eight epochs, losses, accuracies and selected checkpoint |
| [Training settings](training_settings.csv) | Architecture, optimizer, precision and selection settings |
| [Random seeds](random_seeds.csv) | Generation, training, validation and test seeds |
| [Temperature calibration](../02_parameter_studies/validation_temperature.csv) | All 46 temperatures, pooled and per-SNR metrics |

See the [full methods](README.txt) for data preparation and metric aggregation details.
