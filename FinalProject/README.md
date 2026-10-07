# APK 6725 Final Project: Gait, injury, and the trouble with speed

Can a machine-learning model tell an injured runner from a healthy one from their
walking gait? You will build an honest pipeline to test this, with gait features
and raw joint-angle curves from ~1,600 people.

## Files

| File | What it is |
| --- | --- |
| `Final-Project.ipynb` | The project. Instructions, milestones, report and slide outlines, grading. Start here. |
| `DATA_DICTIONARY.md` | What each column in the feature tables means. Read before you start. |
| `CURVES_README.md` | How to load and use the raw joint-angle curves. |
| `features_walk.csv` | Gait features, one row per person, walking (main project). |
| `features_run.csv` | Gait features, one row per person, running (stretch goal only). |
| `curves_walk.npz` | Raw joint-angle curves for walking (Milestone 5 and the optional Milestone 8). |

## How to start

1. Download or clone this folder. Keep the notebook and the data files in the same
   folder.
2. Install the software: `numpy`, `pandas`, `matplotlib`, `scikit-learn`. The
   optional Milestone 8 also needs `torch` (PyTorch). Google Colab has all of these.
   (If you use Colab, upload the data files to the Colab session first.)
3. Open `Final-Project.ipynb` and work through the milestones in order.

## What you submit

Work in a group of 2 to 4 people. You do **not** submit the notebook. You submit:

* a **written report** (Arial 11 pt, maximum 10 pages, APA references), and
* a **10-minute PowerPoint presentation** to the class.

The notebook explains the scope for each group size, gives suggested outlines for
the report and the slides, and shows how both are graded.

## Data source and license

The data come from the Running Injury Clinic gait dataset:

> Ferber, R., Brett, A., Fukuchi, R. K., Hettinga, B., & Osis, S. T. (2024).
> A Biomechanical Dataset of 1,798 Healthy and Injured Subjects During Treadmill
> Walking and Running. *Scientific Data* 11, 1232.
> https://doi.org/10.1038/s41597-024-04011-7

The tables and curves here were computed from the original marker data for this
course. Use them for course work only. Do not use them for commercial purposes,
and cite the paper above if you use the data anywhere else.
