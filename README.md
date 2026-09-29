# IAVTrack — Supplementary Material

Supplementary material for the manuscript submitted to *Remote Sensing* (MDPI):

> **IAVTrack: A single-stream visual tracking method for UAV scenarios with enhanced
> spatio-temporal and channel discrimination**
>
> Tianhang Sun ^1^ (ORCID [0009-0007-2554-8506](https://orcid.org/0009-0007-2554-8506)), Xiaoqi He ^2,\*^
>
> ^1^ School of Automation and Intelligent Sensing, Shanghai Jiao Tong University, Shanghai 200240, China; sth2025@sjtu.edu.cn
> ^2^ Ningbo Institute of Artificial Intelligence, Shanghai Jiao Tong University, Ningbo, China; hexiaoqi@sjtu-naii.com
>
> \* Correspondence: hexiaoqi@sjtu-naii.com

This repository contains the source code, the trained model checkpoints and the raw
tracking results needed to reproduce every table and figure of the paper.

## Zenodo deposit

The complete supplementary material is archived on Zenodo and citable via a
persistent DOI:

**https://doi.org/10.5281/zenodo.23007690**

The 1.84 GB checkpoints archive is deposited as a 90-part split archive
(`IAVTrack-checkpoints.zip.part01` ... `part90`) to stay within the Zenodo
per-file limits. Download all parts, then follow `REASSEMBLE.md` to rebuild
`IAVTrack-checkpoints.zip` byte-for-byte (MD5 `07931bcd6d3d6683d4cebfa6e0378525`)
before unpacking.

## 1. Files

| File | Size | MD5 | Contents |
|---|---|---|---|
| [`IAVTrack-code-v1.0.0.zip`](../../releases/latest) | 1.01 MB | `f64199fdfd5b4cba769c62493e52d10e` | Source code, experiment configs, environment file (280 files) |
| `IAVTrack-checkpoints.zip` | 1,843.80 MB | `07931bcd6d3d6683d4cebfa6e0378525` | 12 trained model checkpoints (`.pth.tar`) — see [Releases](../../releases/latest) |
| [`IAVTrack-tracking-results.zip`](IAVTrack-tracking-results.zip) | 15.50 MB | `8bbec99d6ffd03c85ff2691a14d14c76` | Raw tracking results (7,054 `.txt` files) |
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

# Evaluation - run from the repository root; each script takes no arguments
python tracking/eval_quick.py         # ablation table, UAVDT + DTB70      (AUC / Prec@20)
python tracking/eval_dtb70_uav123.py  # combined table, DTB70 + UAV123     (AUC / Prec@20)
python tracking/eval_got10k.py        # GOT-10k validation                 (AO / SR0.50 / SR0.75)
```

All three scripts follow the same protocol (21 overlap thresholds 0.00-1.00 in
steps of 0.05, trapezoidal AUC plus precision at 20 pixels) and read the
`.txt` predictions produced by the tracker. The evaluated configurations and
datasets are declared in the `TRACKER_CONFIGS` and `DATASETS` constants at the
top of each file, so a single configuration can be selected by editing that
list. Ground-truth and result paths are configured in
`lib/test/evaluation/local.py`.

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
corresponding frame. The archive contains the raw predictions of all evaluated
configurations on the DTB70, UAV123, UAVDT and GOT-10k test sets (7,054 `.txt`
files in total: DTB70 1,960, UAV123 2,214, UAVDT 1,800, GOT-10k 1,080), i.e.
the numbers behind the success / precision values reported in the ablation
tables.

Evaluate them with `python tracking/eval_quick.py` (UAVDT + DTB70),
`python tracking/eval_dtb70_uav123.py` (DTB70 + UAV123) or
`python tracking/eval_got10k.py` (GOT-10k).

Note: a few configurations were evaluated without a corresponding checkpoint
being released (`ablation_atc_*`, `ablation_bddr_atc`, `ablation_upd_*`); their
result files are included for completeness of the ablation study.

## 5. Reproducing the paper

```bash
unzip IAVTrack-code-v1.0.0.zip
unzip IAVTrack-checkpoints.zip   # -> ./output/checkpoints/...
unzip IAVTrack-tracking-results.zip

python tracking/eval_quick.py
```

`eval_quick.py` reports the ablation table on UAVDT + DTB70, including the main
configuration `ablation_stage2_msca_bddr_mixed_v2`; `eval_dtb70_uav123.py`
produces the combined DTB70 + UAV123 table and `eval_got10k.py` the GOT-10k
validation numbers. The scripts skip configurations or sequences whose result
files are absent and print `N/A` instead of aborting.

Re-running the evaluation should reproduce the released results in
`output/test/tracking_results/`.

## 6. License

Code: MIT License (see `LICENSE`). Checkpoints and result files: released for
research use; please cite the paper and the original AVTrack work.

## 7. Citation

```
Tianhang Sun and Xiaoqi He. IAVTrack: A single-stream visual tracking method for UAV
scenarios with enhanced spatio-temporal and channel discrimination. *Remote Sensing*
(MDPI), 2026 (submitted).

Dataset / code: https://doi.org/10.5281/zenodo.23007690
```

Volume, pages and the article DOI will be added once the manuscript is accepted.
