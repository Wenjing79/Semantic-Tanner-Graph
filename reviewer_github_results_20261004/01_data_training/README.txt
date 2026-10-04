DATASET AND TRAINING
Main WiMAX (1248,1040), rate-5/6 experiment with fixed 130-byte payloads

We constructed the source corpus from 248,125 Wikipedia sentences containing
130--137 ASCII bytes. We truncated each sentence to its first 130 bytes and
removed 41 duplicate payloads, leaving 248,084 distinct source blocks. We split
these blocks approximately 80/10/10 into training, validation and test sets
before constructing BP-decoded/clean pairs. All channel realizations of a source
block remained in the same split. Validation and test blocks ending in ASCII
whitespace were excluded after this split. The retained training, validation
and test counts were 185,418, 23,183 and 23,133 blocks, respectively; the
training count is calculated as 927,090 retained pairs divided by five SNRs.
The before-filter, excluded and retained counts are given in dataset_summary.csv.

For each source block, we generated one BP-decoded/clean pair at each of
2.0, 2.5, 3.0, 3.5 and 4.0 dB using LDPC encoding, BPSK transmission over real
AWGN and conventional BP decoding with at most 50 iterations. The BP-decoded
130-byte sequence was the model input and the clean transmitted sequence was
the supervised target. The resulting datasets contained 927,090 training,
115,915 validation and 115,665 test pairs. Four training pairs had no residual
errors and were retained. SNR is signal power divided by real-noise variance,
equivalent to 2 Es/N0 in linear units.

We jointly fine-tuned the pretrained ByT5-small encoder and a position-wise
linear classifier over 256 byte values. The encoder has 12 layers, hidden
dimension 1,472 and six attention heads. Training used mean byte cross-entropy
on unscaled logits (T=1), AdamW, learning rate 0.0002, 1,000 warm-up steps
followed by cosine decay, and BF16 mixed precision. We trained for eight epochs
with training/validation batch sizes 16/128 and evaluated all 115,915 validation
pairs after each epoch without parameter updates. We selected epoch 6 by the
minimum logged validation cross-entropy, 0.16391783766859677; its validation
byte accuracy was 0.9540542639002717. training_settings.csv gives the settings,
and training_history.csv provides all eight epochs. The logged validation loss
averages batch-mean cross-entropies and is distinct from the byte-weighted NLL
used for subsequent temperature calibration. Epoch times describe training.

After freezing the selected checkpoint, we calibrated T over 0.5--5.0 in
0.1 steps using validation byte NLL, selecting T=1.3. With T fixed, we screened
alpha=0.2, 0.4, ..., 1.4 at 2.0 dB, then compared alpha=0.6, 0.8 and 1.0 with
the complete SF-BP receiver at 2.0, 2.5 and 3.0 dB. We selected alpha=0.8 using
the equally weighted mean validation BLER on the common replay prefix at each
SNR. The checkpoint, T and alpha were fixed before final test evaluation;
the test set was reserved for evaluation. The parameter-selection supplement
contains the numerical calibration and alpha comparisons.

random_seeds.csv lists the generation, training, selection and test seeds.
The selected SF-BP and BM-BP test runs use seed 20260602 at 2.0--3.0 dB;
at 3.5--4.0 dB, their seeds are 20260915 and 20260919, respectively.
Within each assisted-receiver run, conventional BP uses the same transmissions.

Files: dataset_summary.csv; training_settings.csv; training_history.csv;
random_seeds.csv.
