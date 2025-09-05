# Expected Free Energy Minefield Demo

This repo contains a simple demo of a robot exploring a noisy minefield using **expected free energy** (EFE) and Bayesian statistics.

The robot balances:
- **Epistemic value**: information gain from probing new cells.
- **Extrinsic value**: risk of stepping on a mine.

---

The robot holds a generative model of the world (beta-bernoulli over mine presence). At each stage we compute the posterior mine probabilities (the risk) and take off the difference between the prior entropy and the evidence waited entropy of what it expects if makes a move.
The move with the lowest expected free energy is then selected.

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
