# Cerebral palsy gait: keypoints from single-camera videos

Derived from the dataset of:

> Kidziński, Ł., Yang, B., Hicks, J. L., Rajagopal, A., Delp, S. L., & Schwartz, M. H. (2020). Deep neural networks enable quantitative movement analysis using single-camera videos. *Nature Communications*, 11, 4054. https://doi.org/10.1038/s41467-020-17807-z

Original data: `video-gait-v1.zip`, SimTK project "video-gaitlab" (https://simtk.org/projects/video-gaitlab), Gillette Children's Specialty Healthcare. The original files are OpenPose keypoints (BODY_25) for each video frame and the gait-laboratory measures for each exam. No videos are included.

**License:** CC BY-NC 2.0 (non-commercial use, with attribution), the same as the original data. These files are a derived version made for teaching in APK 6725 (University of Florida).

These are the same patients and exams as `GMFCS_classification_data.csv` (lecture 04.03). In 04.03 the inputs come from the motion-capture laboratory. Here the inputs come from a normal video camera.

## Files

### `cp_video_features.csv` (3,258 videos, 1,374 patients)

One row per video (one exam). Many patients have more than one exam, so use `patient_id` as the group when you split the data.

| Column | Meaning |
| --- | --- |
| `video_id`, `patient_id`, `exam_id` | identifiers |
| `diagnosis` | type of cerebral palsy |
| `gmfcs` | GMFCS level 1–4 (0 and 6: not a valid level, do not use) |
| `GDI` | Gait Deviation Index from motion capture, mean of left and right (100 = typical gait; lower = more deviation) |
| `age`, `height`, `mass`, `bmi` | demographics (years, cm, kg, kg/m²) |
| `speed`, `cadence` | measured in the motion-capture laboratory |
| other columns | **video features**, computed from the OpenPose keypoints (see below) |

Video features (computed for APK 6725; lengths are divided by the leg length in pixels, angles are 2D angles in the image, in degrees):

- `knee_angle_range_R/L`, `knee_angle_min_R/L`, `knee_angle_max_R/L`: 5th–95th percentile of the 2D angle hip–knee–ankle (180 = straight leg)
- `hip_angle_range_R/L`: range of the 2D angle neck–hip–knee
- `ankle_sep_x_std`, `ankle_sep_y_std`, `ankle_sep_x_mean`: variation and mean of the distance between the two ankles
- `trunk_lean_mean`, `trunk_lean_std`: angle of the line mid-hip → neck relative to the vertical
- `pelvis_vertical_std`, `shoulder_tilt_std`, `pelvis_tilt_std`: vertical movement of the pelvis, and tilt of the shoulders and pelvis
- `step_freq_per_frame`: dominant frequency of the ankle movement (cycles per frame)
- `knee_range_asymmetry`: difference between the left and right knee ranges
- `frac_frames_missing`: fraction of frames with no detected mid-hip

In each frame, the person with the highest total keypoint confidence was used. Some videos include other people, so some frames can belong to another person.

### `cp_video_trajectories.npz` (1,000 videos, one per patient)

- `keypoints`: array (1000 videos, 326 frames, 13 joints, 2) with the x, y pixel position. NaN = not detected (confidence < 0.2) or after the end of the video.
- `joint_names`: nose, neck, r_shoulder, l_shoulder, mid_hip, r_hip, r_knee, r_ankle, l_hip, l_knee, l_ankle, r_big_toe, l_big_toe
- `video_id`: links to `cp_video_features.csv`
- `fps`: 15. Every second frame of the original was kept. The original frame rate is not in the files; the cadence data suggests about 30 frames/s, so 15 frames/s here is an **estimate**.

```python
import numpy as np, pandas as pd
z = np.load('data/cp_video_gait/cp_video_trajectories.npz')
X = z['keypoints']          # (1000, 326, 13, 2)
features = pd.read_csv('data/cp_video_gait/cp_video_features.csv')
```
