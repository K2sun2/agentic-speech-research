# Speech Agent Research

Chinese research page for Agentic RL × Speech Agent: five research directions,
verified experiment progress, interactive risk/latency figures, and action annotation.

Snapshot: 2026-10-31. No completed RL or verified emotion-aware interaction benefit
is claimed. Missing human action labels remain missing.

## Audio attribution

The 64 five-second annotation excerpts are derived from **Meta / FAIR Seamless
Interaction**, Naturalistic / Dev, under **CC-BY-NC 4.0**.

- Source: https://huggingface.co/datasets/facebook/seamless-interaction
- Dataset authors: Agrawal et al., *Seamless Interaction: Dyadic Audiovisual Motion
  Modeling and Large-Scale Dataset*, 2025.
- Changes: causal 48kHz to 16kHz resampling, five-second past-prefix cropping,
  and target-speaker/partner stereo channel ordering.
- Full dataset license: [web/annotations/DATASET_LICENSE.txt](web/annotations/DATASET_LICENSE.txt).
- Dataset card: [web/annotations/DATASET_README.md](web/annotations/DATASET_README.md).

Open `index.html` locally or serve this directory using any static web server.
Annotations stay in the listener's browser until they download a JSON export.
This site does not collect or upload listener answers.
