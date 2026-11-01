# 可用观察匹配的 SFT：内部受控对照

结论：完成 45 个新轻量 SFT 头，但未降低误打断率。Text+WavLM 从旧头的
21.81% 变为新头的 22.63%；有效回应的条件延迟从 532.9ms 缩短到 510.3ms。
风险同时上升，不能宣称改善风险—延迟权衡。当前仍是 Stage 1 评估，未训练 PPO。
按用户要求跳过新增人工标注，监督使用已有端点时间戳。

| 输入 | 策略头 | 校准可行运行 | 测试过早回应 | 测试漏回应 | 有效回应条件延迟 |
|---|---|---|---|---|---|
| Text | 旧头重新校准 | 0/15 | — | — | — |
| Text | 可用观察 SFT 重新校准 | 0/15 | — | — | — |
| Text+WavLM | 旧头重新校准 | 3/15 | 21.81% | 0.00% | 532.9ms |
| Text+WavLM | 可用观察 SFT 重新校准 | 3/15 | 22.63% | 0.00% | 510.3ms |
| +Prosody | 旧头重新校准 | 3/15 | 21.81% | 0.00% | 533.1ms |
| +Prosody | 可用观察 SFT 重新校准 | 3/15 | 23.46% | 0.00% | 504.6ms |

音频两组各有 3 个成对可比运行，全部来自 split 20261104 的相同 81 条测试话轮、
3 个训练 seed。不能当作 243 条独立话轮或三个独立测试集；其他四个划分均保留为无可行策略。
校准目标为过早回应≤5%、漏回应≤1%，仅在 calibration 角色选择工作点。
所有被选策略的测试过早回应仍超过 5%；校准可行不保证测试可行。

## 训练和对照

真实 VAD/服务可用性回放提取 4,279 个始终 Wait 的可用观察，采样不依赖模型得分。
标签为观察时刻是否到达已有 EoT。冻结编码器，沿用 input→64 Tanh→Dropout(0.2)→1
的 MLP；AdamW，lr=0.001、weight decay=0.01，最多250轮、patience30。
5个会话角色划分×3种输入×3个seed，CPU训练45个新头。
归一化仅使用 fit，早停仅使用 stop，每个划分内 fit/stop/calibration/test 会话互斥。

旧头与新头均在同一固定336策略网格上重新校准，使用相同服务可用时钟。
所有模态共用 ASR 与 WavLM+WORLD 的实测服务耗时；Text 也等待声学服务，
这是控制可见信息/观察机会的实验，不代表优化后的纯文本部署成本。
校准与共同时间轴对照不计头耗时，选定策略另行测量实际逐次头耗时并回放。
表中为后者，未根据测试结果再次调参。

此前继承策略的18.52%属于另一个实验；本轮改变了共同时间轴与校准流程，
不可将18.52%→22.63%归因于重训。本轮有效对照为21.81%→22.63%。

## 输入分布审计

旧标注停顿机会点4,837个，真实VAD机会点5,027个，其中320个不在原标注静音区间内。
这不构成独立核验的活跃语音或标注错误。fit角色中，旧ASR音频年龄小于300ms的比例为0%，
新观察约38.6–41.5%；新声学特征陈旧度中位数约32ms、P95约160ms。
这只表明输入分布存在差异，不能证明测试错误的因果来源。

## 验证与复现

45个新头完成；45个旧头及旧归一化文件哈希保持不变。5个fit-only scaler均已逐项验证。
972次选定测试episode的向量化结果与事件回放一致；校准约束只在calibration检查。
完整测试套件55项通过，1个不影响通过的第三方pyworld/pkg_resources警告。

```bash
python -m agentic_speech_rl.available_sft prepare
python -m agentic_speech_rl.available_sft train --worker 0 --workers 1
python -m agentic_speech_rl.available_sft old
python -m agentic_speech_rl.available_sft_evaluate evaluate
python -m agentic_speech_rl.available_sft_evaluate verify
python -m agentic_speech_rl.report_available_sft
```

已有冻结协议与产物应复用；上述训练命令仅供复现，报告命令不会重新训练。
完整数据、权重和日志在 `/mnt/sda/sunkangle/agentic-speech-rl/available_sft`。
本目录保留 [results.json](results.json)、[protocol.json](protocol.json) 和
[verification.json](verification.json)。

## 独立测试状态及研究边界

已在接触外部音频/分数前登记45个原头与scaler哈希，选定官方
`pipecat-ai/smart-turn-data-v3.1-test`，revision
`22b4ec796d222da74beac14245603fba7653ac3f`。该test仓库的HF split虽名为train，
只计划作为外部测试，绝不用于训练。计划从固定首个分片按id/行号哈希选至多512个英语非合成片段。
内部对照完成时，持续TLS握手超时导致分片尚未下载，当时没有外部音频、选择清单、特征或模型分数。
仅片段endpoint标签也不足以产生完整话轮的误打断—延迟指标。

本轮复用了已多次观察的400条内部语料，划分彼此重叠，属于探索性负结果。
实测耗时队列回放不等于状态化流式ASR或真实在线服务；没有情绪理解、共情或RL收益结论。
后续优先恢复已有标签的独立测试和实际状态化运行，保持匹配风险与覆盖率的评价条件。

后续更新：网络传输恢复后，已完成512段外部静态端点分类；参见[独立来源片段测试](../arl2_external_endpoint/README.md)。该测试使用原45个头，未评价本轮新头，也不提供完整话轮风险—延迟结论。
