---
license: cc-by-nc-4.0
configs:
- config_name: improvised
  data_files:
  - split: dev
    path:
    - improvised/dev/**/*
  - split: test
    path:
    - improvised/test/**/*
  - split: train
    path:
    - improvised/train/**/*
- config_name: naturalistic
  data_files:
  - split: dev
    path:
    - naturalistic/dev/**/*
  - split: test
    path:
    - naturalistic/test/**/*
  - split: train
    path:
    - naturalistic/train/**/*

tags:
- webdataset
- audio
- video
pretty_name: Seamless Interaction
---


<div align="center">

<h1>Seamless Interaction Dataset</h1>

<img src="https://github.com/zyaoj/zhiyuanyaoj.github.io/blob/24666287c3f6dc5efb79389c95a42a38bf78f06a/assets/images/fair/seamless_interaction_banner.gif?raw=true" alt="Seamless Interaction Dataset Banner" width="800px">

**A large-scale multimodal dataset of 4,000+ hours of human interactions for AI research**


<table>
<tr>
<td align="center">
<a href="https://ai.meta.com/blog/seamless-interaction-natural-conversational-dynamics/">
🖼️ Blog
</a>
</td>
<td align="center">
<a href="https://ai.meta.com/research/seamless-interaction/">
🌐 Website
</a>
</td>
<td align="center">
<a href="https://www.aidemos.meta.com/seamless_interaction_dataset">
🎮 Demo
</a>
</td>
<td align="center">
<a href="https://github.com/facebookresearch/seamless_interaction">
📦 GitHub
</a>
</td>
<td align="center">
<a href="https://ai.meta.com/research/publications/seamless-interaction-dyadic-audiovisual-motion-modeling-and-large-scale-dataset">
📄 Paper
</a>
</td>
</tr>
</table>


</div>

Human communication involves a complex interplay of verbal and nonverbal signals, essential for conveying meaning and achieving interpersonal goals.

The **Seamless Interaction Dataset** is a large-scale collection of over 4,000 hours of face-to-face interaction footage from more than 4,000 participants in diverse contexts.
This dataset enables the development of AI technologies that understand human interactions and communication, unlocking breakthroughs in:

- 🤖 Virtual agents and embodied AI
- 🎭 Natural human-computer interaction
- 📡 Advanced telepresence experiences
- 📊 Multimodal content analysis tools
- 🎬 Animation and synthetic content generation

## 🚀 Quick Start

```bash
git clone https://github.com/facebookresearch/seamless-interaction
cd seamless-interaction
pip install -e .
streamlit run src/seamless_interaction/app/Welcome.py

# if you use uv
uv sync
uv run streamlit run src/seamless_interaction/app/Welcome.py
```

Explore the dataset with our interactive browser:

**Features:**
- 🔍 **Hierarchical Navigation**: Browse by Label → Split → Batch → Interaction
- 🎲 **Random Sampling**: Discover interactions with one-click random selection
- 📥 **Download Interface**: Download specific batches with size estimation and progress tracking
- 🎬 **Video Viewer**: Side-by-side participant videos with synchronized playback
- 📊 **Data Analysis**: Overview statistics and distribution plots
- 📁 **File Management**: Organize and preview audio, JSON, and NPZ files with expandable dropdowns

### Download Options

We provide comprehensive download methods supporting all research scales and requirements:

