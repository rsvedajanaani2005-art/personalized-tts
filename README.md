# Personalized Text-to-Speech with Tacotron 2

A small experimental project exploring how the amount of speaker-specific speech data affects personalized text-to-speech synthesis.

The project contains three Kaggle/Jupyter notebooks:

| Notebook | Speaker utterances | Training / validation split | Purpose |
|---|---:|---:|---|
| `Speech-ASG1-100-utterances.ipynb` | 100 | 90 / 10 | Initial fine-tuning experiment |
| `Speech-ASG2-200-utterances.ipynb` | 200 | 180 / 20 | Expanded-data experiment |
| `Speech-ASG3-300-utterances.ipynb` | 300 | 270 / 30 | Largest-data experiment |

The workflow uses **SpeechBrain Tacotron 2** for acoustic-model fine-tuning and **HiFi-GAN** for waveform generation. The experiments are intended to compare the audible similarity and naturalness of synthesized speech as the amount of speaker-specific training data increases.

> **Important:** These are experimental comparisons, not a controlled benchmark. Unless the same test sentences, training conditions, checkpoints, and evaluation protocol are held constant, differences should be described as qualitative observations rather than proof that dataset size alone caused the change.

## Repository contents

```text
personalized-tts/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   ├── Speech-ASG1-100-utterances.ipynb
│   ├── Speech-ASG2-200-utterances.ipynb
│   └── Speech-ASG3-300-utterances.ipynb
└── docs/
    └── experiment-notes.md
```

The audio dataset, transcript metadata, pretrained model weights, fine-tuned checkpoints, and generated audio are **not included** in this repository.

## How to run

These notebooks were prepared for a Kaggle environment with GPU acceleration.

1. Download or clone this repository.
2. In Kaggle, create a notebook or import one of the notebooks under `notebooks/`.
3. Attach your own audio dataset to the Kaggle notebook. The repository intentionally does not contain the recordings.
4. Update `AUDIO_DIR` and `SOURCE_METADATA` (or the corresponding metadata path variable) in the notebook to point to your mounted Kaggle inputs. Replace `YOUR_USERNAME` with your Kaggle username and use the actual dataset slugs shown in the Kaggle input panel.
5. Confirm that the recordings and metadata match before training.
6. Run the notebook from top to bottom. Start with ASG1, then ASG2, then ASG3.
7. Check the notebook's final cells for synthesis and the saved output path.

### Expected data layout

For each experiment, the audio folder should contain mono WAV files named consistently with the metadata IDs:

```text
your-audio-dataset/
├── 001.wav
├── 002.wav
├── ...
└── 300.wav
```

The metadata is pipe-delimited, with no header, and uses three columns:

```text
001|Transcript sentence.|Transcript sentence.
002|Another transcript sentence.|Another transcript sentence.
```

Use the corresponding number of rows for each experiment. Audio filenames, metadata IDs, and spoken transcripts must match. The 200- and 300-utterance notebooks expect the matching audio recordings to be available; the transcript metadata alone is not enough to train the model.

## Experiment settings

The 200- and 300-utterance notebooks use the same broad configuration:

- Model: SpeechBrain Tacotron 2
- Vocoder: HiFi-GAN
- Training epochs: 15
- Batch size: 4
- Learning rate: `0.00005`
- Maximum gradient norm: `1.0`
- Audio sample rate: 22,050 Hz mono WAV
- Split: 90% training / 10% validation

The 100-utterance notebook is the initial experiment and may contain different details. Refer to each notebook's configuration cells for its exact setup. The 200- and 300-utterance notebooks start from the original pretrained Tacotron 2 model, not from each other's fine-tuned checkpoint.

## Results

Initial qualitative observation: the 300-utterance experiment (ASG3) sounded more similar to the target voice than the 100-utterance and 200-utterance experiments in the listening comparison.

This is a **subjective listening observation**, not a numerical result. Add a short, fixed test sentence and a listening comparison if you want readers to hear the difference. Only publish synthesized voice samples if you are comfortable making your voice publicly available. Do not upload the original recordings.

Suggested results table to fill in after your final comparison:

| Experiment | Number of utterances | Test sentence | Listening observation | Objective metrics |
|---|---:|---|---|---|
| ASG1 | 100 | Add sentence | Add observation | Add only if measured |
| ASG2 | 200 | Same sentence | Add observation | Add only if measured |
| ASG3 | 300 | Same sentence | Add observation | Add only if measured |

For a fair comparison, synthesize the same unseen test sentence with all three checkpoints, use the same vocoder and output settings, and document the training configuration. Do not claim a WER, MOS, speaker-similarity score, or other metric unless you actually measured it.

## Dependencies and compute

The notebooks install the packages they need in the Kaggle environment. `requirements.txt` lists the main direct dependencies, but exact versions may need to be pinned to reproduce a particular run. A GPU runtime is recommended.

## Privacy and large files

Do not commit:

- Your original voice recordings or full audio dataset
- Metadata/transcript files if you do not want to redistribute the text
- Model checkpoints or pretrained weights
- Generated WAV files unless you deliberately choose to publish a short demo
- Kaggle secrets, API tokens, credentials, or private paths

Notebook outputs have been cleared in the repository copies to avoid accidentally publishing saved outputs, logs, or audio. If you want to share demo audio, create a separate `demo/` directory and add only selected synthesized samples after reviewing them.

## Acknowledgements

- [SpeechBrain](https://github.com/speechbrain/speechbrain) — speech processing toolkit and Tacotron 2 implementation.
- [HiFi-GAN](https://github.com/jik876/hifi-gan) — neural vocoder architecture.
- Cite the specific pretrained checkpoint/model card you use, and review its license and usage terms before redistributing weights or outputs.

## Reproducibility note

These notebooks were prepared for Kaggle and have not been verified as a complete fresh-session run from this repository. Input paths, package versions, pretrained-model availability, and Kaggle GPU availability can affect execution.
