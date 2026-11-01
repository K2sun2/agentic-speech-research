# 外部 Smart Turn 片段端点测试

固定原45个模型的静态外部测试已完成。512段英语非合成音频来自官方
`pipecat-ai/smart-turn-data-v3.1-test` 固定revision的首个分片，
其中endpoint标签为正259段、未完成标签253段。
下面为每种输入15个原监督头在**同一批512段**上的均值。

| 输入 | AUROC | 平衡准确率 @0.5 | 负类FPR @0.5 | 正类召回 @0.5 |
|---|---|---|---|---|
| Text | 0.5965 | 56.98% | 55.39% | 69.34% |
| Text+WavLM | 0.7863 | 68.01% | 52.52% | 88.55% |
| +Prosody | 0.7849 | 68.53% | 50.54% | 87.59% |

AUROC越高越好。@0.5为事先固定的概率阈值，没有在外部标签上调参。
FPR是“未完成片段被分类为结束”的比例，不能称为连续对话的误打断率。
15个头并非15份独立测试数据，不将模型seed波动当作独立数据采样置信区间。

## 数据和冻结协议

- 仓库revision：`22b4ec796d222da74beac14245603fba7653ac3f`；首分片481,161,775字节，SHA256为
  `272d19c7374fade945f5b623f341472556b285995c2e2caa4478e0fa05f603eb`。
- 607个英语非合成候选按固定id/行号哈希排序选择512个，无技术排除或替补。
  512个source id互异，但并不代表会话或说话人独立。
- 来源分布：human_5: 36、human_convcollector_1: 8、liva_1: 368、midcentury_1: 100。这是单分片便利子集，未覆盖官方全部31,473段；
  数据来源不平衡，不代表完整官方基准结果。
- 官方test仓库的HF split名为train，本实验仅用作测试，不用于训练或校准。
- 声学观察为片段结束前至多5秒；ASR仅消费结束前300ms的音频。
  原头、归一化统计、文本哈希器及旧校准策略均保持冻结。
- 主结果使用全部45个原头；9个此前校准可行策略的静态respond-now分类也记录在JSON，
  只评价当前片段，不外推未来等待行为。

初始TLS连接失败，改用默认TLS连接到相同固定URL后通过官方内容哈希验证；
未改变数据revision、分片、抽样规则或评分目标。
官方语言代码为ISO639-3 `eng`，初版兼容器只识别en等代码，初次选样得到0条。
在读取标签或模型分数前加入等价代码eng，并归档原源文件、原协议和修订说明。
修订只涉及语言代码兼容，未按标签或分数更换样本。

继承策略含timeout=None（禁用强制超时）；已修复该条件的实现兼容并归档原评分源文件及协议。
该修复不改变主指标、模型、样本、阈值或策略定义，未依据外部分数选阈值；详见[评分实现修订](scoring_amendment.json)。

## 验证

45个原头和5个原scaler均与预登记哈希一致；官方分片哈希一致；512个样本匹配无标签哈希排名；
逐条核验声学窗口和ASR前缀的内容哈希、长度及300ms截止点。完整实验测试56项通过，
包含语言代码兼容和选样不依赖端点标签的检查。

```bash
python -m agentic_speech_rl.external_endpoint_data prepare
python -m agentic_speech_rl.external_endpoint_data download
python -m agentic_speech_rl.external_endpoint_evaluate prepare
python -m agentic_speech_rl.external_endpoint_data select
python -m agentic_speech_rl.external_endpoint_data extract --gpu 3
# Parakeet使用支持AutoModelForTDT的现有环境
python -m agentic_speech_rl.external_endpoint_data asr --gpu 1
python -m agentic_speech_rl.external_endpoint_evaluate evaluate
python -m agentic_speech_rl.report_external_endpoint
```

## 当前研究结论的边界

这是跨来源的静态片段分类检查，不能验证完整话轮的误打断—延迟、用户行为反馈、
状态化流式ASR、真实在线可用性、情绪决策正确性或RL收益。
与内部400条语料的真实说话人/录音来源重叠尚不能完全核验。
本轮不训练模型，也不评估新45个可用观察SFT头的外部效果；后者的内部负结果单独报告。
完整话轮风险门槛仍未通过，仍处于Stage 1评估。

数据来源：Pipecat及其贡献者，Smart Turn v3.1 test，CC-BY-4.0。
[官方数据卡](https://huggingface.co/datasets/pipecat-ai/smart-turn-data-v3.1-test)。
本网页只发布汇总评测与协议，不额外发布该测试集音频或转写。

[完整结果](results.json) · [验证](verification.json) · [数据协议](protocol.json) ·
[评分协议](evaluation_protocol.json) · [语言修订](selection_amendment.json)。
