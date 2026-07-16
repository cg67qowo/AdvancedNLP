---
theme: gaia
_class: lead
paginate: true
title: "Course 10: Reasoning"
backgroundColor: #fff
marp: true
---


# **Reasoning Language Models**

---


<!--footer: "Course 8: Domain-Specific NLP" -->
## Introduction



---


## Contents

1. **What is reasoning?**
    a. Reasoning and Decomposition
2. **Chain of Thought**
    a. Chain of Thought prompting
    b. Train of Thoughts, ...
3. **Training to reason**
    a. RLHF
    b. DeepSeek-R1?
4. **Test-time compute?**
5. **Conclusion and critical questions**

---



<!--_class: lead -->
## What is reasoning?

---


<!--footer: "What is reasoning?" -->
### _Reasoning and Decomposition_

_Program Induction by Rationale Generation: Learning to Solve and Explain Algebraic Word Problems._ [1]

<center><img height="400px" src="AdvancedNLP/imgs/course10/math_problem.jpg"/></center>

---


### _Reasoning and Decomposition_

<style scoped>section{font-size:30px;}</style>
<center><img width="1100" src="AdvancedNLP/imgs/course10/math_problem_decomposed.jpg"/></center>

**Figure 1**: Example of the reasoning problems in _Program Induction by Rationale Generation: Learning to Solve and Explain Algebraic Word Problems._ [1]. This paper, published at ACL in 2017, is the first one to use natural language to describe intermediate reasoning steps.
---


### _Reasoning and Decomposition

_Thinking, Fast and Slow. [2]_

1. **System 1 reasoning**: fast, automatic and unconscious; e.g. recognizing faces, taking quick decisions 
2. **System 2 reasoning**: slow, deliberate, analytical; e.g. solving maths problems

System 1 is prone to **cognitive biases**, System 2 is **rational**. How do machines pass **from System 1 to System 2?**

---


### _Reasoning and Decomposition_

<style scoped>section{font-size:30px;}</style>
<center><img width="800" src="https://figures.semanticscholar.org/e816f788767eec6a8ef0ea9eddd0e902435d4271/6-Table5-1.png"/></center>

**Table 5**: Results on different phases of adaptive pretraining compared to the baseline RoBERTa (col. 1). Our approaches are $DAPT$ (col. 2, §3), $TAPT$ (col. 3, §4), and a combination of both (col. 4).

---


### Specialized models (BioBERT, SciBERT, Galactica)

"[..] the word distributions of general and biomedical corpora are quite different, which can often be a problem for biomedical text mining models." [2]

---


### Specialized models (BioBERT, SciBERT, Galactica)

<center><img width="900" src="https://figures.semanticscholar.org/1e43c7084bdcb6b3102afaf301cce10faead2702/3-Table1-1.png"/></center>

---


### Specialized models (BioBERT, SciBERT, Galactica)

"We showed that **pre-training BERT on biomedical corpora is crucial in applying it to the biomedical domain**. Requiring minimal task-specific architectural modification, **BioBERT outperforms previous models on biomedical text mining tasks** such as NER, RE and QA."

---


### Specialized models (BioBERT, SciBERT, Galactica)

<center><img width="900" src="https://figures.semanticscholar.org/156d217b0a911af97fa1b5a71dc909ccef7a8028/4-Table2-1.png"/></center>

**Table 2**: Comparing SciBERT with the reported BioBERT results on biomedical datasets.

---


### Specialized models (BioBERT, SciBERT, Galactica)

**NB**: SciBERT was trained on curated textual data ; not trained on code or script for example---at leat not trained directly and purposefully on this kind of data

---


### Specialized models (BioBERT, SciBERT, Galactica)

"Unlike search engines, language models can potentially store, combine and reason about scientific knowledge." [4]

* Specialized models (BioBERT, SciBERT, Galactica) were trained on a rather small highly curated dataset.
* The data was standardized in markdown format.

---


### Specialized models (BioBERT, SciBERT, Galactica)

<style scoped>section{font-size:30px;}</style>
<center><img width="750" src="https://d3i71xaburhd42.cloudfront.net/7d645a3fd276918374fd9483fd675c28e46506d1/4-Table1-1.png"/></center>

**Table 1**: Tokenizing Nature. Galactica trains on text sequences that represent scientific phenomena.

---


