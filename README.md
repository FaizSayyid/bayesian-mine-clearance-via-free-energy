# Expected Free Energy Minefield Demo

This repo contains a simple demo of a robot exploring a noisy minefield using **expected free energy** (EFE) and Bayesian statistics.

The robot holds a generative model of the world (beta-bernoulli over mine presence and a noisy mine sensor). At each stage we compute the posterior mine probabilities (the risk) and take off the difference between the prior entropy and the evidence wieghted entropy of each outcome of a given move. The move with the lowest expected free energy is then selected.

The robot balances:
- **Epistemic gain**: information gain from probing new cells.
- **Extrinsic risk**: risk of stepping on a mine.

Written in haste.

---

## Generative model

Each cell \(s_i \in \{0,1\}\) (mine or no mine) has a Beta–Bernoulli prior:

![](https://latex.codecogs.com/svg.image?s_i%20%5Csim%20%5Cmathrm%7BBernoulli%7D(%5Cpi),%20%5Cquad%20%5Cpi%20%5Csim%20%5Cmathrm%7BBeta%7D(%5Calpha_0,%5Cbeta_0))

The sensor has a confusion matrix:

![](https://latex.codecogs.com/svg.image?%5Cbegin%7Barray%7D%7Bc%7Ccc%7D%20%26%20%5Ctext%7BSensor%20reads%20%22mine%22%7D%20%26%20%5Ctext%7BSensor%20reads%20%22no%20mine%22%7D%20%5C%5C%5Chline%20%5Ctextbf%7BMine%20(s%3D1)%7D%20%26%20P(o%3D1%5Cmid%20s%3D1)%3D0.95%20%26%20P(o%3D0%5Cmid%20s%3D1)%3D0.05%20%5C%5C%20%5Ctextbf%7BSafe%20(s%3D0)%7D%20%26%20P(o%3D1%5Cmid%20s%3D0)%3D0.30%20%26%20P(o%3D0%5Cmid%20s%3D0)%3D0.70%20%5Cend%7Barray%7D)

---

## Posterior update

![](https://latex.codecogs.com/png.latex?a%20%3D%20P(o%3D1%20%5Cmid%20S%3D1))  
![](https://latex.codecogs.com/png.latex?1-a%20%3D%20P(o%3D0%20%5Cmid%20S%3D1))  
![](https://latex.codecogs.com/png.latex?b%20%3D%20P(o%3D1%20%5Cmid%20S%3D0))  
![](https://latex.codecogs.com/png.latex?1-b%20%3D%20P(o%3D0%20%5Cmid%20S%3D0))  

- If mine ($S=1$):

![formula](https://latex.codecogs.com/png.latex?P(\text{data}\mid%20S%3D1)%20%3D%20a^{k}(1-a)^{n-k})

- If safe ($S=0$):

![formula](https://latex.codecogs.com/png.latex?P(\text{data}\mid%20S%3D0)%20%3D%20b^{k}(1-b)^{n-k})



### Bayes’ rule with those likelihoods

![formula](https://latex.codecogs.com/png.latex?P(S%3D1%20%5Cmid%20\text{data})%20%3D%20%5Cfrac{P(\text{data}\mid%20S%3D1)P(S%3D1)}{P(\text{data}\mid%20S%3D1)P(S%3D1)%20%2B%20P(\text{data}\mid%20S%3D0)P(S%3D0)})

Substituting the likelihoods:

![formula](https://latex.codecogs.com/png.latex?P(S%3D1%20%5Cmid%20\text{data})%20%3D%20%5Cfrac{p%20a^{k}(1-a)^{n-k}}{p%20a^{k}(1-a)^{n-k}%20+%20(1-p)%20b^{k}(1-b)^{n-k}})


![formula](https://latex.codecogs.com/png.latex?\boxed{P(S%3D1%20\mid%20k%20\text{%20pos},%20n-k%20\text{%20neg})%20=%20\frac{p%20a^{k}(1-a)^{n-k}}{p%20a^{k}(1-a)^{n-k}%20+%20(1-p)%20b^{k}(1-b)^{n-k}}})

---

###  Expected Free Energy (EFE)
For an action \(a\) (probing a cell):

![](https://latex.codecogs.com/svg.image?G(a)%20%3D%20%5Cunderbrace%7B%5Cmathbb%7BE%7D_%7Bq(o%7Ca)%7D%5B-%5Cln%20p(o)%5D%7D_%7B%5Ctext%7Bextrinsic%20risk%7D%7D%20-%20%5Cunderbrace%7B%5Cbig(H%5Bq(s)%5D%20-%20%5Cmathbb%7BE%7D_%7Bq(o%7Ca)%7D%5BH(q(s%7Co,a))%5D%5Cbig)%7D_%7B%5Ctext%7Bepistemic%20gain%7D%7D)

- ![](https://latex.codecogs.com/svg.image?q(s)) : current posterior belief  
- ![](https://latex.codecogs.com/svg.image?q(s%7Co,a)) : imagined posterior after seeing \(o\) at cell \(a\)  
- ![](https://latex.codecogs.com/svg.image?H%5B%5Ccdot%5D) : entropy  

Entropy of a Bernoulli with parameter \(p\):

![](https://latex.codecogs.com/svg.image?H(p)%20%3D%20-%5Cbig(p%5Cln%20p%20&plus;%20(1-p)%5Cln(1-p)%5Cbig))

The agent picks the action \(a^*\) with lowest EFE, trading off exploration (epistemic gain) against exploitation (low risk).

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
