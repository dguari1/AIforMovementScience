# Raw joint-angle curves (`curves_walk.npz`)

One set of curves per person, for the walking task. Use the **same** train/test
split as for `features_walk.csv`. Match by `sub_id`.

## What is in each curve
* **12 curves** per person: hip, knee, ankle, each in the **sagittal** (flexion /
  extension) and **frontal** (adduction / abduction, eversion) plane, for the
  **right** leg (first 6) and the **left** leg (last 6). Right and left are kept
  separate, so asymmetry is available.
* Each curve is the **mean over all stance phases**, from touchdown (0%) to
  toe-off (100%), 101 points. Bad steps were dropped with the dataset authors' own
  rule.
* Units: degrees. Left and right use the same sign convention, so the same value
  means the same thing on both sides (sagittal: positive = hip flexion, knee
  flexion, ankle dorsiflexion).
* The transverse (rotation) plane is left out. Its absolute value is an unreliable
  per-leg offset in marker data.

## How to load

```python
import numpy as np
d = np.load('curves_walk.npz', allow_pickle=True)
X   = d['X']            # (people, 101, 12) float32
chan = d['channels']    # R_hip_sagittal, R_hip_frontal, R_knee_sagittal, R_knee_frontal,
                        # R_ankle_sagittal, R_ankle_frontal, then the same 6 for L_
sub_id = d['sub_id']    # (people,)  same ids as features_walk.csv

mean_curve = (X[:, :, :6] + X[:, :, 6:]) / 2     # right/left mean
asym_signed = X[:, :, :6] - X[:, :, 6:]          # R - L
asym_abs = np.abs(asym_signed)                   # |R - L|
```

Match the curves to your tables by `sub_id`:

```python
pos = pd.Series(np.arange(len(sub_id)), index=sub_id)
train_c = train[train['sub_id'].isin(pos.index)]
Ctr = X[pos[train_c['sub_id']].values]         # curves in the same row order as train_c
```

For a 1-D CNN, put the curves first: `Ctr.transpose(0, 2, 1)` gives
(people, 12 curves, 101 points).

## A note on asymmetry
The table does not say which leg is hurt. The hurt leg is the right in some people
and the left in others. So the **sign** of R-L points either way and averages out
across people. The absolute value |R-L| does not depend on the hurt side.

## Quality control
People were kept only if they had at least 5 good stance phases on each side, and
their curves were physiologically sensible (peak knee flexion 20-90 deg, peak hip
flexion 5-70 deg, no angle beyond 120 deg). A few people in `features_walk.csv`
therefore have no curves (1,579 have curves). Drop them from both train and test.

## Source
Marker trajectories, run through the dataset authors' MATLAB pipeline (joint
angles, step detection, stance normalisation) under GNU Octave. The first 15 s of
steady walking (from 5 s into the trial) was used per person, so each curve is the
mean of about 10-15 steps. Joint-angle output matches the authors' own values.
