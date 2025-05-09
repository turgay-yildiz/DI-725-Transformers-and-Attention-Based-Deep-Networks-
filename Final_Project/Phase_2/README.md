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
  
### First Phase : 
<br>
In Phase 1, I asked:  

> Can we improve PaliGemma by controlling where Siglip’s visual embeddings and hard-prompt text tokens enter Gemma?  

In phase 2, I built a simple prototype to answer that question:

> Siglip (vision model) → Projection → Gemma (language model)

Notebook: `Siglip_plus_Proj_plus_Gemma.ipynb`

---
  
### Second Phase : 
<br> 
In the second phase — now organized into four Jupyter notebooks — I aim to produce preliminary results under <br>
two main strategies. The first strategy involved reconstructing PaliGemma’s architecture at a subcomponent  <br>
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

Notebook: `Siglip_plus_Proj_plus_Gemma.ipynb`

Results:

- PaliGemma’s projection is non-linear and trained on massive data.
- Full end-to-end training (even with LoRA and freezing) exceeded 16 GB GPU limits.
- Conclusion: re-implementing the full pipeline is infeasible on limited hardware.


---


Consequently, I refocused the investigation on prompt engineering. In the notebook adapted from Google’s official <br>
fine-tuning guide, originally at : <br> 
<br>
https://github.com/google/generative-ai-docs/blob/main/site/en/gemma/docs/paligemma/fine-tuning-paligemma.ipynb <br>
<br>
I fine-tuned the off-the-shelf PaliGemma using conventional hard prompts (e.g., *"caption en"*). Modifications were made<br>
to accommodate the Kaggle environment, and the results—evaluated on aerial imagery—were logged to Weights & Biases: <br>
<br>
https://wandb.ai/DI_725___Final_Project/Phase_2___Original_PaliGemma_with_hard_prompts?nw=nwuserturgayyildizphiepi. <br>
<br>
As expected, satellite images proved more challenging than natural scenes, yielding modest gains. 
<br>
Building on these insights, the next notebook introduces learnable soft prompts in place of fixed hard tokens. <br>
Ten continuous prompt vectors of dimension 2048 were initialized to match PaliGemma’s embedding size, and the data <br>
preprocessing and iterator routines were extended accordingly. Fine‑tuning with these soft prompts produced <br>
the results documented at <br>
<br>
https://wandb.ai/DI_725___Final_Project/Phase_2___Original_PaliGemma_with_soft_prompts?nw=nwuserturgayyildizphiepi.
<br>
<br>
In summary, end‑to‑end retraining of PaliGemma’s constituent models demands both sophisticated architectural support<br>
and substantial GPU resources, making it impractical in a limited‐resource setting. In contrast, lightweight <br>
fine‑tuning—specifically, replacing hard prompts with optimized soft prompts—offers an effective and computationally <br>
efficient path to enhancing metric scores like Bleu.

---

### Files : 

Explatory Data analysis (EDA) : 

> DI_725_Project_Phase_2__EDA.ipynb

Subcomponents of Paligemma :

> Siglip_plus_Proj_plus_Gemma.ipynb

Hard-prompt fine-tuning of PaliGemma.

> PaliGemma_hard_tokens_7_May_17_50.ipynb 

Soft-prompt fine-tuning of PaliGemma :

> paligemma_soft_tokens_9_may_07_20.ipynb


