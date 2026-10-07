# Gait dataset for the final project — data dictionary

Running Injury Clinic dataset (Ferber et al. 2024), curated for APK 6725.

> Ferber, R., Brett, A., Fukuchi, R. K., Hettinga, B., & Osis, S. T. (2024).
> A Biomechanical Dataset of 1,798 Healthy and Injured Subjects During Treadmill
> Walking and Running. *Scientific Data* 11, 1232.
> https://doi.org/10.1038/s41597-024-04011-7 — course use only, non-commercial.

## Two tables — walking and running are separate

In each session a participant did a ~60 s **walk** and a ~60 s **run**, both at a
self-selected speed. They are different movements (walking has a double-support
phase; running has a flight phase and far higher joint powers), so they are kept
in **two separate files**. A participant may appear in both. Build a model for
one activity at a time; do not mix walking and running rows.

* `features_walk.csv` — one row per participant, walking (used in the main project)
* `features_run.csv`  — one row per participant, running (stretch goal only)
* `curves_walk.npz` — the raw signals for walking: 12 joint-angle curves per person
  (hip, knee, ankle x sagittal, frontal x right, left), 101 points of stance. Match
  them to the tables by `sub_id`. About 1,580 people have curves. See
  `CURVES_README.md`.

**One row per participant.** Some people were tested more than once; only their
first session is kept, so each participant appears once and an ordinary
train/test split is valid (no need for group-aware splitting on these tables).

**Required protocol.** Split train/test first (stratified on the label). Do all
exploring, model choice and tuning with cross-validation on the train set only.
Fit scalers inside a `Pipeline`. Score the test set once, at the end.

**Symmetry columns are signed.** `sym_*` is based on (right minus left). The injured
leg is the right leg in some people and the left leg in others, so the sign can
cancel. Try the absolute value too.

## Columns

| Column | Meaning | Units |
| --- | --- | --- |
| `sub_id` | participant id | |
| `age` | age | years |
| `sex` | Male / Female | |
| `height_cm` | height | cm |
| `mass_kg` | body mass | kg |
| `bmi` | body mass index | kg/m² |
| `injury_status` | `healthy`, `injured`, or `unclear` | |
| `injury_joint` | injured joint (blank if healthy) | |
| `specific_injury` | diagnosis (blank if healthy) | |
| `speed` | self-selected speed (walking or running) | m/s |
| `cadence` | strides per minute | 1/min |
| `stride_length` | distance per stride | m |
| `stance_time` | time the foot is on the ground | s |
| `vertical_oscillation` | up-and-down travel of the body per step | mm |
| `peak_knee_flexion` | maximum knee flexion in stance | deg |
| `peak_hip_extension` | maximum hip extension in stance | deg |
| `peak_ankle_dorsiflexion` | maximum ankle dorsiflexion in stance | deg |
| `peak_hip_adduction` | maximum hip adduction (hip drop) in stance | deg |
| `peak_pelvic_drop` | maximum contralateral pelvic drop | deg |
| `peak_eversion` | maximum rearfoot eversion (pronation) | deg |
| `sym_knee_flexion` | left/right symmetry of peak knee flexion | % |
| `sym_stance_time` | left/right symmetry of stance time | % |
| `stride_time_cv` | stride-to-stride variability of stride time (coefficient of variation) | % |

## How to read the features

* **Temporal / spatial** (`cadence`, `stride_length`, `stance_time`,
  `vertical_oscillation`) are the kind of measures you also get from wearables.
  Note that `cadence` × `stride_length` is essentially speed — using both to
  predict speed is close to cheating (a good leakage example).
* **Kinematic peaks** are per-participant magnitudes, averaged over the left and
  right legs. Signs are set so the named quantity reads as a positive magnitude.
  `peak_hip_adduction`, `peak_pelvic_drop` and `peak_eversion` are the variables
  most often linked to running injury.
* **Symmetry** is `200 × (Right − Left) / (|Right| + |Left|)`, in percent. 0 means
  perfectly symmetric. **Caveat:** this blows up when a measure is near zero
  (right and left straddling 0), so watch for extreme values.
* **Variability** (`stride_time_cv`) is the coefficient of variation of stride
  time across the steps of the trial — a wearable-style measure of how regular
  the gait is. Detected from foot-marker trajectories (validated: mean cadence
  matches the authors' stride rate). Typical healthy walking is ~1-2%. Higher
  variability is a known marker of gait problems in frail and clinical groups.

## What is NOT here (on purpose)

* The transverse-plane (rotation) angles — their absolute value is unreliable in
  marker capture, so they are left out.
* Joint moments, powers, and ground reaction forces — this dataset is markers
  only (no force plates), so there are no kinetic variables.

## A note on the labels

"Injured" means a clinically diagnosed running-related lower-limb injury, but the
participants were **pain-free during the test**, so the injury signal is subtle.
Walking speed changes almost every gait measure, so check whether the groups
differ in speed before you interpret any model. Expect a **class imbalance**
(about three injured people for every healthy person). About 4-5% of `injury_status` is `unclear`;
drop those for a clean two-group task.
