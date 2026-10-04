Block-error statistics, byte errors and exact recovery

To quantify decoding reliability, we evaluate SF-BP and BM-BP using fixed
130-byte payloads and the WiMAX (1248,1040) LDPC code over BPSK/AWGN.
Both receivers use the same frozen ByT5 model, T=1.3, alpha=0.8, at most
six semantic invocations and at most 50 BP iterations per decoding run.
At each SNR, source blocks are sampled with replacement from the held-out
test pool and transmitted with independent AWGN realizations.

We count a block error whenever the decoded 1040-bit information block
differs from the transmitted block. A zero syndrome is a decoding stopping
condition, not the definition of a correct block: syndrome-valid incorrect
outputs are included in the error count. Ground truth is used to measure
performance, not to guide receiver updates or per-block termination.

For N transmitted blocks and E block errors, BLER=E/N. To describe the
extent of the residual corruption, we also report information-bit BER
and ByteER, using denominators 1040*N and 130*N, respectively. Raw bytes
are compared before conversion to display text. Exact recovery requires
all 130 transmitted bytes to match, so its rate is (N-E)/N=1-BLER.
The frequency of syndrome-valid incorrect outputs is U/N, where U is their
count. All rate columns in the accompanying CSV are fractions.

Monte-Carlo stopping and uncertainty
For SNRs 2, 2.5, 3, 3.5 and 4 dB, the SF-BP error targets are
200,200,200,100,50 and the sample limits are 1e6,1e6,1e6,3.7e6,2e7.
The BM-BP error targets are 300,300,200,100,50, with sample limits
1e6,1e6,1e6,2e7,2e7. Stopping is checked after each completed batch;
error-count overshoot is retained. SF-BP reaches the sample limit at
3 and 3.5 dB, and reaches its error target at the other SNRs. BM-BP
reaches its error target at all five SNRs.

We report 95% beta-binomial mixture confidence sequences with a fixed
Beta(1/2,1/2) mixture. Under IID Bernoulli block errors for a fixed receiver
and source distribution, their time-uniform coverage accommodates the
error-target stopping rule. Coverage applies separately to each receiver
and SNR, rather than simultaneously to all plotted curves.

Results and paired comparisons
At 4 dB, SF-BP produces 50 errors in 7,372,800 blocks, giving BLER
6.781684027777778e-6. BM-BP produces 50 errors in 1,835,008 blocks,
giving BLER 2.7247837611607144e-5. Their ByteER values are approximately
7.3555e-7 and 3.4835e-6. These measurements distinguish strict bytewise
recovery from semantic similarity.

Within each run, conventional BP is evaluated on the same received
transmissions as its semantic receiver. The CSV therefore has two BP
cohorts, one paired with SF-BP and one with BM-BP. SF-BP and BM-BP high-SNR
runs use different base seeds; their observations are not mutually paired.
The results retain the original 2--3 dB runs and use the designated RTX5090
runs at 3.5/4 dB, without adding the older high-SNR counts. Run-specific
seeds and environments are recorded in the JSON source_runs entries.

Files
sf_bp_results_summary_public.json / bm_bp_results_summary_public.json:
  Detailed aggregate statistics for the five SNRs and paired BP baselines.
per_snr_metrics.csv:
  Twenty rows of counts, BLER/BER/ByteER, exact recovery, uncertainty,
  syndrome-valid errors, stopping rules and run-specific seeds.
semantic_calls_by_snr.csv:
  Invocation and iteration averages for the ten semantic receiver/SNR
  points. These are the error-test cohorts; dedicated latency measurements
  are presented separately in ../04_resources_latency/.
