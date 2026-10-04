Supplementary results for the fixed-130-byte semantic-assisted LDPC experiment

This supplement provides the experimental settings and numerical results
supporting the reviewer responses. We consider fixed 130-byte payloads,
the WiMAX (1248,1040) LDPC code, BPSK modulation and a real AWGN channel.
The linear SNR is signal power divided by real noise variance, or 2 Es/N0.

The material is organized by the scientific questions addressed. Each
section contains a short explanation of the method and numerical data
that can be viewed directly or used to reproduce the reported tables.

01_data_training/
We describe how source blocks and BP-decoded/clean pairs are constructed,
how training/validation/test data are separated, and how the model is
trained and its checkpoint selected. CSV tables give dataset sizes,
training settings, epoch-by-epoch results and stage-specific random seeds.

02_parameter_studies/
We explain the validation-based selection of posterior temperature T and
semantic-message scale alpha, followed by one-parameter-at-a-time tests.
CSV files provide calibration, validation selection and test sensitivity
results. The sensitivity tables contain BLER, information-bit BER and
ByteER with their corresponding sample counts.

03_error_statistics/
We report SF-BP and BM-BP at 2, 2.5, 3, 3.5 and 4 dB, including their
paired conventional-BP baselines. The two JSON summaries and CSV tables
give transmitted-block counts, errors, uncertainty intervals, stopping
rules, byte errors, exact recovery and syndrome-valid incorrect outputs.

04_resources_latency/
We describe the model-resource and receiver-latency measurements, including
hardware/software, execution settings and timing boundaries. The data give
model size, FLOPs, memory, end-to-end timing statistics, semantic invocation
counts and component timings. Full numerical timing observations are included.

05_app_llr/
We compare conventional BP and SF-BP on the same 50,000 blocks at 2 dB.
The CSV files contain the truth-aligned APP-LLR histograms and terminal
bit/block error statistics for zero, one and six semantic-call budgets.

File formats
TXT files explain methods and results. CSV files contain numerical tables;
JSON files retain detailed aggregate results. The large timing file is
CSV.gz, which decompresses to an ordinary CSV file. Rates are fractions
unless a column explicitly states otherwise. Reviewer question numbers
are mapped to the relevant sections in reviewer_index.csv.

This release contains data and explanatory documentation. Implementation
and training/evaluation/benchmark scripts will be released after acceptance.
SHA256SUMS can be used to check file integrity from this directory.
