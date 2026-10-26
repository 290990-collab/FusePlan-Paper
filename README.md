<div align="center">

# FusePlan

**Separating Complementarity from Ensemble Effects in Partial Floor Plan Retrieval**

Giuseppe Angelillis, Enrico Di Maria, Gabriele Germini<br>
University of Modena and Reggio Emilia

[![Paper](https://img.shields.io/badge/Paper-PDF-1f4e9c?style=for-the-badge)](main.pdf)
[![Slides](https://img.shields.io/badge/Slides-PDF-1f4e9c?style=for-the-badge)](presentation.pdf)
[![Code](https://img.shields.io/badge/Code-FusePlan-24292f?style=for-the-badge&logo=github)](https://github.com/290990-collab/FusePlan)

</div>

This repository contains the paper we wrote on the FusePlan project (LaTeX source in CVPR 2026 format and compiled PDF), together with the slides and the notes for the talk. The code, the configurations and the experiments are in the project repository, [290990-collab/FusePlan](https://github.com/290990-collab/FusePlan).

## In short

We take a floor plan, remove between 25% and 75% of its rooms, and try to find the original plan among all 67,405 plans of RPLAN.

A plan can be read as an image or as a graph of rooms, and combining the two improves retrieval. The catch is that combining any two models tends to improve results, even when the second one knows nothing new. So the question we ask is whether the image actually adds information to the graph, or simply works as a second model.

What we found:

- A GATv2 graph encoder trained to match partial plans with their complete version reaches an AUC of 0.904, while LayoutGKN retrained on the same task stops at 0.611.
- With such a strong graph, a frozen image encoder adds nothing more than a second graph model would. Vision heads trained on the damage do add information of their own, but about half of their gain is still the plain effect of combining two models.
- The gain from fusion is largest when the two branches are about equally strong, and the image helps mostly when the graph has few rooms left.

> [!TIP]
> The general lesson is that a fusion gain should always be reported next to a control, here the same graph combined with a second graph model. Without it, the contribution of the second modality looks about twice as large as it is.

## Method

<p align="center">
  <img src="assets/f_final_pipeline_readme.png" alt="FusePlan pipeline: a frozen image encoder and a GATv2 room-graph encoder produce two unit vectors, which are concatenated and searched with FAISS" width="840">
</p>

The vision branch and the graph branch embed the same plan and search the same gallery. We concatenate their normalised vectors with weights $\sqrt{\alpha}$ and $\sqrt{1-\alpha}$, so a single FAISS inner-product search ranks the plans by $\alpha\,\cos_{\text{vision}} + (1-\alpha)\,\cos_{\text{graph}}$.

To isolate what the image really adds, we compare each fusion with the graph combined with a second graph model and call the difference the complementarity gain:

$$\mathrm{CG} = \mathrm{gain}(\text{graph}+\text{vision}) - \mathrm{gain}(\text{graph}+\text{second graph})$$

## Results

<table>
<tr>
<td width="56%" valign="top">

Test split, room removal, mean over four runs.

| System | AUC | CG |
|---|:-:|:-:|
| LayoutGKN (retrained) | 0.611 | |
| Graph W (GATv2) | **0.904** | |
| W + frozen vision | 0.916 | -0.004 |
| W + single-damage head | 0.932 | +0.012 |
| W + multi-damage head | **0.940** | +0.020 |

<sub>Combining W with a second graph model already gives +0.016. A positive CG means the image brings information the graph does not have.</sub>

</td>
<td width="44%" valign="top">

<img src="assets/f_final_curve.png" alt="Fusion gain against the strength of the graph alone, forming an inverted U" width="100%">

<sub>Fusion gain against the strength of the graph alone, over 37 graphs. It peaks when the two branches are equally strong.</sub>

</td>
</tr>
</table>

All choices were made on the validation split. We wrote down our predictions before looking at the test split, and read it only once.

## What is in the paper

| Section | Content |
|---|---|
| 1. Introduction | Retrieval from partial plans, and why a fusion gain alone does not prove much |
| 2. Related Work | Floor-plan similarity (LayoutGMN, LayoutGKN, SSIG, URE), incomplete queries (sketch retrieval, Graph2Plan, PlanCraft), multimodal plans (CrossOver, MMFE) |
| 3. Method | Late fusion, ensemble controls and complementarity gain; graph branch; vision branch |
| 4. Experiments | Metric and protocol, main results, complementarity gain, relative strength, ablations, negative results |
| 5. Conclusions | When the image helps, limitations, future work |

<details>
<summary><b>Repository contents and build</b></summary>

```text
main.tex, preamble.tex, sec/          LaTeX source (CVPR 2026 template)
figures/english/                      figures used in the paper
main.bib, cvpr.sty, *.bst             references and style files
main.pdf                              compiled paper
presentation.pdf                      slides
script.pdf                            notes for the talk
assets/                               images for this README
```

To compile with TeX Live or MiKTeX:

```bash
pdflatex main && bibtex main && pdflatex main && pdflatex main
```

The supplementary material is in `sec/X_suppl.tex`. To include it, uncomment `\input{sec/X_suppl}` in `main.tex`.

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
