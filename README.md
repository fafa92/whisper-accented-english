[README.md](https://github.com/user-attachments/files/25336474/README.md)
# Whisper Large-v3 Fine-Tuning for Accented English ASR

Fine-tuning [Whisper large-v3](https://huggingface.co/openai/whisper-large-v3) (1.5B parameters) with LoRA on 100+ hours of accented English speech to improve ASR performance on underrepresented English accents.

## Approach

- **Base model**: OpenAI Whisper large-v3
- **Fine-tuning**: QLoRA (rank=32, alpha=64, 4-bit quantization) targeting attention and feed-forward layers
- **Training**: 15 epochs, cosine LR schedule, effective batch size 32
- **Hardware**: Single NVIDIA A10 (24GB VRAM), ~140 hours total training time

## Datasets

| Dataset | Hours | Description |
|---------|-------|-------------|
| [EdAcc](https://huggingface.co/datasets/edinburghcstr/edacc) | ~40h | Diverse international accents, conversational English over Zoom |
| [English Dialects](https://huggingface.co/datasets/ylacombe/english_dialects) | ~31h | British Isles regional accents (Irish, Scottish, Welsh, Northern, Midlands, Southern) |
| [Common Voice v24 en-AU](https://datacollective.mozillafoundation.org/datasets/cmko7havo02f5nw07rbwwhowe) | ~40h | Australian English subset via Mozilla Data Collective (CC0-1.0) |

## Benchmark

Evaluated against the [EdAcc Leaderboard](https://groups.inf.ed.ac.uk/edacc/leaderboard.html), which benchmarks ASR systems on accented English. The leaderboard uses WER scored via `sclite`. Note: two evaluation protocols exist (V0.1 conversation-level, V1.0 utterance-level) with non-comparable scores.

## Usage

```bash
# Install dependencies (Cell 1), restart runtime, then run from Cell 2
pip install torch torchvision torchaudio
pip install transformers accelerate datasets[audio] peft bitsandbytes evaluate jiwer librosa
```

The full training script is in `whisper_accent_finetune.py`. It handles dataset downloading, preprocessing, training, and evaluation end-to-end.

## Citations

```bibtex
@inproceedings{sanabria23edacc,
  title={The Edinburgh International Accents of English Corpus: Towards the Democratization of English ASR},
  author={Sanabria, Ramon and Bogoychev, Nikolay and Markl, Nina and Carmantini, Andrea and Klejch, Ondrej and Bell, Peter},
  booktitle={ICASSP 2023},
  year={2023}
}

@article{radford2023robust,
  title={Robust Speech Recognition via Large-Scale Weak Supervision},
  author={Radford, Alec and Kim, Jong Wook and Xu, Tao and Brockman, Greg and McLeavey, Christine and Sutskever, Ilya},
  journal={ICML},
  year={2023}
}

@inproceedings{ardila2020common,
  title={Common Voice: A Massively-Multilingual Speech Corpus},
  author={Ardila, Rosana and Branez, Megan and Davis, Kelly and Henretty, Michael and Kohler, Michael and Meyer, Josh and Morais, Reuben and Saunders, Lindsay and Tyers, Francis and Weber, Gregor},
  booktitle={LREC},
  year={2020}
}
```

## License

The training code is MIT. Dataset licenses vary: EdAcc (CC-BY-4.0), English Dialects (check source), Common Voice v24 en-AU (CC0-1.0).
