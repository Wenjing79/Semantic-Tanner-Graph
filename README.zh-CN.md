# Semantic Tanner Graph

**将字节级语义概率作为联合因子，引入 LDPC 置信传播译码。**

[English](README.md) · [结果与数据](reviewer_github_results_20261004/README.md) · [全部图表](reviewer_github_results_20261004/FIGURES.md) · [审稿问题索引](reviewer_github_results_20261004/REVIEWER_GUIDE.md)

![语义 Tanner 图接收流程](reviewer_github_results_20261004/figures/receiver_overview.svg)

SF-BP 利用经过微调的 ByT5 编码器，从当前可能含错的译码序列中估计每个位置的 256 维字节概率。每个字节对应八个信息比特，语义因子保留这八个比特的联合概率结构，并根据不断变化的外信息重新计算发给各比特的消息。

**当前公开范围：实验设置、结果数据、图表和测量记录。** 接收机代码、训练/评估/基准测试脚本暂不公开，计划在论文接受后发布；本结果包不包含模型权重。

## 从哪里开始看

| 你关心的问题 | 图文入口 |
| :--- | :--- |
| SF-BP 与 BM-BP、普通 BP 的性能差异 | [误码统计与完全恢复](reviewer_github_results_20261004/03_error_statistics/README.md) |
| 内部迭代和再次调用模型时发生了什么 | [审稿人 3 的三组机制分析图](reviewer_github_results_20261004/06_decoding_dynamics/README.md) |
| 数据怎么划分，模型怎么训练，随机种子是什么 | [数据与训练设置](reviewer_github_results_20261004/01_data_training/README.md) |
| 温度与语义强度如何选择，对结果有多大影响 | [参数选择与敏感性](reviewer_github_results_20261004/02_parameter_studies/README.md) |
| 语义辅助如何改变 APP-LLR 概率密度 | [BP/SF-BP 的配对分布对比](reviewer_github_results_20261004/05_app_llr/README.md) |
| 模型多大、速度多快、实际调用多少次 | [资源、时延与逐块计时](reviewer_github_results_20261004/04_resources_latency/README.md) |

## 主要性能结果

![主性能 BLER 与 ByteER 曲线](reviewer_github_results_20261004/figures/main_reliability.png)

在 4 dB，SF-BP 在 7,372,800 个传输块中出现 50 个块错误，BLER 为 **6.78 × 10⁻⁶**；BM-BP 在 1,835,008 个传输块中出现 50 个错误，BLER 为 **2.72 × 10⁻⁵**。二者使用同一个语义模型和匹配的接收机设置，均允许最多六次模型调用。主要区别是 BM-BP 在每一轮 BP 内固定逐比特语义 LLR，而 SF-BP 随外信息变化更新联合字节因子的消息。

[PDF 下载](reviewer_github_results_20261004/figures/main_reliability.pdf) · [CSV 数值](reviewer_github_results_20261004/03_error_statistics/per_snr_metrics.csv)

BLER 误差棒是分别针对每个方法、每个 SNR 的 95% 置信序列，不是覆盖所有曲线的同时置信带。图中的普通 BP 来自与 SF-BP 配对的实验；两种语义接收机的高 SNR 实验彼此不配对。

## 译码过程中的变化

![审稿人 3 的 R3-1：随内迭代与模型调用变化的 BER/BLER](reviewer_github_results_20261004/06_decoding_dynamics/overall_error_evolution.png)

这组机制实验固定在 2 dB，使用同一批 50,000 个传输块。第一次模型调用后的第一个内迭代，BLER 为 0.98986；本阶段结束时降到 0.06426；最多六次模型调用后降到 0.00884。

这说明改善来自两个过程：**固定字节概率下继续迭代消息**，以及**再次调用模型刷新字节概率**。这些是有限长实验的经验分布，不是无限码长密度演化预测，也不能与主性能曲线的自适应停止样本量混用。

[完整 R3-1 / R3-2 / R3-3 图文、PDF 与 CSV](reviewer_github_results_20261004/06_decoding_dynamics/README.md)

## 如何使用这些材料

先看每个目录的 `README.md`，再按图下链接查看 PDF 或 CSV。两个公开 JSON 保留了主实验逐 SNR 的块数、错误数、停止规则和区间。完整逐块计时文件采用 `CSV.gz` 压缩；解压后是普通 CSV。

通过仓库首页 **Code → Download ZIP** 可整体下载。所有研究数据和原有目录路径保持可用，新页面只是为这些数据增加图文入口。原 TXT 方法说明保留供查阅；图表输入可在 [figure_sources.json](reviewer_github_results_20261004/figure_sources.json) 中查看。