### Specialized models (BioBERT, SciBERT, Galactica)


1. **Citations:** wrapped with special reference tokens [START_REF] and [END_REF].
2. **Step-by-Step Reasoning:** wrapped with a working memory token `<work>`, mimicking an internal working memory context.
3. **Mathematics:** for mathematical content, with or without LaTeX, ASCII operations are splitted into individual characters. Parentheses are treated like digits. The rest of the operations allow for unsplit repetitions. Operation characters are !"#$%&’*+,-./:;<=>?\^_‘| and parentheses are ()[]{}.

---


4. **Numbers:** splitted into individual tokens. For example 737612.62 -> 7,3,7,6,1,2,.,6,2.
5. **SMILES formula:** wrapped with [START_SMILES] and [END_SMILES] and tokenized absed on characters. Similarly [START_I_SMILES] and [END_I_SMILES] is usedwhere isomeric SMILES is denoted.
6. **Amino acid sequences:** wrapped with [START_AMINO] and [END_AMINO] and apply character-based tokenization, treating each amino acid character as a single token. For example, MIRLGAPQTL -> M,I,R,L,G,A,P,Q,T,L.

---


1. **DNA sequences:** tokenized based on characters and wrapped inside [START_DNA] and [END_DNA]. For example, CGGTACCCTC -> C, G, G, T, A, C, C, C, T, C.

---

### Specialized models (BioBERT, SciBERT, Galactica)

<style scoped>section{font-size:30px;}</style>
<center><img width="750" src="https://figures.semanticscholar.org/7d645a3fd276918374fd9483fd675c28e46506d1/9-Figure5-1.png"/></center>

**Figure 5:** Prompt Pre-training. Pre-training weighs all tokens equally as part of the self-supervised loss. This leads to a weak relative signal for tasks of interest, meaning model scale has to be large to work. Instruction tuning boosts performance post hoc, and can generalize to unseen tasks of interest, but it risks performance in tasks that are distant from instruction set tasks. Prompt pre-training has a weaker task of interest bias than instruction tuning but less risk of degrading overall task generality.

---


### Specialized models (BioBERT, SciBERT, Galactica)

* **GeLU Activation** - GeLU activations for all model sizes.
* **Context Window** - a 2048 length context window.
* **No Biases** - following PaLM, no biase in any of the dense kernels or layer norms.
* **Learned Positional Embeddings** - learned positional embeddings for the model.
* **Vocabulary** - vocabulary of 50k tokens using BPE. The vocabulary was generated from a randomly selected 2% subset of the training data.

---


### Specialized models (BioBERT, SciBERT, Galactica)

_Gaussian Error Linear Units function (GeLu)_

$$GELU(x)= x ∗ \Phi(x)$$

Where $\Phi(x)$ is the Gaussian function.

$$GELU(x) \approx x ∗ \frac{1}{2}(1 + Tanh(\frac{2}{\pi}∗(x+0.044715 ∗ x^{3})))$$

---


### Specialized models (BioBERT, SciBERT, Galactica)

<center><img height="350" src="https://pytorch.org/docs/stable/_images/GELU.png"/></center>

* Allows small negative values when $x < 0$.
* Avoids the dying ReLU problem.

---


### Specialized models (BioBERT, SciBERT, Galactica)

_Why no biases?_

<center><img height="450" src="https://upload.wikimedia.org/wikipedia/commons/9/91/Full_GPT_architecture.png"/></center>

---


<!--footer: "Course 8: Domain-Specific NLP" -->
<!--_class: lead -->
## Unsupervised Classification Models

---


<!--footer: "Unsupervised Classification Models" -->
### Représentations out-of-the-box: limitations

Embedding **pooling** is the process of **combining token embeddings** from an encoder model **into a single vector representing the entire input sequence**. Common methods include averaging (**mean pooling**), taking the maximum (**max pooling**), or using a **special token** like `[CLS]` or `<s>`.

---


### Représentations out-of-the-box: limitations

<center><img height="500" src="https://miro.medium.com/v2/resize:fit:526/format:webp/1*I8uc9PO0ai_o4LJX7tnLbQ.jpeg"/></center>

---


### Représentations out-of-the-box: limitations

<center><img height="500" src="https://miro.medium.com/v2/resize:fit:542/format:webp/1*T3A4AHF41MieOkZWOyK19g.jpeg"/></center>

