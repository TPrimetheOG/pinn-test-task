# Experimental Study of PINNs for a Time-Scaled Damped Harmonic Oscillator
Anshul Singh

## 1 Problem Setup

We consider the damped harmonic oscillator:

ẍ(t) + 2ξ ẋ(t) + x(t) = 0 (1)

with:
- T = 20
- ω0 = 1 (time-scaled)
- ξ ∈ (0.1, 0.4)
- x(0) = 0.7, ẋ(0) = 1.2

True solution:

x(t) = e^{-ξt} (A cos(ω_d t) + B sin(ω_d t)),  ω_d = sqrt(1 − ξ^2) (2)

All references to ω below correspond to ω_model (SIREN frequency).

---

## 2 Base PINN Configuration

- Architecture: 4 layers × 32 neurons
- Activations: Tanh, SIREN
- Optimizers:
  - ADAM with varying LR (optimal ≈ 0.001)
  - L-BFGS (1.0)
- Training: Two phase (ADAM → L-BFGS)

Hard constraint formulation:

x(t) = x0 + v0 t + t^2 Nθ(t) (3)

---

## 3 Experiment 1: Damping Ratio Fixed + Tanh vs SIREN

ξ = 0.3

### 3.1 Ansatz Forms

x(t) = x0 + v0(1 − e^{-t}) + (1 − e^{-t})^2 Nθ(t) (4)

x(t) = x0 + v0 t + t^2 Nθ(t) (5)

### 3.2 Results

| Model | ω_model | Ansatz | Final Loss |
|------|--------|--------|-----------|
| SIREN | 30 | exp | 8.095e-1 |
| Tanh | - | exp | 8.286e-3 |
| SIREN | 40 | exp | 1.384e-2 |
| SIREN | 1 | exp | 1.368e-5 |
| SIREN | 30 | t^2 | 6.66e1 |
| SIREN | 1 | t^2 | 2.37e-1 |
| SIREN | 0.1 | exp | 9.175e-3 |

### 3.3 Observations

- SIREN strongly depends on ω_model
- Tanh is more stable but suboptimal
- t^2 ansatz performs poorly
- Best early result: ω_model = 1

---

## 4 Experiment 2: Model Frequency Sweep

- ω_model < 0.4 → flat loss landscape
- ω_model > 1.8 → rugged landscape

Conclusion:

0.4 ≤ ω_model ≤ 1.8 (6)

---

## 5 Experiment 3: Warm Start vs Cold Start

- Warm-start: slightly worse
- Cold-start (T=20): better

---

## 6 Experiment 4: Loss vs L2 Mismatch

- Loss: 6.36 × 10^-7
- Relative L2: 0.52

→ Introduced L2 evaluation

---

## 7 Experiment 5: Time Horizon Scaling

| T | L2 | Loss |
|--|----|------|
| 2.5 | 1.04e-4 | 5.1e-6 |
| 5 | 1.42e-4 | 1.38e-6 |
| 10 | 5.03e-3 | 3.0e-5 |
| 20 | 8.49e-3 | 2.16e-5 |

Observations:
- L2 increases with time
- Loss remains small
- ~100× mismatch

---

## 8 Experiment 6: Precise Frequency Sweep

ω_model ≈ 1.137 (7)

Rel L2 ≈ 1.89 × 10^-4 (8)

---

## 9 Experiment 7: Variable ξ

- Rel L2 = 6.99 × 10^-2
- Stable ω range: 0.4 to 4
- Best ω_model ≈ 3.0787

---

## 10 Experiment 8: Sample Scaling

- 3k–15k: ~6e-2
- 30k: 5.89e-2
- 60k: 4.9e-2

Conclusion: diminishing returns

---

## 11 Experiment 9: Optimization Depth

- L-BFGS improves till ~1600 iterations

---

## 12 Experiment 10: Precision Study

Rel L2 = 4.32 × 10^-3 (9)

---

## 13 Experiment 11: Ansatz Formulations

(1) Time-only:
x(t) = x0 + v0(1 − e^{-t}) + (1 − e^{-t})^2 Nθ(t, ξ) (10)

(2) Exponential gated:
x(t) = x0 + v0(1 − e^{-ξt}) + (1 − e^{-ξt})^2 Nθ(t, ξ) (11)

(3) Linear physics:
x(t) = e^{-ξt}[x0 + (v0 + ξx0)t] + (1 − e^{-ξt})^2 Nθ(t, ξ) (12)

(4) True solution:
ω_d = sqrt(1 − ξ^2)
A = x0
B = (v0 + ξx0)/ω_d (13)

x(t) = e^{-ξt}[A cos(ω_d t) + B sin(ω_d t)] + (1 − e^{-ξt})^2 Nθ(t, ξ) (14)

Best:
Rel L2 ≈ 1.2 × 10^-4

---

## 14 Experiment 12: Fourier Features

Rel L2 ≈ 2.9 × 10^-2 (16)

---

## 15 Experiment 13: Temporal Weighting

- Worse performance

---

## 16 Experiment 14: RAR

Baseline:
Rel L2 = 4.0697 × 10^-3 (17)

Best:
Rel L2 ≈ 1.3716 × 10^-3 (21)

Projected:
Rel L2 → 10^-4

---

## 17 Conclusion and Future Work

- Key factors:
  - Ansatz
  - Model frequency
  - Sampling

- Achieved:
  ~10^-4 error

### Future Work

- DeepONet / PINOs
- Phase-based metrics
- Better optimization
- Push to 10^-5 – 10^-6
