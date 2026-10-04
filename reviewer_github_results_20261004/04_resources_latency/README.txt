MODEL RESOURCES AND RECEIVER LATENCY

These measurements evaluate the fixed 130-byte, (1248,1040), rate-5/6 receiver
configuration. The four methods are LDPC-BP, LM-Top1, BM-BP and SF-BP. Results
were measured on 12 September 2026 using an NVIDIA GeForce RTX 3060 (12 GB),
Intel Core i7-12700F and Windows 11 Pro. The software environment used Python
3.9.25, PyTorch 2.0.1+cu117, CUDA 11.7 and Transformers 4.36.2. All inference
used batch size one, FP32, evaluation mode and no gradients. Autocast and TF32
were disabled; deterministic algorithms were enabled. BP used one worker,
with one PyTorch intra-op and one inter-op thread.

Model resources

The semantic model contains a ByT5-small encoder and a position-wise,
256-class byte head. A 130-byte input produces 138 encoder positions and
130 classified positions; no autoregressive decoder is used. Resource tests
used synthetic byte inputs with seed 20260602. Parameter storage is the sum
of tensor element counts times element sizes: 218,034,560 parameters occupy
872,138,240 bytes, or 831.73583984375 MiB, in FP32.

Counting one multiply-accumulate as two FLOPs, analytical matrix-operation
counts and a separate PyTorch shape/FLOP profiler agree on 60,356,239,360
FLOPs (60.35623936 GFLOPs) per model inference. This includes attention
projections, QK/AV products, feed-forward projections and the byte head.
It excludes embeddings, biases, normalization, activations, softmax, indexing,
transfers, CPU BP and semantic updates. It therefore measures the model's
dominant matrix operations, not the full receiver. Six calls contribute at
most 362.13743616 GFLOPs (362.14 rounded). Average matrix cost per transmitted
block is its mean actual semantic-call count times 60.35623936 GFLOPs.

GPU memory was measured after 10 untimed model-call warmups. After
synchronization, baseline allocation was recorded and peak statistics reset;
30 inference-pipeline calls were then measured without profiling. Peak
allocated tensor memory was 920,897,536 bytes (878.236328125 MiB), including
the resident model and temporary tensors; peak reserved memory was 950 MiB.
Parameter storage and peak allocation are distinct quantities. These values
exclude loading/training peaks, host RAM and non-PyTorch device allocations.
The same resident model is reused across semantic calls.

Primary latency experiment

At each SNR in {2,3,4,5,6} dB, all methods received the same 4,000 held-out
blocks and channel realizations. SNR is 1/sigma^2 for unit-energy real BPSK
over AWGN (2Es/N0). Each method/SNR group first ran 50 separate warmup blocks.
Three timing passes then reused the same 4,000 received blocks, yielding
12,000 observations per group and 240,000 overall. Repeated timings are not
additional independent blocks or channel draws.

End-to-end wall-clock timing began with received symbols and ended with final
information-bit decisions, with GPU synchronization at both boundaries.
It included channel-LLR construction, input preparation, initial BP, triggered
model pipelines, CPU/GPU transfers, semantic updates and subsequent BP.
Channel generation, accuracy evaluation, file writing, model loading and
warmup were excluded. No per-stage timers were used in this experiment.
Separately measured model construction/loading/device transfer took
2.0634434 seconds; CUDA-context setup took 0.0457313 seconds.

The checkpoint, temperature T=1.3 and alpha=0.8 were fixed; BM-BP used the
same alpha. Each BP run allowed at most 50 iterations. Maximum semantic
calls were zero for LDPC-BP, one for LM-Top1 and six for BM-BP/SF-BP.
Syndrome-based stopping could reduce the actual number, including to zero.
All blocks contribute to latency and call statistics. SD is the sample
standard deviation (ddof=1); P95 uses linear percentile interpolation across
all 12,000 timings. These describe timing variability, not confidence
intervals. At 6 dB, only 11/4,000 blocks triggered semantic assistance.
The error counters in this timing cohort are sanity checks, not precision
estimates of low block-error rates.

Component profiling

A separate diagnostic pass measured 100 blocks per method/SNR, totaling
2,000 records. ByT5 pipeline time includes preparation, model execution,
transfers and posterior processing. Ordinary stage measures initial BP;
assisted BP measures later BP; semantic update measures factor/extrinsic
messages and damping. Other time includes remaining work and timer overhead.
All profiled decisions matched production. The profiling cohort is a smaller
subset and uses an instrumented execution path, so its totals cannot replace
primary latency. matched_primary_ms averages the three primary timings for
the same sample; profile_to_primary_ratio also reflects execution variation.

Files

resource_summary.csv and flops_by_component.csv: parameter, memory and FLOP
results. environment.csv and environment_packages.csv: hardware, execution
settings and package versions. latency_summary.csv: all 20 method/SNR
summaries at stored precision. per_block_latency.csv.gz: all 240,000 timing
observations. semantic_call_histogram.csv: call distributions, including zero,
with distinct-block and repeated-observation counts. component_summary.csv:
20 component summaries. component_timings.csv: all 2,000 component records.
initialization_summary.csv: separately timed one-time setup costs.

CSV files are UTF-8 with headers. Times are milliseconds unless labeled
seconds; MiB is 2^20 bytes and GFLOPs is 10^9 FLOPs. repeat and sample_id are
zero-based; sample_id is shared by methods within an SNR. Empty fields mean
not applicable. BP round 0 is initial BP; rounds 1-6 are subsequent runs.