---


### Représentations out-of-the-box: limitations

<center><img height="500" src="https://miro.medium.com/v2/resize:fit:526/format:webp/1*TS_8YjY4LY5epCvJRHJ6pg.jpeg"/></center>

---


### Représentations out-of-the-box: limitations

<center><img height="400" src="https://d3i71xaburhd42.cloudfront.net/93d63ec754f29fa22572615320afe0521f7ec66d/3-Figure1-1.png"/></center>

**NB**: when your output pooled vectors are scaled, the cosine similarity is equal to the dot product.

---


### Représentations out-of-the-box: limitations

Late interaction:
<center><img width="700" src="https://figures.semanticscholar.org/590432f953b6ce1b4b36bf66a2ac65eeee567515/3-Figure1-1.png"/></center>

---


### Représentations out-of-the-box: limitations

The data is being compressed mutliple time -> challeging document can be hard to embed.

Can we do better?

---


### SimCSE, E5, GTE...

**Contrastive learning** uses **similar data point**  and **opposite ones** in order for the model build **close representations for the first ones** and and **more separated ones for the latter**. [7]

* Unsupervised SimCSE: standard dropout as data augmentation
* Supervised SimCSE: use pairs in NLI datasets

---


### SimCSE, E5, GTE...

$$\mathcal{L}_{uns} = -log \frac{ exp( \frac{ sim( \textbf{ h }_{ i }, \textbf{ h }_{ i }^{ + } )}{ \tau } ) } {\sum_{j=1}^{N}exp( \frac{ sim( \textbf{ h }_{ i }, \textbf{ h }_{ j }^{ + } )}{ \tau } ) } $$

$$\mathcal{L}_{sup} = -log \frac{ exp( \frac{ sim( \textbf{ h }_{ i }, \textbf{ h }_{ i }^{ + } )}{ \tau } ) } {\sum_{j=1}^{N}exp( \frac{ sim( \textbf{ h }_{ i }, \textbf{ h }_{ j }^{ + } )}{ \tau } ) + exp( \frac{ sim( \textbf{ h }_{ i }, \textbf{ h }_{ j }^{ - } )}{ \tau } ) } $$

[8]

---


### SimCSE, E5, GTE...

<center><img width="1100" src="https://d3i71xaburhd42.cloudfront.net/c26759e6c701201af2f62f7ee4eb68742b5bf085/2-Figure1-1.png"/></center>

---


### SimCSE, E5, GTE...

**Contrastive learning mitigates anisotropy** in language models by encouraging **embeddings** to be **more uniformly distributed** in the representation space. It pulls similar embeddings closer and pushes dissimilar ones apart, preventing over-clustering and ensuring better geometric properties for downstream tasks.

---


<!--footer: "Course 8: Domain-Specific NLP" -->
<!--_class: lead -->
## Learning Long-Range Dependencies

---


<!--footer: "Learning Long-Range Dependencies" -->
### Long-range attention models

Sliding window attention: Longformer [11]

<center><img width="1100" src="https://d3i71xaburhd42.cloudfront.net/925ad2897d1b5decbea320d07e99afa9110e09b2/3-Figure2-1.png"/></center>

---


### Long-range attention models

Sliding window attention: Mistral 7B [12]

<center><img width="900" src="https://d3i71xaburhd42.cloudfront.net/db633c6b1c286c0386f0078d8a2e6224e03a6227/2-Figure1-1.png"/></center>

---


### State-space models: Mamba

<center><img width="900" src="https://figures.semanticscholar.org/7bbc7595196a0606a07506c4fb1473e5e87f6082/3-Figure1-1.png"/></center>



---


<!--footer: "Course 8: Domain-Specific NLP" -->
<!--_class: lead -->
## Questions?

---


### References

[1] Ling, Wang, Yogatama, Dani, Dyer, Chris, and Blunsom, Phil. “[Program Induction by Rationale Generation : Learning to Solve and Explain Algebraic Word Problems.](https://doi.org/10.48550/arXiv.1705.04146.)” In Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics, edited by Dan Jurafsky, Joyce Chai, Natalie Schluter, and Joel Tetreault, 158-167. Online: Association for Computational Linguistics, 2017.

---


[2] Kahneman, Daniel. Thinking, Fast and Slow. Farrar, Straus and Giroux, 2011.

