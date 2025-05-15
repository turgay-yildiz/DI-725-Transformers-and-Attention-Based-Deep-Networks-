

<div align="center">

# DI-725: Transformers and Attention-Based Deep Networks

## Final Project: Phase 2

### PaliGemma Image-Captioning

**Turgay Yıldız**  
Graduate School of Informatics, Middle East Technical University (METU)

</div>



---
<br>

This project addresses an image‐captioning task developed for the **Transformers and Attention-Based Deep Networks** course. In the first phase, I sought to determine whether it was possible to enhance PaliGemma’s descriptive accuracy by precisely controlling the interaction between Siglip (the vision encoder) and Gemma (the language decoder). Specifically, I investigated whether manipulating the insertion points of hard prompt tokens and continuous visual tokens could compel Gemma to ground its captions more faithfully in the image content and thereby reduce hallucinations. This approach ultimately did not yield the desired improvements.

---

### Project Overview
<br>

We navigated five main stages in Phase 2:

1. Siglip + Projection + Gemma Prototype

2. Hard‑Prompt Fine‑Tuning of the off‑the‑shelf PaliGemma

3. Soft‑Prompt Adaptation with learnable continuous tokens

4. Hyperparameter Search, Ablation Study & Best Model design and training

5. Comparisons of all models through  W&B Reports

<br>

All experiments are version‑controlled on GitHub and tracked via Weights & Biases (W&B). 


---
  
### First Phase : 
<br>
In Phase 1, I asked:  
<br>
The Research Question: 

> Can we improve PaliGemma’s caption fidelity by precisely controlling where Siglip’s visual embeddings and hard-prompt tokens enter Gemma?

In phase 2, I built a simple prototype to answer that question:

> Siglip (vision model) → Projection → Gemma (language model)

Notebook: `2_Siglip_plus_Proj_plus_Gemma.ipynb`

---
  
### Second Phase : 

<br> 

In the second phase — now organized into 8 Jupyter notebooks — I aim to produce preliminary results under <br>
three main strategies. 

1. The first strategy involved reconstructing PaliGemma’s architecture at a subcomponent  <br>
level so that a custom module could be interposed between vision and language models. This was in order for  <br> 
answering the question in phase 1.  <br> 

In the notebook :

> Siglip_plus_Proj_plus_Gemma.ipynb,

I implemented a streamlined pipeline comprising Siglip, a simple linear projection, and Gemma. These experiments <br>
revealed that the true PaliGemma architecture is substantially more intricate: its projection layers are not purely <br>
linear, it has been pre‐trained on extremely large datasets, and its decoder expects continuous embeddings rather <br>
than discrete token representations. The computational demands of retraining even a frozen Gemma model <br>
— with Low‑Rank Adaptation (LoRA) applied — exceeded the capacity of a 16 GB GPU.  <br> 

Siglip (vision model) → Projection → Gemma (language model)

Notebook: `2_Siglip_plus_Proj_plus_Gemma.ipynb`

Results:

- PaliGemma’s projection is non-linear and trained on massive data.
- Full end-to-end training (even with LoRA and freezing) exceeded 16 GB GPU limits.
- Conclusion: re-implementing the full pipeline is infeasible on limited hardware.


---


2. Consequently, I refocused the investigation on prompt engineering. In the notebook adapted from Google’s official <br>
fine-tuning guide, originally at : <br> 
<br>

https://github.com/google/generative-ai-docs/blob/main/site/en/gemma/docs/paligemma/fine-tuning-paligemma.ipynb <br>
<br>

I first freezed all layers and then evaluated it on the dataset, in order to get the baseline. 

> 3_PaliGemma_Baseline.ipynb 

Then, I fine-tuned the off-the-shelf PaliGemma using conventional hard prompts (e.g., *"caption en"*). <br>

> 4_PaliGemma_(hard_tokens).ipynb 


Modifications were made to accommodate the Kaggle environment, and the results —evaluated on aerial imagery— were <br>
logged to Weights & Biases: <br>
<br>

https://wandb.ai/DI_725___Final_Project/projects <br>
<br>


As expected, satellite images proved more challenging than natural scenes, yielding modest gains. <br>
Building on these insights, the next notebook 

> 5_PaliGemma_(soft_tokens).ipynb 

introduces learnable soft prompts in place of fixed hard tokens. <br>
Ten continuous prompt vectors of dimension 2048 were initialized to match PaliGemma’s embedding size,<br>
 and the data preprocessing and iterator routines were extended accordingly. <br>


