
<div align="center">

<img src="assets/final-logo-2.png" alt="EAFT Logo" width="800"/>

# 🎯 Entropy-Adaptive Fine-Tuning :<br/> Resolving Confident Conflicts to Mitigate Forgetting


[![arXiv](https://img.shields.io/badge/Arxiv-2601.02151-b31b1b.svg?logo=arXiv)](https://arxiv.org/abs/2601.02151)
[![Project Page EAFT](https://img.shields.io/badge/Project--Page-EAFT-blue?style=flat)](https://ymxyll.github.io/EAFT/)
[![HF Daily Paper #1](https://img.shields.io/badge/HF-Daily--Paper1-ffcc00?logo=huggingface)](https://huggingface.co/papers/2601.02151)


If you like our project, please give us a star ⭐ on GitHub for the latest update.

</div>

## 🗂️ Table of Contents

- [📣 Latest News](#latest-news)
- [📖 Abstract](#abstract)
- [⚡ Method: EAFT](#method-eaft)
- [📊 Results](#results)
- [🚀 Quick Start](#quick-start)
- [📝 Citation](#citation)
<!-- - [🌟 Star History](#star-history) -->

---


## 📣 Latest News <a id="latest-news"></a>
* **[Jan 08,2026]** 🔥 We are honored to be featured as [**🤗 HuggingFace Daily Paper #1**](https://huggingface.co/papers/date/2026-01-08).
* **[Jan 07,2026]** 📄 Our paper is now available on [arXiv](https://arxiv.org/abs/2601.02151) and [Hugging Face](https://huggingface.co/papers/2601.02151) daily paper.
* **[Jan 06,2026]** ✨ **Integration:** EAFT has been merged into [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)! You can now use EAFT via `use_eaft_loss` parameter in it.
* **[Jan 06,2026]** 🚀 **Code Release:** Training code and scripts are now available! Please ⭐ Star this repo to stay tuned!

---

## 📖 Abstract <a id="abstract"></a>

Supervised Fine-Tuning (SFT) is the standard paradigm for domain adaptation, yet it frequently incurs the cost of **🎯 catastrophic forgetting**. In sharp contrast, on-policy Reinforcement Learning (RL) effectively preserves general capabilities.

We investigate this discrepancy and identify a fundamental **distributional gap**: while RL aligns with the model's internal belief, SFT forces the model to fit external supervision. This mismatch often manifests as **"💥 Confident Conflicts"**—tokens characterized by low probability but low entropy. In these instances, the model is highly confident in its own prediction but is forced to learn a divergent ground truth, triggering destructive gradient updates.

To address this, we propose **🚀 Entropy-Adaptive Fine-Tuning (EAFT)**. Unlike methods relying solely on prediction probability, EAFT utilizes token-level entropy as a gating mechanism to distinguish between epistemic uncertainty and knowledge conflict. This allows the model to learn from uncertain samples while suppressing gradients on conflicting data.

**🔬 Key Findings:**
* Extensive experiments on **Qwen** and **GLM** series (4B-32B parameters) across mathematical, medical, and agentic domains
* EAFT consistently matches the downstream performance of standard SFT 
* Significantly mitigates the degradation of general capabilities
* *"The right balance between learning and forgetting"*

<div align="center">
  <img src="assets/figure1.png" alt="Concept Figure" width="800"/>
  <br>
  <em><b>Figure 1:</b> (a) Conceptual illustration of Confident Conflicts. (b) Token-level entropy-probability landscape comparison between SFT and On-Policy Rollouts.</em>
</div>

---

## ⚡ Method: EAFT <a id="method"></a>

### 🔍 **What are "Confident Conflicts"?**

We identify **"Confident Conflicts"** (Low Probability, Low Entropy) as the primary driver of catastrophic forgetting. These occur when:
* The model is **confident** about its prediction (low entropy)
* But the prediction **conflicts** with ground truth (low probability)

### 🛠️ **Our Solution: Entropy-Adaptive Fine-Tuning**

EAFT introduces an entropy-based gating mechanism to the standard Cross-Entropy loss:


$$\mathcal{L}_{EAFT} (\theta) = - \sum_{t=1}^{T} \tilde{H}_t \cdot \log P_\theta(y_t | x, y_{<t})$$


Where $\tilde{H}_t$ is the **normalized entropy**. This mechanism:

1. **⚠️ Suppresses Conflicts:** Down-weights gradients when the model is stubborn (Low Entropy), preventing destructive updates
2. **✨ Encourages Learning:** Maintains high weights when the model is uncertain/exploring (High Entropy)

### 📈 **Intuitive Understanding**

Unlike methods that rely solely on prediction probability, EAFT utilizes **token-level entropy** to distinguish between:
- **Epistemic uncertainty**: Model doesn't know <font color="green">→ Strong learning signal</font>
- **Knowledge conflict**: Model is confident but wrong <font color="red">→ Suppressed updates</font>

<div align="center">
  <img src="assets/figure3.png" alt="Gradient Landscape" width="800"/>
  <br>
  <em><b>Visualization:</b> EAFT effectively suppresses destructive gradients in the Confident Conflict region.</em>
</div>

---

## 📊 Results <a id="results"></a>

### 🧮 **Main Results on Math Domain**

| Method | AIME24 | AIME25 | GSM8K | **Math Avg.** | MMLU | IFEval | CLUEWSC | **General Avg.** |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Qwen3-4B-Instruct** | 63.3 | 47.4 | 94.3 | 68.3 | 77.1 | 81.0 | 85.2 | 81.1 |
| + SFT | <u>63.3</u> | <u>50.0</u> | **94.8** | **69.4** | <u>76.5</u> | <u>79.5</u> | 74.5 | 76.5 $\color{red}{(-4.6)}$ |
| + SFT-KL | <u>63.3</u> | <u>50.0</u> | 93.6 | 69.0 | 74.5 | 74.9 | **89.4** | <u>79.6</u> $\color{red}{(-1.5)}$ |
| + FLOW | **66.7** | 46.7 | 94.3 | 69.2 | 76.2 | 78.3 | 82.8 | 79.1 $\color{red}{(-2.0)}$ |
| + DFT | 56.7 | 40.0 | 93.9 | 63.5 | 75.9 | 77.0 | 81.4 | 78.1 $\color{red}{(-3.0)}$ |
| + TALR | 50.0 | <u>50.0</u> | 93.3 | 64.4 | 76.2 | 78.1 | 74.5 | 76.2 $\color{red}{(-4.9)}$ |
| **+ EAFT (Ours)** | 60.0 | **53.3** | <u>94.5</u> | <u>69.3</u> | **76.6** | **80.1** | <u>83.7</u> | **80.1** $\color{green}{(-1.0) \textbf{✓}}$ |

### 🏥 **Universality: Medical & Agent Domains**

**🩺 Medical Domain (Base: Qwen3-4B-Thinking)**

| Method | MedMCQA | MedQA | PubMedQA | **Medical Avg.** | MMLU | IFEval | CLUEWSC | **General Avg.** |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Qwen3-4B-Think | <u>63.5</u> | 78.2 | 76.0 | 72.6 | <u>79.3</u> | **85.0** | **94.1** | **86.1** |
| + SFT | 63.3 | <u>79.5</u> | **78.0** | <u>73.6</u> | 78.3 | 75.3 | 90.4 | 81.3 $\color{red}{(-4.8)}$ |
| **+ EAFT (Ours)** | **63.9** | **80.0** | <u>77.2</u> | **73.7** | **80.1** | <u>81.7</u> | <u>91.8</u> | <u>84.5</u> $\color{green}{(-1.6) \textbf{✓}}$ |

**🤖 Agent Domain (Base: Qwen3-4B-Instruct)**

| Method | BFCL v3 (Target) | MMLU | IFEval | CLUEWSC | **General Avg.** |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Qwen3-4B-Inst | 60.5 | **77.1** | **81.0** | **85.2** | **81.1** |
| + SFT | **61.4** | 74.5 | 77.8 | 72.2 | 74.8 $\color{red}{(-6.3)}$ |
| **+ EAFT (Ours)** | <u>60.8</u> | <u>76.1</u> | <u>78.6</u> | <u>77.7</u> | <u>77.5</u> $\color{green}{(-3.6) \textbf{✓}}$ |

---

## 🚀 Quick Start <a id="quick-start"></a>
- **Step 1:** Clone the repository
```bash
git clone https://github.com/ymxyll/LlamaFactory-EAFT.git
cd LlamaFactory-EAFT
```
- **Step 2:** Install dependencies
```bash
pip install -e .
```
- **Step 3:** Run the training script
```bash
llamafactory-cli train --config examples/extras/eaft/qwen25_05b_eaft_full.yaml
```

---

## 📝 Citation <a id="citation"></a>

If you find this work helpful for your research, please consider citing our paper:

```bibtex
@article{diao2026entropy,
  title={Entropy-Adaptive Fine-Tuning: Resolving Confident Conflicts to Mitigate Forgetting},
  author={Diao, Muxi and Yang, Lele and Gong, Wuxuan and Zhang, Yutong and Yan, Zhonghao and Han, Yufei and Liang, Kongming and Xu, Weiran and Ma, Zhanyu},
  journal={arXiv preprint arXiv:2601.02151},
  year={2026}
}
```
---


<!-- ## 🌟 Star History <a id="star-history"></a>

[![Star History Chart](https://api.star-history.com/svg?repos=PRIS-CV/EAFT&type=Date)](https://star-history.com/#PRIS-CV/EAFT&Date) -->










