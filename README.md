# Expected Free Energy Minefield Demo

This repo contains a simple demo of a robot exploring a noisy minefield using **expected free energy** (EFE) and Bayesian statistics.

The robot balances:
- **Epistemic value**: information gain from probing new cells.
- **Extrinsic value**: risk of stepping on a mine.

Written in haste.

---

The robot holds a generative model of the world (beta-bernoulli over mine presence and a noisy mine sensor). At each stage we compute the posterior mine probabilities (the risk) and take off the difference between the prior entropy and the evidence waited entropy of what it expects if makes a move.
The move with the lowest expected free energy is then selected.

### Sensor model



| True state    | Sensor reads "mine"        | Sensor reads "no mine"        |
|---------------|-----------------------------|--------------------------------|
| **Mine (S=1)** | P(o=1 ∣ S=1) = 0.95       | P(o=0 ∣ S=1) = 0.05           |
| **Safe (S=0)** | P(o=1 ∣ S=0) = 0.30       | P(o=0 ∣ S=0) = 0.70           |

In this sensor model we assume that the manufacturer has traded low false negatives for higher false positives.

## Notebooks

- **naieve_EFE_no_revisit.ipynb** — the robot samples each tile at most once. Revisits confer no additional information.  
- **EFE_revisit.ipynb** — the robot can re-probe tiles. Revisits confer additional information and yield a sharper posterior.

---

## Single-visit vs. revisit

**Posterior PDFs**

<p float="left">
  <img src="images/visit_pdf.png" width="45%" />
  <img src="images/revisit_pdf.png" width="45%" />
</p>

*Left: single-visit posterior stays diffuse. Right: revisits drive probabilities sharply toward 0/1.*

**Boards**

<p float="left">
  <img src="images/visit_board.png" width="45%" />
  <img src="images/revisit_board.png" width="45%" />
</p>

*Ground truths of the boards.*

---

## Run the notebooks

You can view the notebooks on GitHub or run them in Colab:

- **naieve_EFE_no_revisit.ipynb**  
  [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/FaizSayyid/mine_clearance_via_free_energy/blob/main/naieve_EFE_no_revisit.ipynb)

- **EFE_revisit.ipynb**  
  [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/FaizSayyid/mine_clearance_via_free_energy/blob/main/EFE_revisit.ipynb)


  # Expected Free Energy Minefield Demo

This repo contains a simple demo of a robot exploring a noisy minefield using **expected free energy (EFE)** and **Bayesian** updates.

The robot balances:
- **Epistemic value** — information gain from probing new cells.
- **Extrinsic value** — risk of stepping on a mine.

---

The robot holds a **generative model** of the world (Beta–Bernoulli prior over mine presence and a noisy sensor). At each step it:
1. Updates the **posterior mine probabilities** (risk),
2. Evaluates the **expected information gain** from probing a candidate cell,
3. Picks the action with the **lowest EFE** (best trade-off of risk vs information).

---

## Sensor model

| True state     | Sensor reads “mine”        | Sensor reads “no mine”        |
|----------------|-----------------------------|-------------------------------|
| **Mine (s=1)** | $P(o=1 \mid s=1)=0.95$      | $P(o=0 \mid s=1)=0.05$        |
| **Safe (s=0)** | $P(o=1 \mid s=0)=0.30$      | $P(o=0 \mid s=0)=0.70$        |

In this sensor model the manufacturer trades **low false negatives** for **higher false positives**.

---

## 🧮 Math

**1) Generative model (per cell)**  
Hidden state $s \in \{0,1\}$ indicates mine/no-mine. Let $\pi=\Pr(s=1)$.  

Prior and likelihood:
$$
\pi \sim \mathrm{Beta}(\alpha_0,\beta_0),
$$
$$
o \in \{0,1\},\quad
P(o=1\mid s)=
\begin{cases}
\alpha,& s=1 \\
\beta,& s=0
\end{cases}
$$

with $P(o=0\mid s=1)=1-\alpha$ and $P(o=0\mid s=0)=1-\beta$.

---

**2) Posterior updating (conjugate Beta–Bernoulli)**  
Each cell’s posterior mine probability remains Beta:  
$$
\pi \mid D \sim \mathrm{Beta}(\alpha,\beta).
$$

Given an observation $o$ at that cell, update $\alpha,\beta$ with the **responsibility** terms:
$$
P(s=1\mid o=1)=\frac{\alpha\,\mathbb{E}[\pi]}{\alpha\,\mathbb{E}[\pi]+\beta\,(1-\mathbb{E}[\pi])},
$$
$$
P(s=1\mid o=0)=\frac{(1-\alpha)\,\mathbb{E}[\pi]}{(1-\alpha)\,\mathbb{E}[\pi]+(1-\beta)\,(1-\mathbb{E}[\pi])}.
$$

---

**3) Expected Free Energy (EFE)**  
For probing action $a$ (a cell), define:
$$
G(a)=
\underbrace{\mathbb{E}_{q(o\mid a)}\big[-\ln p(o)\big]}_{\text{risk}}
-
\underbrace{\Big(H[q(s)]-\mathbb{E}_{q(o\mid a)}\,H[q(s\mid o,a)]\Big)}_{\text{epistemic gain}}.
$$

- $q(s)$: current posterior over that cell’s state.  
- $q(s\mid o,a)$: imagined posterior after observing $o$ at $a$.  
- $H[\cdot]$: entropy.  

The agent selects the cell with the **lowest $G(a)$**.  

---

## Notebooks

- **naive_EFE_no_revisit.ipynb** — the robot samples each tile at most once. Revisits confer no additional information.  
- **EFE_revisit.ipynb** — the robot can re-probe tiles. Revisits add evidence and sharpen the posterior.

---

## Single-visit vs revisit

**Posterior PDFs**

<p float="left">
  <img src="images/visit_pdf.png" width="45%" />
  <img src="images/revisit_pdf.png" width="45%" />
</p>

*Left: single-visit posterior stays diffuse. Right: revisits drive probabilities toward 0/1.*

**Boards**

<p float="left">
  <img src="images/visit_board.png" width="45%" />
  <img src="images/revisit_board.png" width="45%" />
</p>

*Ground truths of the boards.*

---

## Run the notebooks

Open in Colab:

- **naive_EFE_no_revisit.ipynb**  
  [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/FaizSayyid/mine_clearance_via_free_energy/blob/main/naive_EFE_no_revisit.ipynb)

- **EFE_revisit.ipynb**  
  [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/FaizSayyid/mine_clearance_via_free_energy/blob/main/EFE_revisit.ipynb)
