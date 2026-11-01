# Speech Agent Research

Chinese research page for Agentic RL × Speech Agent: five research directions,
verified experiment progress, interactive risk/latency figures, and read-only audio playback.

Route updated: 2026-11-01. Experiment snapshot: 2026-10-31.
New manual annotation is skipped at the user's request. Automatic evaluation uses
existing endpoint labels, real VAD and ASR/runtime measurements. Unlabelled external
desired-action accuracy remains unassessed. No completed RL or verified
emotion-aware interaction benefit is claimed.

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
