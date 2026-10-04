APP-LLR distributions on paired received blocks

To examine how semantic assistance changes the soft information used by
the decoder, we compare conventional BP with SF-BP permitting at most
one or six semantic invocations. All three receivers are evaluated on
the same 50,000 transmitted 130-byte blocks and channel realizations
at 2 dB. The code is WiMAX (1248,1040), with BPSK over real AWGN;
T=1.3, alpha=0.8 and the BP iteration limit is 50 per stage.
The base seed is 20260916. This fixed-size study is separate from the
Monte-Carlo cohorts used for the main BLER curves.

We align each information-bit APP-LLR with the transmitted bit as
(1-2*c)*L, so that a positive value supports the correct bit decision.
Each curve contains 50,000*1040=52,000,000 information-bit observations.
The observed APP is the channel LLR plus incoming check-node and semantic
messages, immediately before same-step numerical saturation. Ground truth
is used for this offline alignment, not for decoding updates.

Conventional BP is observed at its 50-iteration budget position. SF-BP
is observed at the terminal state of the one- or six-call budget; blocks
that stop early retain their terminal values. Fixed-budget distributions
combine recorded stage-entry and stopped-terminal statistics, with boundary
BP stages reconstructed using cached model probabilities. No additional
model invocation is needed for that reconstruction. Each displayed
distribution therefore represents the same complete transmission cohort.

The negative-LLR mass decreases from 10.3705% for BP to 0.1522% and
0.02436% for one- and six-call SF-BP. The corresponding exact negative
counts are 5,392,635, 79,130 and 12,665. No exactly-zero LLRs occur in
these curves. The block-error counts are 50,000, 3,213 and 442.
Thus, semantic assistance shifts the APP distribution toward stronger
support for correct decisions and reduces incorrectly directed messages.

The histogram uses bins of width 0.5 covering [-120,120), with no
underflow or overflow in this study. Probability density is
bin_count/(52,000,000*0.5), using the same full-cohort normalization for
all bins. No smoothing or density fitting is applied. The histogram
describes marginal soft information; block-error counts are measured
separately from decoded payloads.

Files
app_density_bins.csv contains all 1,440 histogram rows: 480 bins for each
of the three budgets. max_semantic_calls=0 denotes conventional BP.
terminal_metrics.csv contains each receiver's bit/block error counts,
BER, BLER, negative mass, zero count and mean aligned APP-LLR.
