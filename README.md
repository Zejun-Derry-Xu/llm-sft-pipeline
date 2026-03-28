# 🏴‍☠️ Pirate to Professional: LLM Persona Fusion via LoRA Fine-Tuning

<p align="center">
  <img src="https://img.shields.io/badge/Model-Llama--3.2--1B--Instruct-blue?style=for-the-badge&logo=meta" alt="LLM">
  <img src="https://img.shields.io/badge/Dataset-MultiWOZ-orange?style=for-the-badge" alt="Dataset">
  <img src="https://img.shields.io/badge/PEFT-LoRA-purple?style=for-the-badge" alt="PEFT LoRA">
</p>

> **🛑 Important Notice on Source Code Access**
> To fully comply with academic integrity policies, the complete source code and raw data for this project are hosted in a **private repository**. This repository serves as a **Portfolio Showcase**, detailing the experimental design, hyperparameter analysis, and behavioral evaluation. I am happy to discuss implementation details during the interview process.

## 🚀 The Mission: "Teaching a Pirate to Book a Train"

Fine-tuning an LLM to follow instructions is standard practice. But what happens when you force two completely conflicting personas to merge? 

**The Premise:** I took a base Llama-3.2-1B model and firmly prompted it to act as a **Pirate**. Then, using the MultiWOZ dataset, I applied Supervised Fine-Tuning (SFT) to train it as a **polite, task-oriented Customer Service Representative**. 

**The Goal:** The objective of this project was *not* just to build a chatbot, but to systematically analyze how different LoRA hyperparameters (Epochs and Rank) influence **Persona Fusion**. I wanted to find the exact configuration where the model could successfully complete complex customer service tasks *while still maintaining its required pirate flair*.

---

## 🔬 The Core Experiment: Hyperparameter Showdown

The heart of this project lies in `supervised_finetuning.ipynb`. I conducted three distinct training experiments to observe the behavioral shift of the model compared to the baseline.

### 🏴‍☠️ The Baseline: Pure Pirate (Base Model)
* **Observation:** The base model strictly adheres to the pirate persona but completely fails at the customer service task. It offers rum instead of booking a train.
<p align="center">
  <img width="870" height="82" alt="image" src="https://github.com/user-attachments/assets/48e69804-66d0-44dd-87a8-e28a2215e72e" />
</p>

### 🧪 Configuration 1: The "Under-trained" Model (Epoch=1, Rank=8)
* **Hypothesis:** One pass over the data might not be enough to override the strong base instruction.
* **Observation:** A mixed bag. The model attempts to be helpful but struggles with formatting and consistency, showing moderate hallucinations.
<p align="center">
  <img width="868" height="91" alt="image" src="https://github.com/user-attachments/assets/b85e8d6c-5d78-4b19-9cdb-a48ce1c940c1" />
</p>

### 🧪 Configuration 2: The "Over-written" Model (Epoch=3, Rank=8)
* **Hypothesis:** More training will force the model to behave like a customer service agent.
* **Observation:** The model becomes highly helpful and follows the data format well, but suffers from catastrophic forgetting—the pirate persona is entirely lost, replaced by a bland corporate tone.
<p align="center">
  <img width="740" height="39" alt="image" src="https://github.com/user-attachments/assets/0001d71a-560e-49bf-934f-9870afda5646" />

</p>

### 🧪 Configuration 3: The "Sweet Spot" (Epoch=3, Rank=16)
* **Hypothesis:** Increasing the LoRA rank provides the model with more parameter space to hold *both* personas simultaneously.
* **Observation:** The perfect fusion. The model is highly helpful, follows the required format excellently, and successfully maintains the pirate persona while booking the ticket!
<p align="center">
  <img width="828" height="61" alt="image" src="https://github.com/user-attachments/assets/569dcb35-ce5c-4aa7-8d57-df8d03c2c7f1" />

</p>

---

## 📊 Evaluation Summary

To systematically evaluate the trade-offs, I compared the models across four key dimensions: Pirate Persona Maintenance, Task Helpfulness, Data Formatting, and Hallucination levels.

<p align="center">
  <img width="878" height="130" alt="image" src="https://github.com/user-attachments/assets/5c782555-f3bb-423b-b853-4951b54623af" />
  <br>
  <em>Figure 1: Comprehensive evaluation of Persona Fusion across different LoRA configurations.</em>
</p>

### 📉 Deep Dive: The "Loss vs. Persona" Paradox
*Why doesn't the lowest training loss equal the best model?*

As shown in the training logs, **Experiment 3 (the optimal persona fusion) did not achieve the lowest training loss.** In fact, Experiment 2 achieved a lower training loss, yet suffered from complete catastrophic forgetting of its pirate persona. 

**The Explanation:**
This highlights a classic disconnect between Next-Token Prediction loss and human-aligned behavioral goals. 
* The SFT loss strictly measures how well the model mimics the *MultiWOZ dataset*. Since MultiWOZ contains only polite, standard corporate dialogue, driving the training loss too low (Exp 2) caused the model to **overfit to the dataset's style**, effectively erasing the "Pirate" system prompt.
* Experiment 3 achieved the perfect balance. By increasing the parameter capacity (Rank=16), the model learned the **structural mechanics** of the dataset (e.g., extracting entities, formatting responses) while resisting stylistic homogenization. The slightly higher training loss is actually a positive indicator that the model retained its creative prior knowledge.

**Engineering Takeaway:** In LLM persona engineering and behavioral alignment, optimizing blindly for the lowest loss function is dangerous. Human-perceived qualitative evaluation (Helpfulness + Persona maintenance) is a far more critical metric than raw loss values.

---

## 💡 Conclusion & Engineering Takeaways

This experiment highlights a crucial lesson in AI alignment and behavioral fine-tuning:

1. **Catastrophic Forgetting is Real:** If you train an LLM too hard on a new task (like Customer Service) without enough parameter capacity, it will completely "forget" its underlying instructions (the Pirate persona). 
2. **Rank ($r$) is the Key to Complexity:** By increasing the LoRA Rank from 8 to 16, I effectively gave the model a larger "brain capacity" to handle the conflicting constraints. It proved that **higher rank is necessary when the SFT task fundamentally clashes with the base prompt's persona.**
3. **The Art of Balancing:** Achieving true persona fusion requires meticulous tuning. The Epoch=3, Rank=16 configuration proved that an LLM can successfully navigate complex, structured data-extraction tasks without losing its assigned creative voice.
