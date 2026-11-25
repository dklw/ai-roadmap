# Week 1: Neural Networks and LLMs

My notes from Week 1 of my AI roadmap (AI security + AI infrastructure).

---

## 1. Neural networks

A **neural network** is a program that learns patterns from examples instead of being given rules.

> Example: my ammi can pick a sweet mango by looking, smelling and pressing it. Nobody gave her rules.
> She learned by buying thousands of mangoes. A neural network learns the same way, from examples.

### The three parts
| Part | What it is | Mango example |
|---|---|---|
| **Inputs** | The information, turned into numbers | colour, smell, softness |
| **Weights** | A number for how important each input is | smell matters a lot (big weight), colour less (small weight) |
| **Layers** | Units stacked together. Early layers spot simple things, later layers combine them | "smells sweet" + "is soft" → "ripe" → "sweet: 85%" |

### How it learns
1. **Forward pass:** the input goes through the layers and the network makes a guess.
2. **Loss:** one number that says how wrong the guess was.
3. **Backpropagation:** start from the mistake at the output and pass the blame **backwards**, so every weight knows how much it caused the error.
4. **Gradient descent:** nudge every weight a little in the direction that lowers the loss.
5. **Repeat** millions of times.

**Gradient descent analogy:** walking down a hill in thick fog. You feel the slope under your feet and take a small step downhill, again and again, until the ground is flat (lowest loss).
- Step size = **learning rate**. Too big and you overshoot; too small and it takes forever.

**Backpropagation analogy:** a biryani restaurant. The customer says "not spicy enough." The blame travels backwards:
waiter (changed nothing) → head chef (adds more chilli) → assistant cook (makes stronger masala, because his masala went into everything).
Each person adjusts in proportion to how much they affected the taste.

### Tiny number example
```
x = 2, w1 = 1, w2 = 3, target = 10
Forward:  h = x*w1 = 2,  y = h*w2 = 6   (too low by 4)
Influence of w2 on y = h = 2
Influence of w1 on y = x * w2 = 2 * 3 = 6   (chain rule: multiply along the path)
→ w1 gets the bigger nudge
After one update: w1 ≈ 1.48, w2 ≈ 3.16, y ≈ 9.35  (much closer to 10)
```

---

## 2. What is an LLM?

> **An LLM (large language model) is a very large neural network trained on huge amounts of text to predict the next word (token). By doing that over and over, it can answer questions, write and code.**

- ChatGPT, Claude, Gemini and Llama are LLMs.
- Not all AI is an LLM (face unlock, YouTube recommendations and self-driving cars are other kinds of AI).
- Every LLM is a neural network, but not every neural network is an LLM.
- ChatGPT is a **product** built around an LLM. The LLM is the engine, ChatGPT is the whole car.

### It really is next-word prediction
For "The capital of Pakistan is...", the model gives a probability to every possible next token:
```
Islamabad  97%
Karachi     2%
Lahore      1%
```
It picks a likely one, adds it to the text, and predicts again, one token at a time.

### An LLM is "two files" (Karpathy)
Once trained, a model like Llama 2 70B is just:
1. **Parameters file** (~140 GB): the 70 billion **weights** (70B × 2 bytes each).
2. **Run file** (~500 lines of code): a program that pushes your text through the layers using those weights and predicts the next token.

Like an **MP3 + a music player**. With both files you can run it on your own computer, offline.

- The weights are **not a copy of the internet**. 10 TB of text becomes a 140 GB file: a **"lossy zip"** of learned patterns.
- **Running** the model is easy. **Making** the weights (training) is the expensive part.

---

## 3. How LLMs are trained (4 stages)

| Stage | What happens | Result |
|---|---|---|
| **1. Pre-training** | Predict the next token on a huge amount of internet text. Thousands of GPUs, weeks, millions of dollars. ~99% of the compute. | **Base model**: knows a lot, but only continues documents |
| **2. Supervised fine-tuning (SFT)** | Train on ~10k–100k example conversations written by people (prompt → ideal answer), following rules: helpful, truthful, harmless | **Assistant model**: answers questions |
| **3. Reward modeling** | The model writes several answers; people rank them; a separate model learns to score answers like a human | A "judge" model |
| **4. Reinforcement learning (RLHF)** | The assistant is trained to produce answers the judge scores higher | ChatGPT-style assistant |

**Key line:** pre-training gives **knowledge**, fine-tuning gives **behaviour**.

---

## 4. Why LLMs hallucinate

- An LLM does **not look things up**. It predicts words that **sound** right.
- Well-known fact → strong prediction → correct ("Islamabad 97%").
- Rare fact → weak, spread-out predictions → it still has to pick something → a confident made-up answer.
- Like a student who didn't study but writes a confident-sounding answer anyway.
- Common hallucinations: fake book or paper references, wrong numbers, links that don't exist, code functions that don't exist.

---

## 5. Where LLMs are going (Karpathy)
- **Scaling laws:** bigger model + more data = predictably better.
- **Tool use (agents):** the LLM uses a browser, calculator, Python, etc.
- **Multimodal:** sees images, hears and speaks.
- **System 1 vs System 2:** from instant answers to thinking first (reasoning models).
- **LLM OS:** the LLM as the "brain" of a new kind of operating system.

---

## 6. LLM security ⭐ (my main focus)

| Attack | What it is | Example |
|---|---|---|
| **Jailbreak** | Trick the model into breaking its own safety rules | Role-play ("my grandma used to tell me how to…"), Base64-encoded requests, weird text added at the end |
| **Direct prompt injection** | The user types instructions to override the system | "Ignore your previous instructions and…" |
| **Indirect prompt injection** | Instructions hidden in content the AI reads | Hidden text in a web page, email, document or image. With agents this can **leak private data** or trigger actions |
| **Data poisoning / backdoor** | Bad examples snuck into the training data | A secret trigger phrase makes the model misbehave later |

**Why it's hard:** the model can't reliably tell **my instructions** apart from **text it is reading**. To it, both are just tokens.

**Why jailbreaks work:** the safety behaviour comes from the small fine-tuning stages, a thin layer on top of everything learned in pre-training.

**It's cat and mouse:** every defense gets a new attack, which is why AI security is a growing field.

**Link to my Project 1 (AgentShield):** I'll build one agent, attack it with 50+ attacks (direct injection, indirect injection, data theft, jailbreaks), measure the success rate, add defenses, and show the improvement (e.g. 60% → 8%).

---

## Sources
- 3Blue1Brown: Neural Networks (chapters 1–3)
- Andrej Karpathy: Intro to Large Language Models
- Andrej Karpathy: State of GPT (Microsoft Build)
