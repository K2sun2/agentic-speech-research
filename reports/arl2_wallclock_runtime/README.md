# 按真实时钟运行的流式 ASR 工程试验

完成公开 Cekura 合成测试集固定6通电话的24个用户话轮。输入按32ms真实时钟释放，
实际执行Silero、CPU有状态Zipformer、GPU WavLM和冻结策略头，没有读取缓存转写或回放计算耗时。
原内部策略阈值冻结；ASR后端从Parakeet换成Zipformer，本轮为单独的工程与合成数据试验。

| 方法 | 过早回应 | 漏回应数 | 有效回应条件延迟 |
|---|---|---|---|
| 冻结 Audio 策略 | 16/24 (66.67%) | 0 | 620.1ms |
| VAD 1.1秒 | 15/24 (62.50%) | 0 | 1090.0ms |

共有5个脚本，包含重复脚本和库存合成声音；24个话轮不是24个独立会话。
样本小，未匹配风险或覆盖率，不宣称方法优势。逐话轮结果和实际计时分布见[完整JSON](results.json)。

## 数据与边界

数据：Cekura Turn-Taking Benchmark公开v1，revision
`f629c591adcd0a94e223e6ea72654400aa9cb8c5`，CC-BY-4.0。
在取得音频和策略分数前，对官方24通End-of-Turn电话按固定哈希选择6通：
9041、8121、8041、8013、8092、9042。未使用中断或backchannel脚本。
官方README明确所有声音均为ElevenLabs库存合成，标签由脚本生成；a/b/c三轨相同，不能称为三名人工标注。

本轮按“下一条脚本Agent发声前的所有caller片段”聚合用户话轮，并裁切为首次发声前200ms至
最后发声后1.5s，严格限制在原caller音轨内，不添加静音。
**切分边界使用已有标注**，流式运行在这些已裁切话轮上进行，ASR每轮重新建流；
并非自主识别完整通话中的话轮边界。run_turn不接收金标准时间戳。
48kHz→16kHz使用63tap因果FIR，不补偿约0.65ms滤波延迟，保留原标注时钟。

## 运行与缓存

使用sherpa-onnx1.13.8、int8 Zipformer chunk16/left64，CPU两线程；
每个新话轮建立一个stream，按512样本连续accept_waveform，重复decode_stream保留encoder/decoder缓存。
禁止端点重置和EOF补零/flush，末尾不足512样本丢弃。
依赖仅安装到 `/mnt/sda/sunkangle/agentic-speech-rl/runtime-deps`，未改共享Python环境。
权重固定HF revision `672fbf1b30579d6585301139bb363f42a0ad4a24`并验证官方内容哈希。

WavLM仅观察已到达的过去至多5s，沿用原归一化；此头不使用WORLD韵律特征，故未调用。
ASR记录的是完成decode时已接收的音频前缀上界，不假设其等于精确token声学对齐点。
只有已完成的同一静音段声学结果可以参与决策，回应时刻使用真实单调时钟。
首次学习策略回应取消尚未启动的推理；已启动工作完成并计费。
VAD基线在相同输入上影子运行，可能多消费一段音频，不能将总运行耗时当作单策略专属成本。

实际送达音频约82.11s，总话轮运行（含影子VAD与工作排空）约82.15s。
执行2566个VAD帧、191次有状态decode、123次策略头前向。
设备共享，加载与预热不计入逐话轮时间；本次测量不代表部署延迟保证。

## 验证与研究状态

逐条核验帧连续性、实际释放时刻、推理结果可用性、同静音段约束、声学窗口哈希、
原策略头/scaler哈希及模型/数据内容哈希。58项完整实验测试通过，1项第三方弃用警告。

```bash
python -m agentic_speech_rl.runtime_assets
python -m agentic_speech_rl.wallclock_runtime curate
PYTHONPATH=/mnt/sda/sunkangle/agentic-speech-rl/runtime-deps python -m agentic_speech_rl.wallclock_runtime run --gpu 3
python -m agentic_speech_rl.report_wallclock_runtime
```

当前仍是Stage1评估，无新训练、人工标注或RL。该试验补上本地真实时钟与有状态ASR运行，
下一步仍需自然人连续对话、实际生成回复与闭环用户行为验证。
它不提供麦克风/网络往返、自然度、共情或情绪动作正确性结论，也不是TurnBench/Cekura官方私榜成绩。

[协议](protocol.json) · [验证](verification.json) ·
[数据卡](https://huggingface.co/datasets/cekura/turn-taking-bench)。
