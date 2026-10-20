<div align="center">

# FusePlan — Paper

**Separating Complementarity from Ensemble Effects in Partial Floor Plan Retrieval**

Giuseppe Angelillis · Enrico Di Maria · Gabriele Germini
<br>University of Modena and Reggio Emilia

[![Paper](https://img.shields.io/badge/Paper-PDF-1f4e9c?style=flat-square)](main.pdf)
[![Slides](https://img.shields.io/badge/Slides-PDF-1f4e9c?style=flat-square)](FusePlan%20–%20Presentation.pdf)
[![Code](https://img.shields.io/badge/Code-290990--collab%2FFusePlan-24292f?style=flat-square&logo=github)](https://github.com/290990-collab/FusePlan)
[![Format](https://img.shields.io/badge/Format-CVPR%202026-555?style=flat-square)](main.tex)

[Abstract](#abstract) • [Paper at a glance](#paper-at-a-glance) • [Key results](#key-results) • [Repository](#repository-contents) • [Build](#building-the-paper) • [Citation](#citation)

</div>

> [!NOTE]
> This repository holds the **paper** written on the FusePlan project: the LaTeX source in CVPR 2026 format, the compiled PDF, the presentation slides and the speaker script.
> The **code, configurations, logs and experiments** behind every number are in the project repository, [**290990-collab/FusePlan**](https://github.com/290990-collab/FusePlan).

## Abstract

Architects often search for floor plans starting from an incomplete layout, yet floor-plan retrieval methods train and evaluate on complete plans.
We study retrieval from incomplete queries on RPLAN with FusePlan, which fuses a graph branch and a vision branch over the same gallery of 67,405 plans.
The graph branch, a GATv2 encoder trained to map plans with rooms removed onto complete plans, recovers the source plan with an AUC of 0.903 on the test split (the reciprocal rank of the source averaged over three fractions of removed rooms; 1 means always first), against 0.611 for LayoutGKN retrained on the same task.
Because fusing any two models helps, we compare each fusion with ensemble controls that fuse the graph with a second graph model, and define the complementarity gain as the part of the gain the controls do not reach.
With this strong graph, a frozen image encoder adds +0.013 AUC and no complementarity gain, while vision heads trained on the damage pass both controls by +0.012 to +0.022 over four replicas.
The fusion gain peaks when the two branches are equally strong, and the image helps most where the graph keeps few rooms.
We make every choice on the validation split and read the test split with pre-registered predictions.

<p align="center">
  <img src="assets/f_final_pipeline_readme.png" alt="FusePlan pipeline: vision branch and graph branch fused late into one FAISS search" width="820">
  <br><sub>Figure 2 of the paper: the FusePlan pipeline.</sub>
</p>

## Paper at a glance

| Section | What it covers |
|---|---|
| 1 Introduction | Retrieval from partial plans; why a fusion gain alone cannot show that the image adds information; contributions. |
| 2 Related Work | Floor-plan similarity (LayoutGMN, LayoutGKN, SSIG, Unit Region Encoding), incomplete queries (sketch retrieval, Graph2Plan, PlanCraft), multimodal plan representations (CrossOver, MMFE). |
| 3 Method | Late fusion and **ensemble controls**, the **complementarity gain**, the GATv2 graph branch trained on damaged ↔ complete pairs, the frozen PE-Spatial vision branch with whitening and damage-trained heads. |
| 4 Experiments | Self-recovery AUC under room removal, crop and patch; paired bootstrap intervals and four replicas; main results, complementarity gain, relative strength, ablations, negative results, qualitative examples. |
| 5 Conclusions | When the image adds information of its own; recommendation to report fusion gains next to ensemble controls; limitations and future work. |
| Supplementary | Extra figures (controls, α sweep, replicas, damage kinds, per-class results, ablation); not included in the main build. |

## Key results

Room removal on the test split, AUC averaged over 25, 50 and 75 % of rooms removed; mean ± std over four replicas (Table 1 and Table 2 of the paper).

| System | AUC | Complementarity gain vs. ensemble control |
|---|---|---|
| Graph W (GATv2) | 0.904 ± 0.001 | — |
| W + frozen vision | 0.916 ± 0.001 | −0.0038 (below the control) |
| W + single-damage head | 0.932 ± 0.001 | +0.0116 |
| W + multi-damage head | 0.940 ± 0.001 | +0.0203 |

> [!IMPORTANT]
> A second graph model fused with W already gains +0.0163. With a graph near the ceiling, a frozen image encoder adds nothing beyond this ensemble effect; damage-trained heads add information of their own, but the control still explains about half of their gain.

<p align="center">
  <img src="assets/f_final_curve.png" alt="Fusion gain against the strength of the graph alone: an inverted U peaking when the branches are equally strong" width="460">
  <br><sub>Figure 5 of the paper: the fusion gain peaks when the two branches are equally strong.</sub>
</p>

## Repository contents

```
├── main.tex, preamble.tex     # paper entry point and packages (CVPR 2026 template)
├── sec/                       # abstract, introduction, related work, method, experiments, conclusions, supplementary
├── figures/english/           # figures included by the paper, produced by the project's scripts
├── assets/                    # PNG renderings of two figures for this README
├── main.bib                   # references
├── cvpr.sty, ieeenat_fullname.bst
├── main.pdf                   # compiled paper
├── FusePlan – Presentation.pdf
└── FusePlan – Presentation script.pdf   # speaker notes for the talk
```

## Building the paper

Requires a LaTeX distribution with `pdflatex` and `bibtex` (TeX Live or MiKTeX).

```bash
pdflatex main
bibtex main
pdflatex main
pdflatex main
```

> [!TIP]
> The supplementary material is in `sec/X_suppl.tex`; uncomment `\input{sec/X_suppl}` in `main.tex` to include it.

To reproduce the experiments, see the [project repository](https://github.com/290990-collab/FusePlan).

## Citation

```bibtex
@misc{angelillis2026fuseplan,
  title  = {FusePlan: Separating Complementarity from Ensemble Effects in Partial Floor Plan Retrieval},
  author = {Angelillis, Giuseppe and Di Maria, Enrico and Germini, Gabriele},
  year   = {2026},
  note   = {University of Modena and Reggio Emilia}
}
```
