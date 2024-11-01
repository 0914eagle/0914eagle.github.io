---
layout: post
title: "Rethinking the Role of Demonstrations: What Makes In-Context Learning Work?"
date: 2024-11-01 10:00:00 +0900
categories: blog
---

<div>
<h2 class = "title"><strong>Contents</strong></h2>
</div>

- [**Prerequisite**](#prerequisite)
- [**Introduction**](#introduction)
- [**Experimental Setup**](#experimental-setup)
- [**Ground truth Matters Little**](#ground-truth)
- [**Why does In-Context Learning work?**](#ICL-work)
- [**Conclusion**](#conclusion)

<h2 class = "title" id="prerequisite"><strong>Prerequisite</strong></h2>
<h3><strong>In-context learning</strong></h3>

<div>
<p>
In-context learning(ICL) means that the model understands the contextual meaning within a prompt and generates responses based on it. In other words, ICL does not involve updating the model's weights, like in pretraining or fine-tuning, and it does not require a separate model training process. ICL's approach mirrors the human cognitive reasoning process, making it a more intuitive model for problem-solving.
</p>

<div style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/ICL.png" alt="In-Context Learning Diagram" title="Diagram of In-Context Learning" width="500">
</div>

<p>
ICL can be divided into <strong>zero-shot</strong>, <strong>one-shot</strong>, and <strong>few-shot</strong> depending on the given context and examples.
</p>

</div>

<h4><strong>Zero-shot</strong></h4>

<p>
Zero-shot refers to the ability to perform a task without any given examples.
</p>

<div class="highlight-box">
  <strong>Prompt:</strong> What is the 'red apple' in French?<br>
  <strong>GPT:</strong> "pomme rouge"
</div>

<h4><strong>One-shot</strong></h4>

<p>
One-shot refers to performing a task with the help of a single example.
</p>

<div class="highlight-box">
  <strong>Prompt:</strong> Let's say 'red apple' as 'rouge apple'. <br> Then what is the green watermelon. <br>
  <strong>GPT:</strong> "vert watermelon"
</div>

<h4><strong>Few-shot</strong></h4>

<p>
Few-shot learning refers to performing a task with the help of multiple examples.
</p>

<div class="highlight-box">
  <strong>Prompt:</strong> Let's say 'red apple' as 'rouge apple'. And 'green watermelon' as 'vert watermelon'. <br> Then what is the 'green apple'?<br>
  <strong>GPT:</strong> "vert Apple"
</div>

<h2 class = "title" id="introduction"><strong>Introduction</strong></h2>

<p> In-context learning is a useful method to improve a model's performance without additional training. However, despite this performance improvement, there is little understanding of how it works and which aspects of the demonstrations contribute to end task performance. </p>

<p> This paper investigates how the model learns and which elements of the demonstration impact the performance of downstream tasks. The goal and conclusion of this research are as follows: </p>

- <strong>Evaluate the importance of ground-truth labels in demonstrations:</strong> replacing the labels in demonstrations with random labels barely hurts performance in a range of classification and multi-choice tasks.
<div style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/intro.png" alt="Random label" width="500">
</div>

- <strong>Identify key factors in demonstration contributing to ICL:</strong> Label space, the distribution of the input text and format.

<strong>The example of random labels</strong>

<div class="highlight-box">
  <strong>Input:</strong> This device is the best! <br>
  <strong>Label:</strong> "Negative"
</div>

<h2 class = "title" id="experimental-setup"><strong>Experimental Setup</strong></h2>

<p>In this study, <strong>6 language models</strong> with two inference methods, direct and channel, were used. Direct method means that the model's input is in the usual order and predicts the output corresponding to the input. But channel method means that the model is given the output first and then predicts the input.</p>

<div style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/models.png" alt="Random label" width="300">
</div>

<p>The evaluation was conducted on <strong>26 datasets</strong>, that cover various tasks and domains. (e.g. classification and multichoice) </p>

<h2 class = "title" id="ground-truth"><strong>Ground Truth Matters Little</strong></h2>

<p> To figure out the importance of correctly-paired inputs and lables in the demonstrations, the researcher compared the three methods</p>

- <strong>No demonstrations</strong>: typical zero-shot method, i.e.,
  <span>$argmax\_{y \in C} P(y|x)$</span>
- <strong>Demonstrations w/ gold labels</strong>: typical ICL method (correct input-label mapping), i.e.,
  <span>$argmax\_{y \in C} P(y|x_1, y_1, ..., x_k, y_k, x)$</span>
- <strong>Demonstrations w/ random labels</strong>: alter gold labels from the labeled data to random labels, i.e.,
  <span>$argmax\_{y \in C} P(y|x_1, \tilde{y_1}, ..., x_k,\tilde{y_k}, x)$</span>

<div style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/gold_random.png" alt="Random vs golden label" width="700">
</div>

<p>Results are reported above figure. Without demonstrations, the model's performance significantly decrease. However, the trend is that replacing gold labels with random labels only marginally hurts performance. This indicates that models are capable of recovering the expected input-label correspondence for the task; but, it is not directly from the pairings in the demonstrations.</p>

<h2 class = "title" id="ICL-work"><strong>Why does In-Context Learning work?</strong></h2>

<p>Above shows that golden input-label mapping is not a crucial role in demonstrations. Then which aspects of the demonstrations improve the model performance?</p>

<p>The researcher define three aditional asoects of the demonstrations that potentially provide learning signal</p>

- <strong> The distribution of the input text </strong>: the underlying distribution of the input text ($x_1 ... x_k$) in the demonstrations
- <strong> The label space </strong>: the space covered by $y_1 .. y_k$
- <strong> The format </strong>: the use of input-label pairing as the format

<div style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/add_aspect.png" alt="Random vs golden label" width="500">
</div>

<h4><strong>Impact of the distribution of the input text</strong></h4>

<p> The researcher compared the performance of the model with out-of-distribution (OOD) demonstrations while keeping the label space and the format. The results show that the model's performance is significantly affected by the distribution of the input text. </p>

<div style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/OOD.png" alt="OOD" width="700">
</div>

<p>This shows that in-distribution inputs impact the performance of model due to conditioning on the in-distribution text making the task closer.</p>

<h4><strong>Impact of the label space</strong></h4>

<p>In this experiment, researcher used random English words as labels for all k pairs. They construct a random subset of English words $|C_{rand}| = |C|$ and randomly pair $\tilde{y_i} \in C_{rand}$ with $x_i$</p>

<div style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/label.png" alt="Impact of the label space" width="700">
</div>

<p>Along with above results, the channel models does not show significant drop or sometimes even an increase. This is because the channel models only condition on the labels. However, the direct models exhibits the performance gap. This indicates that they learn the distribution of the label space for a given input.</p>

<h4><strong>Impact of the format</strong></h4>

<p>The "format" means the use of input-label pairing. Experiments were conducted by changing the format using methods such as demonstrations with no labels and demonstrations with labels only.</p>

<div style="text-align: center;">   
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/format.png" alt="Impact of the label space" width="700">
</div>

<p>As shown in the figure above, altering the format (indicated by the purple and green bars) led to performance that was nearly the same or even lower than the no-demonstration case. This suggests that format is essential in guiding the model to replicate the intended inference process.</p>

<h4><strong>Impact of the meta-training</strong></h4>

<p>MetaICL is minimally affected by random labels; however, changes in format—whether using only random labels or no labels at all—significantly impact performance. This suggests that meta-training emphasizes learning straightforward elements, like format, over correctly matched input-label examples from demonstrations, implying that format may be easier for the model to leverage. Additionally, altering the output space (e.g., using random English words) has little effect on Channel MetaICL compared to Direct MetaICL. This implies that utilizing the input text space the model needs to generate is simpler than utilizing the input text space the model relies on for conditioning.</p>

<strong>The example demonstrations used in identifying the aspect of the demonstration</strong>

<div style="text-align: center;">
  <img src="{{ site.baseurl }}/assets/img/content/Rethink/example.png" alt="Impact of the label space" width="700">
</div>

<h2 class = "title" id="conclusion"><strong>Conclusion</strong></h2>

- <strong>Format Matters More Than Labels:</strong> The study shows that correct input-label pairings are less critical than maintaining a consistent format in demonstrations. Random labels have minimal impact, but format changes significantly reduce performance, highlighting format as a primary cue for the model.
- <strong>MetaICL Leverages Simple Patterns:</strong> MetaICL focuses on simple elements like format rather than precise input-label matching, making format an easily exploitable structure for In-Context Learning.
- <strong>Exploiting Input Structure Over Correctness:</strong> The findings suggest that when optimizing In-Context Learning, designing demonstrations with clear structural hints may be more beneficial than ensuring perfect labels.
