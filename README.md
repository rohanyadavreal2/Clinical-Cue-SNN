# Clinical Cues as Spiking Neurons

A seizure detector for EEG that reads recordings the way a neurologist does. Spiking-neurons detect nine clinical seizure cues, and two small logistic readouts (**866 learned weights**) turn those cues into alarms.

Developed by Rohan Yadav at the Center for Translational Cognitive Neuroscience (CTCN), VA Boston Healthcare System (Jamaica Plain) / Boston University.
(Please note there will be more evaluation done at a later date.)
---

## Results

### Held-out patients (TUSZ v2.0.6 eval: 880 recordings, 129 h, 360 seizures)

| | This model | SeizureTransformer |
|---|---|---|
| Learned parameters | **866** | 41 million |
| Seizures caught at exactly 1 false alarm/hour* | **72%** | 58% |
| Per-second AUROC | **0.925** | 0.893 |
| With settings fixed on dev: caught, false alarms/hour | 58.9%, 0.46 | 53.1%, 0.77 |
| Precision (share of alarms that are real seizures) | **79%** | 67% |
| Median detection delay | 4.1 s | 5.0 s |

Each model's threshold is set to give exactly one false alarm per hour on eval for a fair comparison. SeizureTransformer (2025 seizure-detection challenge winner) was run with its released weights and scored with the same pipeline.

### External test (Siena Scalp EEG Database: 14 patients, 141 h, 47 seizures, recorded in Italy)

Heads and settings from TUSZ, applied once without retuning: **62% caught at 0.66 false alarms/hour**, per-second AUROC 0.870.

---

## How it works

```
EEG (22 bipolar channels, 250 Hz)
  → spike encoders (rate, delta-sigma, temporal)
  → fixed clinical-cue neurons (72 outputs per channel)
  → per second: mean and max over channels (144 numbers)
  → two logistic heads: short (this second) and long (+ past 5–60 s)
  → score = max(short, long) → smoothing, hysteresis → alarms
```

| Clinical cue | Neurons |
|---|---|
| 1 · A new rhythm | Rhythm ALIF, resonator bank (1.5–40 Hz) |
| 2 · Steady rhythm | Regularity, persistence |
| 3 · Evolution in frequency | Frequency drift, slow drift |
| 4 · Sharp waves | Sharpness, spike-and-wave, rhythmic-sharp |
| 5 · Quiet onsets | Silence, fast activity, normalized line length |
| 6 · Focal start | Hemisphere comparator, local contrast + top-3 |
| 7 · Spread | Delay-line spread neurons |
| 8 · Sudden start | Onset |
| 9 · Not an artifact | Synchrony + artifact signatures, artifact gate |

Only the two heads learn. Each neuron's thresholds are calibrated once from training data and then fixed.

A full model and neuron-by-neuron breakdown is in the accompanying slides.

Publication coming soon...

---

## AI assistance

This project was developed with AI assistance (Claude, Gemini) for writing code and analysing results.

## Acknowledgments

Thanks to Dr. Brandon Frank and the Center for Translational Cognitive Neuroscience for their support! Without them none of this would have been possible.

## Code and technical slides available upon request, email rohan.yadav2023@gmail.com
