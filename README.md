# IAVTrack — Supplementary Material

Supplementary material for the manuscript submitted to *Remote Sensing* (MDPI):

> **&lt;FILL IN PAPER TITLE&gt;**

This repository contains the source code, the trained model checkpoints and the raw
tracking results needed to reproduce every table and figure of the paper.

## 1. Files

| File | Size | MD5 | Contents |
|---|---|---|---|
| [`IAVTrack-code-v1.0.0.zip`](../../releases/latest) | 1.01 MB | `f64199fdfd5b4cba769c62493e52d10e` | Source code, experiment configs, environment file (280 files) |
| `IAVTrack-checkpoints.zip` | 1,843.80 MB | `07931bcd6d3d6683d4cebfa6e0378525` | 12 trained model checkpoints (`.pth.tar`) — see [Releases](../../releases/latest) |
| [`IAVTrack-tracking-results.zip`](IAVTrack-tracking-results.zip) | 15.50 MB | `8bbec99d6ffd03c85ff2691a14d14c76` | Raw tracking results (7119 `.txt` files) |
| [`checksums.md5`](checksums.md5) | — | — | MD5 checksums of the three archives |

Verify integrity with:

```bash
md5sum -c checksums.md5
```

## 2. Source code

Full training and evaluation code of IAVTrack. Main entry points:

```bash
# Training
python tracking/train.py --script avtrack --config <config_name> --save_dir ./output

# Evaluation (DTB70 / UAVDT)
python tracking/test.py avtrack <config_name> --dataset_name dtb70 uavdt
```

The code is built upon [AVTrack](https://github.com/wuyou3474/AVTrack); the files
originating from AVTrack are redistributed under the original MIT License
(Copyright (c) 2024 You Wu, see `LICENSE`).

The software environment is pinned in `environment_backup.yml`. Third-party
packages: PyTorch, timm, easydict, tensorboardX (all listed in `requirements.txt`).

## 3. Trained checkpoints

Layout: `checkpoints/train/avtrack/<config_name>/AVTrack_ep<NNNN>.pth.tar`

| Config | Epoch | Size |
|---|---|---|
| `ablation_00_baseline_got10k` | 45 | 241.0 MB |
| `ablation_01_hfda_only` | 80 | 244.3 MB |
| `ablation_01_msca_hfda_v2` | 80 | 246.1 MB |
| `ablation_01_msca_only` | 80 | 244.8 MB |
| `ablation_02_bddr_bcr` | 80 | 246.3 MB |
| `ablation_02_bddr_v2` | 80 | 246.3 MB |
| `ablation_02_bddr_wo_debias` | 80 | 246.3 MB |
| `ablation_02_bddr_wo_neg` | 80 | 246.3 MB |
| `ablation_stage2_msca_bddr_got10k_stage2` | 45 | 106.0 MB |
| `ablation_stage2_msca_bddr_mixed_v2` | 80 | 107.2 MB |
| `ablation_stage2_msca_bddr_mixed_v2_nofreeze` | 80 | 250.3 MB |
| `deit_tiny_patch16_224_30ep_baseline` | 80 | 242.1 MB |

The backbone is initialized from timm's DeiT-tiny ImageNet-1k weights, which are
**not** redistributed here and can be downloaded from the
[timm model zoo](https://github.com/huggingface/pytorch-image-models).

## 4. Tracking results

Layout: `tracking_results/avtrack/<config_name>/<dataset_name>/<sequence>.txt`

Each file holds one predicted bounding box per line (`x,y,w,h`) for the
corresponding frame. The archive covers the DTB70 and UAVDT test sets for all
20 evaluated configurations, i.e. the raw predictions behind the success /
precision numbers reported in the ablation tables.

Note: a few configurations were evaluated without a corresponding checkpoint
being released (`ablation_atc_*`, `ablation_bddr_atc`, `ablation_upd_*`); their
result files are included for completeness of the ablation study.

## 5. Reproducing the paper

```bash
unzip IAVTrack-code-v1.0.0.zip
unzip IAVTrack-checkpoints.zip   # -> ./output/checkpoints/...
unzip IAVTrack-tracking-results.zip

python tracking/test.py avtrack ablation_stage2_msca_bddr_mixed_v2 \
    --dataset_name dtb70 uavdt
```

Re-running the evaluation should reproduce the released results in
`output/test/tracking_results/`.

## 6. License

Code: MIT License (see `LICENSE`). Checkpoints and result files: released for
research use; please cite the paper and the original AVTrack work.

## 7. Citation

```
<FILL IN PAPER CITATION / DOI AFTER ACCEPTANCE>
```