| **Scale** | **Size** | **Method** | **Use Case** | **Script** | **Sampling** |
|-----------|----------|------------|--------------|------------|-------------|
| 🔍 **Single Example** | ~100MB | S3 | Quick exploration, understanding data structure | [`download_s3.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_s3.py#L10) | Auto-sample from preferred vendors |
| 👥 **Interaction Pair** | ~200MB | S3 | Study conversational dynamics between participants | [`download_s3.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_s3.py#L34) | Auto-detect conversation pairs |
| 📂 **Sample Set** | ~1GB | S3/HF | Initial prototyping, algorithm development | [`download_s3.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_s3.py#L66), [`download_hf.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_hf.py#L10) | File selection or archive-based |
| 🎯 **Session Groups** | ~400MB | S3 | Deep conversational context, session dynamics | [`download_s3.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_s3.py#L100) | Auto-sample rich sessions |
| 📦 **Single Batch** | ~50GB | HF | Substantial local development, full exploration | [`download_hf.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_hf.py#L24) | WebDataset tarball download |
| 🗂️ **Multiple Batches** | ~150GB+ | HF | Training datasets, large-scale analysis | [`download_hf.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_hf.py#L38) | WebDataset tarball download |
| 🎯 **Different Splits** | Variable | HF | Cross-validation (train/dev/test, improvised/naturalistic) | [`download_hf.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_hf.py#L55) | WebDataset tarball download |
| 🌍 **Whole Dataset** | ~27TB | HF | Complete research dataset, production systems | [`download_hf.py`](https://github.com/facebookresearch/seamless_interaction/blob/main/scripts/download_hf.py#L82) | WebDataset tarball download |


### Basic Data Loading (HF + WebDataset)

```python
from datasets import load_dataset

# configure
label = "improvised"
split = "dev"
batch_idx = 0
archive_list = [0, 1]

base_url = (
    f"https://huggingface.co/datasets/facebook/"
    f"seamless-interaction/resolve/main/{label}/{split}/"
    "{batch_idx:04d}/{archive_idx:04d}.tar"
)
urls = [base_url.format(batch_idx=batch_idx, archive_idx=archive_idx) for archive_idx in archive_list]
dataset = load_dataset(
    "webdataset", data_files={split: urls}, split=split, streaming=True
)

for item in dataset:
    break

isinstance(item["mp4"], bytes)
# True
item["npz"].keys()
# dict_keys(['boxes_and_keypoints:box', 'boxes_and_keypoints:is_valid_box', 'boxes_and_keypoints:keypoints', 'movement:EmotionArousalToken', 'movement:EmotionValenceToken', 'movement:FAUToken', 'movement:FAUValue', 'movement:alignment_head_rotation', 'movement:alignment_translation', 'movement:emotion_arousal', 'movement:emotion_scores', 'movement:emotion_valence', 'movement:expression', 'movement:frame_latent', 'movement:gaze_encodings', 'movement:head_encodings', 'movement:hypernet_features', 'movement:is_valid', 'smplh:body_pose', 'smplh:global_orient', 'smplh:is_valid', 'smplh:left_hand_pose', 'smplh:right_hand_pose', 'smplh:translation'])
item["json"].keys()
# dict_keys(['id', 'metadata:transcript', 'metadata:vad'])
item["wav"].keys()
# dict_keys(['path', 'array', 'sampling_rate'])
```

## 📦 Deep Dive into the Dataset

### Dataset Structure

The Seamless Interaction Dataset is organized into two main categories/labels:
- **Improvised**: Interactions primarily based on predefined scenarios with guided prompts with at least one professional actor.
- **Naturalistic**: Prompted conversations that can be carried out by normal people.

```
seamless_interaction
├── interactions.csv          # Metadata for prompts
├── participants.csv          # Metadata for participants
├── relationships.csv         # Metadata for participant relationships per session
├── improvised                # Interactions with guided prompts
│   ├── dev
│   │   ├── 1P-IS/            # First-party internal state annotations
│   │   │   └── V<vendor>_S<session>_I<interaction>_P<participant>.json
│   │   ├── 1P-R/             # First-party internal state rationale annotations
│   │   │   └── V<vendor>_S<session>_I<interaction>_P<participant>.json
│   │   ├── 3P-IS/            # Third-party internal state annotations
│   │   │   └── V<vendor>_S<session>_I<interaction>_P<participant>.json
│   │   ├── 3P-R/             # Third-party internal state rationale annotations
│   │   │   └── V<vendor>_S<session>_I<interaction>_P<participant>.json
│   │   ├── 3P-V/             # Third-party visual annotation
│   │   │   └── V<vendor>_S<session>_I<interaction>_P<participant>.json
│   │   ├── audio/            # Speaker-bleed denoised audio
│   │   │   └── V<vendor>_S<session>_I<interaction>_P<participant>.wav
│   │   ├── boxes_and_keypoints/
│   │   │   ├── box/          # Bounding boxes for each participant
│   │   │   ├── is_valid_box/ # Whether bounding boxes are valid
│   │   │   └── keypoints/    # Detected facial/body keypoints
│   │   ├── movement/         # Quantified Imitator movement features
│   │   │   ├── emotion_arousal/           # Arousal measures
│   │   │   ├── emotion_valence/           # Valence measures
│   │   │   ├── emotion_scores/            # Emotion detection scores
│   │   │   ├── expression/                # Facial expression parameters
│   │   │   ├── FAUToken/                  # Facial Action Unit tokens
│   │   │   ├── FAUValue/                  # Facial Action Unit values
│   │   │   ├── gaze_encodings/            # Eye gaze direction encodings
│   │   │   ├── head_encodings/            # Head position/rotation encodings
│   │   │   ├── frame_latent/              # Per-frame latent representations
│   │   │   └── is_valid/                  # Validity flags for extracted features
│   │   ├── smplh/            # SMPL-H body model parameters
│   │   │   ├── body-pose/    # Body pose parameters
│   │   │   ├── global_orient/ # Global orientation parameters
│   │   │   ├── is_valid/     # Valid frames indicators
│   │   │   ├── left_hand_pose/ # Left hand pose parameters
│   │   │   ├── right_hand_pose/ # Right hand pose parameters
│   │   │   └── translation/  # Global translation parameters
│   │   ├── transcript/       # Time-aligned speech transcription
│   │   │   └── V<vendor>_S<session>_I<interaction>_P<participant>.jsonl
│   │   ├── vad/              # Voice activity detection
│   │   │   └── V<vendor>_S<session>_I<interaction>_P<participant>.jsonl
│   │   └── video/            # Raw HD video recordings
│   │       └── V<vendor>_S<session>_I<interaction>_P<participant>.mp4
│   ├── test/                 # Test split with similar structure
│   └── train/                # Training split with similar structure
└── naturalistic/             # Spontaneous conversations
    ├── dev/                  # Same structure as improvised/dev
    ├── test/                 # Same structure as improvised/test
    └── train/                # Same structure as improvised/train
```

Each file is named according to a consistent convention:
- `V<vendor_id>`: Collection site/vendor identifier
- `S<session_id>`: Unique session identifier
- `I<interaction_id>`: Specific interaction within a session
- `P<participant_id>`: Individual participant identifier

### Available Modalities and Features

Each interaction in the dataset includes:

| Modality | Description | File Format | Sample Rate |
|----------|-------------|-------------|-------------|
| 🎥 Video | High-definition face-to-face footage | MP4 (H.264) | 30/29.97 FPS, 1080p |
| 🎙️ Audio | Denoised audio with separate channels | WAV | 48kHz, 16-bit |
| 📝 Transcript | Time-aligned speech transcription | JSONL | - |
| 🏃 SMPL-H | 3D body model parameters | NPY | 30 Hz |
| 🧠 Imitator Movement Features | Comprehensive quantified imitator movement data | NPY | 30 Hz |
| 📊 Annotations | Human-annotated behavioral data | JSON | - |
| 🔊 VAD | Voice activity detection | JSONL | 100 Hz |
| 📦 Keypoints | Face and body keypoints | NPY | 30 Hz |

#### Annotation Types

The dataset includes several types of human annotations for rich behavioral analysis:

| Annotation | Hours | Total Annotations | Mean # Tokens |
|------------|-------------|--------|--------|
| 1P-IS (1st-party internal state annotations) | 1.1 | 751 | 5.8 |
| 1P-R (1st-party internal state rationale annotations) | 1.1 | 751 | 10.2 |
| 3P-IS (3rd-party internal state annotations) | 4.7 | 5132 | 5.2 |
| 3P-R (3rd-party internal state rationale annotations) | 4.7 | 5132 | 11.3 |
| 3P-V (3rd-party visual annotation) | 4.7 | 5132 | 14.6 |

Please refer to the [technical report](https://ai.meta.com/research/publications/seamless-interaction-dyadic-audiovisual-motion-modeling-and-large-scale-dataset/) for a more detailed overview of annotations.

#### Movement/Imitator Feature Types

The movement directory contains rich behavioral features (output of the Imitator model):

| Feature | Description |
|---------|-------------|
| `emotion_arousal` | Arousal intensity measurements |
| `emotion_valence` | Valence (positive/negative) measurements |
| `emotion_scores` | Detected emotion categorical scores |
| `expression` | Parametric facial expression encodings |
| `FAUToken`/`FAUValue` | Facial Action Unit tokens and intensity values |
| `gaze_encodings` | Neural encodings of gaze direction |
| `head_encodings` | Neural encodings of head position and rotation |
| `frame_latent` | Per-frame latent representations |
| `alignment_head_rotation` | Head rotation data for temporal alignment |
| `alignment_translation` | Translation parameters for temporal alignment |
| `EmotionArousalToken`/`EmotionValenceToken` | Discretized emotion tokens |
| `hypernet_features` | Features from hypernetwork processing |

### Dataset Versions

The dataset is organized in self-contained batches for flexible exploration:

| Split | Batches | Size per Batch | Total Size | Description |
|-------|---------|----------------|------------|-------------|
| **dev** | 5 | ~50GB | ~500GB | Development/validation set |
| **test** | 5 | ~50GB | ~500TB | Hold-out test set |
| **train** | 200+ | ~50GB | ~20TB+ | Full training data |

#### File Format Specifications

Our data is stored in the following formats for optimal usability:

| Format | Description | Usage |
|--------|-------------|-------|
| NPZ | NumPy array files | Efficient storage of numerical feature vectors, keypoints, and parameters |
| JSONL | JSON Lines | Time-aligned annotations with one event per line (e.g., transcripts, VAD) |
| JSON | JavaScript Object Notation | Structured metadata and annotations with timestamps |
| MP4 | MPEG-4 Part 14 | High-quality compressed video with H.264 encoding |
| WAV | Waveform Audio | Uncompressed audio for highest fidelity processing |

## 🧪 Research Applications

The Seamless Interaction Dataset enables research across multiple domains:

### Embodied AI and Virtual Agents
- Train agents that display natural gestures
- Model turn-taking dynamics and interaction rhythms
- Generate contextually appropriate responses to human behavior

### Multimodal Understanding
- Analyze cross-modal correlations between speech, gesture, and expressions
- Extract behavioral patterns from large-scale interaction data
- Develop models to understand social dynamics

### Human-Computer Interaction
- Design interfaces that respond to subtle human cues
- Improve telepresence technologies with better behavioral modeling
- Create more natural conversational agents

### Animation and Content Creation
- Generate realistic human behaviors for animated characters
- Synthesize conversational dynamics for virtual production
- Create training data for digital human technologies


## ⚠️ Known Limitations and Noise in Metadata

Given the scale and complexity involved in collecting the Seamless Interaction dataset, there are several known limitations that we will address in our  ongoing work, with improvements planned for in future versions:

### Errors in Human-Based Time-Stamping
The core unit of the dataset is interactions. An interaction defines the *active time* during which a  participant’s conversation and behavior can be linked to a pair of prompts. We have observed instances of misaligned time-stamps, including:
- Annotated start/end times may be too early or too late.
- Occasional misalignment between prompt text and spoken material.
- Ordering of prompts that may contain off-by-one errors.

Despite our efforts to automatically identify and correct these errors, approximately 10% of the interactions remain affected.

### Time Stamping "Noise" in Moments of Interest (MOI)
While defining a MOI inherently involves some subjectivity, there are rare instances where:
- The described behavior only represents a subset of the observed behavior.
- The duration of the MOI does not fully capture the annotated behavior.

### Incorrect Assignment of Participant IDs
In rare instances, we have observed:
- Duplicate participant identifiers being assigned to different individuals.
- The same individual being mapped to different identifiers.

### Unreleased "Meta Time"
Currently, the dataset only contains *active time* segments - time in which two participants are actively responding to prompts. *Meta time* refers to the time between *active segments* in which participants are studying their new prompts, taking a break, etc. *Meta time* constitutes hundreds of hours in the raw collection and maybe be explored for future releases.

### Variation in Recording Site Consistency
This multi-site project contains variation in:
- Recording quality, including issues like speaker bleed and participants staying in frame.
- Acting quality in *Improvised* segments.
- The likelihood of time-stamping errors.

All vendors met our technical requirements; however,there is noticeable variation in production quality across different sites.

## 📄 License & Data Usage Policy

The Seamless Interaction Dataset is licensed under CC-BY-NC 4.0 (Creative Commons Attribution-NonCommercial 4.0 International).

This means you are free to:
- **Share** — copy and redistribute the material in any medium or format
- **Adapt** — remix, transform, and build upon the material

Under the following terms:
- **Attribution** — You must give appropriate credit, provide a link to the license, and indicate if changes were made.
- **NonCommercial** — You may not use the material for commercial purposes without explicit permission.


## 📑 Citation

If you use the Seamless Interaction Dataset in your research, please cite:


<details>
<summary>BibTeX</summary>

```bibtex
@article{seamless_interaction,
  title={Seamless Interaction: Dyadic Audiovisual Motion Modeling and Large-Scale Dataset},
  author={Vasu Agrawal and
		Akinniyi Akinyemi and
		Kathryn Alvero and
		Morteza Behrooz and
		Julia Buffalini and
		Fabio Maria Carlucci and
		Joy Chen and
		Junming Chen and
		Zhang Chen and
		Shiyang Cheng and
		Praveen Chowdary and
		Joe Chuang and
		Antony D'Avirro and
		Jon Daly and
		Ning Dong and
		Mark Duppenthaler and
		Cynthia Gao and
		Jeff Girard and
		Martin Gleize and
		Sahir Gomez and
		Hongyu Gong and
		Srivathsan Govindarajan and
		Brandon Han and
		Sen He and
		Denise Hernandez and
		Yordan Hristov and
		Rongjie Huang and
		Hirofumi Inaguma and
		Somya Jain and
		Raj Janardhan and
		Qingyao Jia and
		Christopher Klaiber and
		Dejan Kovachev and
		Moneish Kumar and
		Hang Li and
		Yilei Li and
		Pavel Litvin and
		Wei Liu and
		Guangyao Ma and
		Jing Ma and
		Martin Ma and
		Xutai Ma and
		Lucas Mantovani and
		Sagar Miglani and
		Sreyas Mohan and
		Louis-Philippe Morency and
		Evonne Ng and
		Kam-Woh Ng and
		Tu Anh Nguyen and
		Amia Oberai and
		Benjamin Peloquin and
		Juan Pino and
		Jovan Popovic and
		Omid Poursaeed and
		Fabian Prada and
		Alice Rakotoarison and
		Alexander Richard and
		Christophe Ropers and
		Safiyyah Saleem and
		Vasu Sharma and
		Alex Shcherbyna and
		Jia Shen and
		Jie Shen and
		Anastasis Stathopoulos and
		Anna Sun and
		Paden Tomasello and
		Tuan Tran and
		Arina Turkatenko and
		Bo Wan and
		Chao Wang and
		Jeff Wang and
		Mary Williamson and
		Carleigh Wood and
		Tao Xiang and
		Yilin Yang and
		Zhiyuan Yao and
		Chen Zhang and
		Jiemin Zhang and
		Xinyue Zhang and
		Jason Zheng and
		Pavlo Zhyzheria and
		Jan Zikes and
		Michael Zollhoefer
  },
  url={https://ai.meta.com/research/publications/seamless-interaction-dyadic-audiovisual-motion-modeling-and-large-scale-dataset/},
  year={2025}
}
```
</details>

## 🙏 Acknowledgments

This project was made possible thanks to contributions from:

- The thousands of participants who provided interaction data
- Our dedicated annotation and QA team
- Research collaborators from multiple institutions
- FAIR (Fundamental AI Research)
- The open-source community for valuable tools and libraries
- Our data collection partners across multiple sites
- Meta Reality Labs for supporting this research initiative
