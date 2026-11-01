# Speech Agent Research

Chinese research page for Agentic RL × Speech Agent: five research directions,
verified experiment progress, interactive risk/latency figures, and read-only audio playback.

Route updated: 2026-11-01. Experiment snapshot: 2026-11-01.
New manual annotation is skipped at the user's request. Automatic evaluation uses
existing endpoint labels, real VAD and ASR/runtime measurements. Unlabelled external
desired-action accuracy remains unassessed. No completed RL or verified
emotion-aware interaction benefit is claimed.

Latest: 45 availability-matched lightweight SFT heads trained and verified.
At the same service clock and recalibration protocol, Text+WavLM cutoff increased
from 21.81% to 22.63%; conditional delay decreased from 532.9ms to 510.3ms.
No risk/latency improvement is demonstrated. The three eligible seeds reuse the
same 81 test turns. An external fixed-model static endpoint test is also complete
on 512 English non-synthetic Smart Turn clips; this does not establish full-turn
cutoff/latency or live interaction. Full experiment suite: 56 passed.
See [SFT comparison](reports/arl2_available_sft/README.md).
External test: [static clip classification](reports/arl2_external_endpoint/README.md).

Current route: [configs/automatic_route.json](configs/automatic_route.json).

## Audio attribution

The 64 five-second audio excerpts are derived from **Meta / FAIR Seamless
Interaction**, Naturalistic / Dev, under **CC-BY-NC 4.0**.

- Source: https://huggingface.co/datasets/facebook/seamless-interaction
- Dataset authors: Agrawal et al., *Seamless Interaction: Dyadic Audiovisual Motion
  Modeling and Large-Scale Dataset*, 2025.
- Changes: causal 48kHz to 16kHz resampling, five-second past-prefix cropping,
  and target-speaker/partner stereo channel ordering.
- Full dataset license: [web/annotations/DATASET_LICENSE.txt](web/annotations/DATASET_LICENSE.txt).
- Dataset card: [web/annotations/DATASET_README.md](web/annotations/DATASET_README.md).

Open `index.html` locally or serve this directory using any static web server.
Audio playback is read-only and does not request, collect or upload action answers.
