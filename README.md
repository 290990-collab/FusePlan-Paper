<div align="center">

# FusePlan — Paper

**Separating Complementarity from Ensemble Effects in Partial Floor Plan Retrieval**

Giuseppe Angelillis · Enrico Di Maria · Gabriele Germini · University of Modena and Reggio Emilia

[![Paper](https://img.shields.io/badge/📄_Paper-PDF-1f4e9c?style=for-the-badge)](main.pdf)
[![Slides](https://img.shields.io/badge/Slides-PDF-1f4e9c?style=for-the-badge)](FusePlan%20–%20Presentation.pdf)
[![Code](https://img.shields.io/badge/Code-FusePlan-24292f?style=for-the-badge&logo=github)](https://github.com/290990-collab/FusePlan)

*This repository is the **paper** (CVPR 2026 format, LaTeX + PDF) and the talk.
Code, configurations and experiments live in the project repository: [290990-collab/FusePlan](https://github.com/290990-collab/FusePlan).*

</div>

## TL;DR

**Task.** Given a floor plan with 25–75 % of its rooms removed, retrieve the original among all 67,405 RPLAN plans.

**Question.** Fusing a room-graph model with an image model improves retrieval, but fusing *any* two models does. Does the image add information, or does it only act as a second model?

**Answer.**

- 🏗️ A GATv2 graph trained on damaged ↔ complete plans reaches an AUC of **0.904**, against **0.611** for a retrained LayoutGKN.
- ⚖️ Fused with this graph, a **frozen image encoder adds nothing** beyond what a second graph model gives; **damage-trained vision heads do add information**, but about **half of their gain is still an ensemble effect**.
- 📈 The fusion gain peaks when the two branches are **equally strong**, and the image helps most where the graph has **few rooms left**.

> [!TIP]
> **Takeaway for multimodal fusion:** report every fusion gain next to an *ensemble control* (the same branch fused with a second model of the same modality). Without it, the contribution of the second modality is overstated by about 2×.

## Method

<p align="center">
  <img src="assets/f_final_pipeline_readme.png" alt="FusePlan pipeline: a frozen image encoder and a GATv2 room-graph encoder produce two unit vectors, concatenated with weight alpha and searched with FAISS" width="840">
</p>

Two branches embed the same plan and search the same gallery. Late fusion concatenates their unit vectors as `[√α·v ; √(1−α)·g]`, so one FAISS inner-product search ranks by `α·cos_vision + (1−α)·cos_graph`.

The **complementarity gain** isolates what the image really adds:

$$\mathrm{CG} = \mathrm{gain}(\text{graph}+\text{vision}) - \mathrm{gain}(\text{graph}+\text{second graph})$$

## Results

<table>
<tr>
<td width="56%" valign="top">

**Test split, room removal** · AUC, mean of 4 replicas

| System | AUC | CG |
|---|:-:|:-:|
| LayoutGKN (retrained) | 0.611 | |
| **Graph W** (GATv2) | **0.904** | |
| W + frozen vision | 0.916 | ❌ −0.004 |
| W + single-damage head | 0.932 | ✅ +0.012 |
| W + multi-damage head | **0.940** | ✅ +0.020 |

<sub>A second graph model fused with W already gains +0.016. CG > 0: the image adds information of its own.</sub>

</td>
<td width="44%" valign="top">

<img src="assets/f_final_curve.png" alt="Fusion gain against the strength of the graph alone: an inverted U" width="100%">

<sub>Fusion gain vs. strength of the graph alone over 37 graphs: an inverted U that peaks when the branches are equally strong.</sub>

</td>
</tr>
</table>

Every choice is made on validation; the test split is read once, with predictions written in advance.

## Inside the paper

| | |
|---|---|
| **§1 Introduction** | Retrieval from partial plans; why a fusion gain alone proves nothing |
| **§2 Related Work** | LayoutGMN, LayoutGKN, SSIG, URE · sketch retrieval, Graph2Plan, PlanCraft · CrossOver, MMFE |
| **§3 Method** | Late fusion, ensemble controls, complementarity gain · GATv2 graph branch · PE-Spatial vision branch |
| **§4 Experiments** | Metric and protocol · main results · complementarity gain · relative strength · ablations · negative results |
| **§5 Conclusions** | When the image helps · limitations · future work |

<details>
<summary><b>Repository contents and build</b></summary>

```text
main.tex, preamble.tex, sec/      LaTeX source (CVPR 2026 template)
figures/english/                  figures included by the paper
main.bib, cvpr.sty, *.bst         references and style
main.pdf                          compiled paper
FusePlan – Presentation.pdf       slides
FusePlan – Presentation script.pdf  speaker notes
assets/                           PNG figures for this README
```

Build with TeX Live or MiKTeX:

```bash
pdflatex main && bibtex main && pdflatex main && pdflatex main
```

The supplementary material is in `sec/X_suppl.tex`: uncomment `\input{sec/X_suppl}` in `main.tex` to include it.

</details>

## Citation

```bibtex
@misc{angelillis2026fuseplan,
  title  = {FusePlan: Separating Complementarity from Ensemble Effects in Partial Floor Plan Retrieval},
  author = {Angelillis, Giuseppe and Di Maria, Enrico and Germini, Gabriele},
  year   = {2026},
  note   = {University of Modena and Reggio Emilia}
}
```