After creating a working model, I then conduct Hyperparameter Search & Ablation Study :


> 6_Hyper_parameter_search.ipynb

> 7_Ablation_study.ipynb

<br>

I varied key parameters —learning rate, decay schedule (cosine vs. linear), warmup percentage, batch size, <br>
sequence length, and total runtime— and logged METEOR as the primary optimization metric, alongside BLEU and ROUGE. <br> 

Following the sweeps, I ran an ablation study on the soft‑prompt model to isolate the impact of individual design choices.<br>
 I systematically varied prompt length, initialization strategy, and decay function (cosine vs. linear). <br> 
 After finding the best hyper-parameters and most important parts of the architecture, capitalizing on these findings, <br>
 I devised the Best Model. Then, I fine-tuned it <br>
 

 > 8_Best_Model.ipynb

---

In summary, end‑to‑end retraining of PaliGemma’s constituent models demands both sophisticated architectural support<br>
and substantial GPU resources, making it impractical in a limited‐resource setting. In contrast, lightweight <br>
fine‑tuning—specifically, replacing hard prompts with optimized soft prompts—offers an effective and computationally <br>
efficient path to enhancing metric scores like Bleu.

---

### Phase 2 Deliverables : 
<br>
<br>

#### 0. README.md

This file: Provides an overview, detailed notebook descriptions, and repository structure.
<br>
<br>


#### 1. Exploratory Data Analysis (EDA)

Notebook: 1_DI_725_Project_Phase_2__EDA.ipynb

Content: Dataset inspection, preprocessing steps, and basic visualizations of RISCM imagery and captions.
<br>
<br>


#### 2. Siglip + Projection + Gemma Prototype

Notebook: 2_Siglip_plus_Projection_plus_Gemma.ipynb

Approach: Implemented a minimal pipeline: Siglip → Linear Projection → Gemma

Findings: Linear projection and LoRA adaptation failed to replicate emergent behaviors; GPU memory limits exceeded.
<br>
<br>



#### 3. PaliGemma Baseline Evaluation

Notebook: 3_PaliGemma_Baseline.ipynb

Approach: Inference with original hard-token PaliGemma on RISCM without fine‑tuning.

Metrics: ROUGE‑L/1/2, METEOR, BLEU logged to W&B as baseline scores.
<br>
<br>



#### 4. Hard‑Prompt Fine‑Tuning

Notebook: 4_PaliGemma_(hard_tokens).ipynb

Approach: Fine‑tuned PaliGemma by training only vision attention layers, using fixed hard tokens.

W&B Project: Hard‑Prompt Sweeps & Results

Outcome: Significant gains in ROUGE and METEOR; BLEU remained near zero.
<br>
<br>



#### 5. Soft‑Prompt Adaptation

Notebook: 5_PaliGemma_(soft_tokens).ipynb

Approach: Introduced 10 learnable soft tokens (2048‑dim) replacing hard prompts.

W&B Project: Soft‑Prompt Sweeps & Results

Outcome: BLEU improved marginally; ROUGE‑2 and METEOR saw slight upticks but did not exceed fine‑tuned hard tokens.
<br>
<br>



#### 6. Hyperparameter Search

Notebook: 6_Hyper_parameter_search.ipynb

Focus: Sweep over learning rate, decay functions, warmup, runtime, batch size, sequence length.

Key Insight: Runtime and learning rate were most critical; unstable above 1e‑4; optimal in [1e‑5, 3e‑5].
<br>
<br>



#### 7. Ablation Study

Notebook: 7_Ablation_study.ipynb

Focus: Isolate the impact of soft‑prompt length, initialization, and decay schedule.
<br>
<br>



#### 8. Best Model Training

Notebook: 8_Best_Model.ipynb

Approach: Combined architectural and hyperparameter insights to define a model. 

Outcome: Achieved balanced improvements across ROUGE, METEOR, and BLEU under constrained compute.
<br>
<br>

---


### Next Steps (Phase 3)


Explore hybrid token strategies: 


1. Fixed hard + Learnable soft tokens, 
 

2. Hard‑token initialization of soft prompts




---


GitHub: https://github.com/turgay-yildiz/DI-725-Transformers-and-Attention-Based-Deep-Networks-/tree/main/Final_Project/Phase_2

Weights & Biases: https://wandb.ai/DI_725___Final_Project/projects


---
