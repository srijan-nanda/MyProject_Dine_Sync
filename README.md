# Predicting Gait Deviation Index (GDI) from Walking Keypoints

UE24CS352A Machine Learning — Mini-Project

**Team:** Swara Ashish Potdar (PES1UG24CS487), Srijan Kumar (PES1UG24CS467)

We predict a patient's **Gait Deviation Index (GDI)** — a clinical score of how abnormal someone's walking is (about 100 = typical walking, lower = more abnormal) — from 2D body keypoints extracted from ordinary walking videos. This is a **regression** problem.

Based on: [Automated Identification of Gait Abnormalities (Stanford CS229, 2018)](https://cs229.stanford.edu/proj2018/report/31.pdf). That paper's DensePose video data is private, so we use the public OpenPose keypoint dataset released by the same research group ([mobile-gaitlab](https://github.com/stanfordnmbl/mobile-gaitlab)).

## Dataset

1. Download **video-gait-v1.zip** (3.1 GB) from https://simtk.org/frs/?group_id=1918 (doi:10.18735/j0rz-0k12; data under CC BY-NC 2.0).
2. **Do not unzip it.** It holds about 3.7 million small OpenPose JSON files (one per video frame) plus label tables in `annotations/`. Convert it straight from the zip:
   ```bash
   python src/convert_dataset.py --zip "path/to/video-gait-v1.zip"
   ```
   This creates:
   ```
   data/raw/video_csvs/<videoid>.csv     # one file per video: frames x 75 (25 OpenPose joints x (x, y, confidence)), 25 fps
   data/raw/alldata_processed.csv        # labels: videoid, Patient_ID, examid, side, GDI
   ```
   Following the original research code, GDI comes from `annotations/gdi.csv` (linked to videos via `annotations/video_list.csv`), only videos with a GDI for both legs are kept, and the patient is the person in the left half of the frame.

The data is **not** stored in this repo (see `.gitignore`).

## Setup

```bash
pip install -r requirements.txt
```
Tested with Python 3.10+. Runs on a normal laptop or Google Colab (no GPU needed for these steps).

## How to run (in order)

```bash
python src/convert_dataset.py --zip <path to video-gait-v1.zip>   # once: zip -> per-video CSVs
python src/explore.py          # data facts + exploration plots       -> results/
python src/preprocess.py       # clean, normalise, window, split      -> data/processed/
python src/train_baselines.py  # features + baseline models           -> results/baselines.csv
python src/train_lstm.py       # deep models (LSTM + 1D CNN)       -> models/, results/all_models.csv
python src/demo.py --video my_walk.mp4   # live demo on a walking video (uses models/cnn.pt, the best model)
python src/demo.py --from_test 5         # backup demo on 5 unseen test-set videos
```

`train_lstm.py` uses a GPU automatically if one is available (e.g. Colab); on a laptop CPU it takes a few minutes per model.

**No data yet?** Test the pipeline on fake data in the same format (never report these scores):
```bash
python src/make_sample_data.py --out data/sample
python src/preprocess.py --video_dir data/sample/video_csvs --labels data/sample/alldata_processed.csv
python src/train_baselines.py
```

## Data format (shared between all scripts — do not change without telling the team)

| File | Shape | Meaning |
|---|---|---|
| `X_train.npy`, `X_val.npy`, `X_test.npy` | (windows, 100, 12) | keypoint sequences |
| `y_train.npy`, `y_val.npy`, `y_test.npy` | (windows,) | GDI (average of left and right side) |
| `meta_train.csv`, ... | (windows, 2) | `videoid`, `patient_id` of each window |

- **100 frames per window** (4 seconds at 25 fps), non-overlapping. Each window gets its video's GDI.
- **12 features per frame**, in this order: `LHIP_x, LHIP_y, LKNE_x, LKNE_y, LANK_x, LANK_y, RHIP_x, RHIP_y, RKNE_x, RKNE_y, RANK_x, RANK_y`.
- **Missing values** (OpenPose writes 0/NaN) are filled by linear interpolation; videos with more than 30% missing are dropped.
- **Normalisation** (`normalise()` in `src/preprocess.py`): centre on the mid-point of the hips in every frame, then divide by average leg length (hip-to-ankle). The demo must import this same function.
- **Split:** 70 / 15 / 15 **by patient** (`GroupShuffleSplit`, `random_state=42`), so no patient appears in two sets.

## Method

1. **Preprocessing** — select leg joints, fill missing keypoints, normalise, cut into 100-frame windows, split by patient.
2. **Features** (`src/features.py`) — knee angle range/mean/min, ankle height range, steps per second, left–right differences, and mean/std of every joint coordinate (38 features per window).
3. **Baselines** — mean guess, Linear (Ridge) Regression, Random Forest, XGBoost. Settings picked on validation; final scores on the test set, per window and per video (average of a video's windows).
4. **Deep models** (`src/models.py`, `src/train_lstm.py`) — an LSTM (2 layers, 64 units) and a 1D CNN (3 convolution blocks) on the raw keypoint sequences. Each of the 12 inputs is standardised with training-set mean/std (stored inside the model). Trained with Adam on scaled GDI, with dropout, weight decay, gradient clipping and early stopping on validation RMSE. The LSTM summarises the sequence with the average and final hidden state.
5. **Demo** (`src/demo.py`) — MediaPipe Pose finds the same 6 leg joints in a new video, which is resampled to 25 fps and passed through the same `fill_missing()` and `normalise()` functions as the training data. The predicted GDI is the average over 4-second windows. This demonstrates the pipeline; it is not a medical assessment.

Metrics: **RMSE** (average error in GDI points, lower is better) and **R²** (share of variation explained, 1 is perfect).

## Results (test set: 293 patients never seen in training)

Scores are per video (the average of a video's 4-second window predictions). Full table with per-window scores: `results/all_models.csv`.

| Model | Input | Test RMSE (GDI points) | Test R² |
|---|---|---|---|
| Mean guess | – | 12.04 | −0.01 |
| Linear Regression | 38 gait features | 9.40 | 0.38 |
| Random Forest | 38 gait features | 8.59 | 0.48 |
| XGBoost | 38 gait features | 8.32 | 0.52 |
| LSTM | keypoint sequence | 8.11 | 0.54 |
| **1D CNN** | keypoint sequence | **7.71** | **0.58** |

The 1D CNN is the best model: it cuts the error of guessing the average by about 36%. Models that read the full movement sequence beat the feature-based models, matching the original paper's finding that movement over time matters most.

Plots: `results/gdi_histogram.png`, `results/knee_normal_vs_abnormal.png`, `results/feature_importance.png`, `results/true_vs_pred_baseline.png`.

## Folder structure

```
├── README.md
├── requirements.txt
├── data/            # not in git: raw/ and processed/
├── notebooks/       # exploration notebooks
├── src/
│   ├── convert_dataset.py
│   ├── explore.py
│   ├── preprocess.py
│   ├── features.py
│   ├── train_baselines.py
│   ├── make_sample_data.py
│   ├── models.py
│   ├── train_lstm.py
│   └── demo.py
├── models/          # trained model files (lstm.pt, cnn.pt)
└── results/         # plots and score tables
```

## References

- Gotlin, Pancholi, Agarwal. *Automated Identification of Gait Abnormalities.* Stanford CS229, 2018.
- Kidziński et al. *Deep neural networks enable quantitative movement analysis using single-camera videos.* Nature Communications, 2020. Code and data: [mobile-gaitlab](https://github.com/stanfordnmbl/mobile-gaitlab) (data under CC BY-NC 2.0).
- Schwartz & Rozumalski. *The Gait Deviation Index.* Gait & Posture, 2008.
