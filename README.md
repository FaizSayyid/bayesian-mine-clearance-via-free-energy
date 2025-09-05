# Expected Free Energy Minefield Demo

This repo contains a simple demo of a robot exploring a noisy minefield using **expected free energy** (EFE) and Bayesian stats.

The robot balances:
- **Epistemic value**: information gain from probing new cells.
- **Extrinsic value**: risk of stepping on a mine.

---
naieve_EFE_no_revisit.ipynb: In this demo the robot can sample a new tile exactly once. Revisits confer no additional information. 
EFE_revisit.ipynb: In this demo the robot can re-probe tiles. Revisits confer additional information and a sharper posterior. 

### Single-visit vs. revisit

<p float="left">
  <img src="images/visit_pdf.png" width="45%" />
  <img src="images/revisit_pdf.png" width="45%" />
   <img src="images/visit_board.png" width="45%" />
  <img src="images/revisit_board.png" width="45%" />
</p>
---

## Run the notebooks

**naieve_EFE_no_revisit.ipynb**: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/FaizSayyid/mine_clearance_via_free_energy/blob/main/naieve_EFE_no_revisit.ipynb)   
**EFE_revisit.ipynb**: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/FaizSayyid/mine_clearance_via_free_energy/blob/main/EFE_revisit.ipynb)

You can view the notebook on GitHub, or **run it** on colab:




