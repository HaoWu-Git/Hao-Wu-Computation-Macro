**Team members / partners: Ziyi Feng and Hao Wu.**

# Problem Set 2:

## Files and how to run

Place these files in the `ps2/` directory of each team member's course repository:

- `derivations.pdf`: derivations for Part 1 and Questions 2.1 and 3.1.
- `ps2_solution.ipynb`: Python implementation, numerical experiments, tables and figures for Parts 2 and 3.
- `README.md`: execution instructions, discussion answers and AI disclosure.

Select the Python environment containing corresponding packages, restart the kernel and run all cells from top to bottom. No external data files are needed. The numerical tables below reproduce the uploaded notebook's saved outputs. 

## Part 1 

## Part 2: CARA household

### 2.1 Continuous-normal versus discrete-income policy

| Coefficient | Continuous normal | Seven-state Rouwenhorst | Discrete minus normal |
| --- | ---: | ---: | ---: |
| $\mu$ | 0.038461538 | 0.038461538 | 0 |
| $\nu$ | 0.247252747 | 0.247189479 | -6.326786843e-5 |
| $f$ | 0.957660911 | 0.957662125 | 1.214595186e-6 |

The largest consumption difference on the state grid is 1.671e-5, which is economically negligible relative to consumption of order one. Rouwenhorst matches the process's selected moments, but its conditional exponential moment differs slightly from the Gaussian one. Solver errors therefore use the exact discrete-income policy as the benchmark. This affine benchmark concerns the model with unrestricted assets; computational bounds can distort its numerical approximation near the grid edges.

### 2.2(a) Five methods at the baseline calibration

Here $\gamma=2$, $r=0.04$, $\beta=1/R$, $\bar z=1$, $\sigma=0.1$ and $\rho=0.9$, with 400 uniformly spaced asset points on [-15, 25] and seven income states. Both tolerances are 1e-6 and the iteration cap is 5000. $e_W$ and $e_G$ are maximum absolute consumption-policy errors on the window and whole grid; Euler errors are relative consumption residuals.

| Method | Iterations | Time (s) | ms/iteration | $e_W$ | $e_G$ | Euler max | Mean log10 absolute Euler error |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| VFI on grid | 314 | 4.22 | 13.43 | 4.6e-2 | 1.8e-1 | 1.4e-2 | -2.2 |
| VFI off grid | 314 | 79.90 | 254.46 | 4.2e-6 | 1.8e-1 | 1.8e-7 | -7.0 |
| Howard | 15 | 5.82 | 387.99 | 1.6e-7 | 1.8e-1 | 7.4e-8 | -8.0 |
| Time iteration | 136 | 1.72 | 12.61 | 4.6e-5 | 1.8e-1 | 3.3e-6 | -5.9 |
| EGM | 136 | 0.06 | 0.46 | 1.9e-5 | 1.8e-1 | 8.1e-7 | -6.2 |

All five methods converge with the expected iteration counts. Howard is most accurate on the window, while EGM is fastest in these saved runs. The whole-grid errors are much larger because the finite asset bounds differ from the unrestricted benchmark. The notebook plots the consumption policies and the error profiles along the asset grid.

### 2.2(b) Grid refinement

| Asset points | Method | $e_W$ | ms/iteration |
| ---: | --- | ---: | ---: |
| 200 | VFI on grid | 8.8229e-2 | 2.742 |
| 400 | VFI on grid | 4.5801e-2 | 13.433 |
| 800 | VFI on grid | 2.1395e-2 | 38.530 |
| 200 | VFI off grid | 4.2029e-6 | 178.505 |
| 400 | VFI off grid | 4.1875e-6 | 254.458 |
| 800 | VFI off grid | 4.1872e-6 | 357.226 |
| 200 | Time iteration | 2.5120e-4 | 8.482 |
| 400 | Time iteration | 4.6396e-5 | 12.612 |
| 800 | Time iteration | 6.7624e-6 | 20.595 |
| 200 | EGM | 1.8699e-5 | 0.350 |
| 400 | EGM | 1.8699e-5 | 0.455 |
| 800 | EGM | 1.8699e-5 | 0.823 |

Refinement reduces on-grid VFI's choice-discretization error and time iteration's interpolation error; off-grid VFI and EGM remain near their stopping-error floors. On-grid VFI has approximately quadratic work in the number of assets because it considers every asset choice at every asset state. The other methods have approximately linear work for fixed income states and search settings, with additional interpolation/search overhead. All measured times per iteration increase with grid size; these short timing samples need not follow the theoretical scaling exactly. EGM interpolates an almost affine interior policy here, explaining its negligible benefit from grid refinement at the baseline tolerance.

### 2.2(c) Tighter stopping rules

| Method | Iterations, 1e-6 | Iterations, 1e-8 | $e_W$, 1e-6 | $e_W$, 1e-8 |
| --- | ---: | ---: | ---: | ---: |
| VFI on grid | 314 | 431 | 4.5801e-2 | 4.5801e-2 |
| VFI off grid | 314 | 431 | 4.1875e-6 | 1.3726e-7 |
| Time iteration | 136 | 249 | 4.6396e-5 | 6.5294e-5 |
| EGM | 136 | 249 | 1.8699e-5 | 2.2763e-7 |

On-grid VFI is limited by the discrete choice grid. Off-grid VFI and EGM improve sharply, indicating that their baseline errors are primarily set by stopping. Time iteration is limited by interpolation at this grid size: tighter convergence can remove a stopping error that partly offsets the interpolation bias, increasing the true error. Thus a smaller iteration distance need not imply a smaller policy error.

### 2.2(d) Euler residuals versus true policy errors

At the baseline, both Euler summaries and $e_W$ give the same ranking: Howard, off-grid VFI, EGM, time iteration, then on-grid VFI. Their magnitudes differ: the Euler measure is a relative local consistency residual calculated using the computed future policy, whereas $e_W$ measures absolute distance from the exact policy. Small residuals can understate policy-level error and propagated boundary or approximation effects; they do not by themselves verify the global solution. Grid-bound choices are excluded because their optimality condition is an inequality rather than the interior Euler equality.

## Part 3: CRRA household

### 3.1 What changes across models?

- **Utility.** CARA has $u(c)=-e^{-\gamma c}/\gamma$, $u_c(c)=e^{-\gamma c}$ and $u_c^{-1}(m)=-\ln(m)/\gamma$, with real consumption and $m>0$. CRRA has $u(c)=c^{1-\gamma}/(1-\gamma)$, $u_c(c)=c^{-\gamma}$ and $u_c^{-1}(m)=m^{-1/\gamma}$, requiring $c>0$ and $m>0$. Here $\gamma=2$.
- **Income.** CARA discretizes the level deviation $e=z-\bar z$ and sets $z_m=\bar z+e_m$. CRRA discretizes log income and exponentiates its states. Its innovation standard deviation is 0.2, so the unconditional log-income standard deviation passed to Rouwenhorst is $0.2/\sqrt{1-0.9^2}$.
- **Grid.** CARA uses the uniform grid on [-15, 25]. CRRA imposes the economic lower bound zero and uses 200 quadratically spaced points on [0, 100], concentrating resolution near the borrowing kink. The upper bound is a numerical truncation and must be checked for boundary effects.
- **Feasibility.** With $x=z+Ra$, CARA's numerical choice interval implies $x-25\le c\le x+15$. CRRA requires $c\in[x-100,x]\cap(0,\infty)$; the code uses a positive floor of 1e-10. At a binding borrowing limit, $a'=0$ and $c=x$.

### 3.2 Solver comparison and accuracy

The CRRA parameters are $\gamma=2$, $r=0.03$, $\beta=0.96$, $\rho=0.9$ and $\sigma_\varepsilon=0.2$, with seven income states and the grid described above. Both stopping tolerances are 1e-6.

| Method | Iterations | Time (s) | ms/iteration | Euler max | Mean log10 absolute Euler error |
| --- | ---: | ---: | ---: | ---: | ---: |
| VFI on grid | 339 | 1.02 | 3.00 | 2.5e-1 | -1.8 |
| VFI off grid | 339 | 66.01 | 194.73 | 2.4e-5 | -5.5 |
| Howard | 11 | 3.76 | 341.74 | 2.4e-5 | -5.5 |
| Time iteration | 272 | 3.06 | 11.25 | 5.5e-5 | -4.7 |
| EGM | 272 | 0.13 | 0.49 | 3.7e-7 | -7.2 |

All methods converge with the expected counts. EGM ranks first by Euler accuracy, off-grid VFI and Howard are effectively tied next, then time iteration and on-grid VFI. In terms of the maximum Euler-implied relative consumption correction, EGM achieves roughly six decimal orders of accuracy, off-grid VFI/Howard and time iteration roughly four, while on-grid VFI does not achieve one full decimal order uniformly. I would use EGM for the main results and off-grid VFI/Howard as cross-checks; time iteration is less accurate, and on-grid VFI is too coarse for precise results.

These are residual-based assessments, not certified significant digits of the unknown optimal policy. Part 2 showed that Euler residuals can be substantially smaller than true policy errors. Its CARA error-to-residual ratios cannot be transferred quantitatively to CRRA, whose curvature, constraints, grid and normalization differ. The notebook includes the consumption policies near the borrowing limit and the Euler-error profile along the asset grid.

### 3.3 Borrowing constraint and locations of the largest errors

Shares below count asset-grid points separately at each income state; they are not population shares.

| Method | z1 | z2 | z3 | z4 | z5 | z6 | z7 |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| VFI on grid | 2.0% | 2.0% | 0% | 0% | 0% | 0% | 0% |
| VFI off grid | 2.0% | 1.5% | 0% | 0% | 0% | 0% | 0% |
| Howard | 2.0% | 1.5% | 0% | 0% | 0% | 0% | 0% |
| Time iteration | 2.0% | 1.5% | 0% | 0% | 0% | 0% | 0% |
| EGM | 2.0% | 1.5% | 0% | 0% | 0% | 0% | 0% |

At $a'=0$, the household cannot borrow more, and the Euler condition is $u_c(c)\ge\beta R E[u_c(c')\mid z]$, so equality residuals are inappropriate there. Across all nonbinding grid states, on-grid VFI's largest residual is about 0.251 at assets 16.1612 in income state 2, reflecting discrete savings choices. Off-grid VFI, Howard, time iteration and EGM peak near assets 98.9975 in income state 7, with residuals about 0.0129, 0.0129, 0.00156 and 0.000331 respectively. These peaks are consistent with truncation and interpolation effects near the artificial upper boundary, even when the current choice is interior; low-income policies can also be delicate around the borrowing kink. Consequently, whole-grid maxima and interior-window statistics answer different questions.

## Use of AI tools

ChatGPT was used to review the supplied derivations and notebook, and check the numerical results. We have reviewed the AI-assisted suggestions and understand the derivations, code, numerical methods, and interpretation of the results. 
