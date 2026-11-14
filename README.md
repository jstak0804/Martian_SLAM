# Martian Map — SLAM & Terrain AI Research Track

This repository holds the **research track** that grew alongside our NASA Space Apps 2026 entry
for *Interplanetary Survival Guide: Martian Map*. The submitted product is the
**Mars EVA Walkback & Survival Planner**, which lives in a separate repository:
<https://github.com/jstak0804/martian-map-slam>.

The planner answers "where can an astronaut still walk back to base from?" using satellite DEMs.
This repository asks the follow-up question: **what if the map itself is not perfect?** It studies
how a rover could build the map incrementally (SLAM), how terrain classes could be learned from
rover images, and how map uncertainty would propagate into risk maps.

> 이 저장소는 해커톤 제출물(지도 기반 EVA 귀환 반경 플래너)과 별도로 진행한 SLAM·지형 인식 연구 기록입니다.
> 제출물은 <https://github.com/jstak0804/martian-map-slam> 에 있습니다. 원래 저장소의 README는
> `README_original_planner_repo.md`에 그대로 남겨 두었습니다.

## Contents

| Topic | Code (`code/`) | Write-up (`reports/`) | Outputs |
|---|---|---|---|
| Visual odometry on Devon Island rover data (stills vs video) | `devon_pipeline.py`, `devon_vo_sun_correction.py`, `plot_trajectory.py` | `03-slam-demo.html` | — |
| Exploration SLAM simulation (frontier exploration, occlusion-aware scans, VO drift) | `exploration_slam_sim.py`, `exploration_*.py`, `anisotropic_risk_exploration.py` | `05-exploration-slam.html` | `outputs/exploration/` |
| Whole-map SLAM on real HiRISE terrain (loop closure) | `slam_site_exploration.py`, `slam_navigation_*.py`, `slam_*_video.py` | `12-slam-navigation-integration.md` | `outputs/slam_sites/`, `outputs/slam_navigation/` |
| Terrain segmentation (AI4Mars U-Net, multi-rover, 512 px) | `train_terrain_segmentation*.py`, `fetch_ai4mars_*.py`, `eval_*`, `expand_holdout_set.py`, `hackathon/finetune_segmentation.py` | `08-trained-model.html` | `outputs/terrain_segmentation*/` |
| ConeQuest cone detection and risk fusion | `conequest_*.py`, `cascade_fusion_detection.py`, `boulder_detection_no_labels.py` | `10-conequest-integration.*` | `outputs/conequest*/` |
| Lunar LOLA terrain risk and heatmaps | `real_terrain_risk.py`, `moon_multi_patch.py`, `export_risk_grid_for_demo.py`, `terrain_demo.py`, `threshold_sensitivity.py` | `02-risk-heatmap-demo.html`, `06-real-terrain-risk.html` | `outputs/real_terrain/` |
| Human-mission navigation prototypes | `human_mission_navigation.py`, `human_mission_risk.py`, `first_person_walk.py` | `11-human-mission-navigation.*` | `outputs/human_mission_navigation/` |
| Early design notes | — | `01-brainstorm.html`, `04-mmgis-integration.html`, `07-interactive-pathfinding.html`, `09-progress-review.html` | — |

`exploration_slam_sim.py`, `anisotropic_risk_exploration.py`, `real_terrain_risk.py`,
`human_mission_*.py` and `metabolic_navigation.py` are also kept in the planner repository because the
planner imports their shared terrain utilities.

## Key results (from the write-ups)

- Whole-map SLAM on HiRISE terrain: VO-only position error grows to hundreds of metres to kilometres; loop
  closure brings it down to tens of metres and raises high-risk-zone IoU (Jezero 0.15 → 0.63, Aram 0.58 → 0.85).
- Devon Island VO: 2.3 % error with still frames vs 30.9 % with video frames.
- Selective trust: masking cells by modelled position uncertainty improved risk IoU 0.199 → 0.290 (10 seeds).

## Running

```bash
pip install -r code/requirements.txt          # map / SLAM simulation
pip install -r code/requirements-train.txt    # segmentation training (PyTorch)
cd code && python exploration_slam_sim.py
```

Large raw datasets (HiRISE DTMs, AI4Mars, ConeQuest) are not included; the fetch scripts download them.
