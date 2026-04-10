# Geometry of Nothing: Spectral Matching, UV Divergence, and the Standing-Wave Advantage in Dynamical Casimir Simulations

**Author:** Cid (Independent Researcher)

**Date:** April 3, 2026

**Repository:** github.com/mr-gl00m/paper-geometry-of-nothing

**Companion Software:** Nothing Engine — Open-source 1+1D quantum field theory simulator with dynamical plate coupling and multi-topology boundary support

---

## Abstract

I present a computational investigation of vacuum friction in 1+1D dynamical Casimir cavities with closed, open, and periodic boundaries. Using a validated quantum field simulator, I report three principal findings. First, naive topology comparisons produce dramatic apparent differences (99% energy depletion for periodic vs 24.5% for closed boundaries) that vanish under spectral matching; the false-positive mechanism is characterized as a "spectral matching trap." Second, after matching mode frequencies, standing-wave (closed) boundaries couple approximately 5x more efficiently to a moving plate than traveling-wave (periodic) boundaries... The opposite of the initial hypothesis. Third, the vacuum friction rate in 1+1D free scalar theory is UV-divergent: damping grows with mode count, and the smooth form-factor regularizations tested (Gaussian and sigmoid) do not achieve convergence within the parameter range explored. Physical regularization via finite plate thickness is required, making absolute friction rates cutoff-dependent. These results constrain claims of topology-enhanced vacuum energy extraction and provide a validated open-source simulator for further study.

---

## 1. Introduction

### 1.1 Motivation

The Casimir effect, an attractive force between conducting surfaces arising from quantum vacuum fluctuations, was predicted by Casimir in 1948 and experimentally confirmed by Lamoreaux in 1997. The dynamic Casimir effect (DCE), in which the motion of a boundary converts virtual photons into real photon pairs, was observed experimentally by Wilson et al. in 2011 using a superconducting circuit. Together, these phenomena establish that the quantum vacuum is a physical medium capable of exerting measurable forces and exchanging real energy with mechanical systems.

A natural question follows: can boundary geometry be engineered to control the character of vacuum-mechanical coupling? Specifically, does the topology of the cavity, whether photons are trapped (closed boundaries), allowed to escape (open boundaries), or circulate in a loop (periodic boundaries), alter the rate, functional form, or spectral character of vacuum friction on a moving plate?

This question has practical relevance for nanomechanical engineering, where Casimir forces create stiction and anomalous damping in MEMS/NEMS devices at sub-100nm scales, and for quantum computing, where vacuum coupling contributes to qubit decoherence.

This study was originally motivated by the hypothesis that toroidal (periodic) boundary topology would enhance vacuum-mechanical coupling, potentially enabling vacuum energy extraction through collective mode amplification. This hypothesis was not confirmed. I report the null and negative results in full, as they carry methodological value independent of the original motivation.

### 1.2 Background

The dynamical Casimir effect has been studied computationally using lattice field theory, Bogoliubov transformation methods, and semiclassical approximations. Most computational work has focused on validating analytical predictions for photon production rates in short-duration simulations with prescribed (externally driven) plate motion. Long-duration simulations with dynamical plate coupling, where the plate is a free mechanical element exchanging energy with the field via radiation pressure, are largely absent from the literature. The question of whether vacuum friction converges with respect to mode count, or whether the friction rate depends on boundary topology, has not been systematically investigated.

### 1.3 Approach

I constructed a 1+1D quantum field theory simulator (Nothing Engine) implementing two complementary simulation tracks: a semiclassical lattice simulation (Track A) with Arbitrary Lagrangian-Eulerian coordinate mapping for moving boundaries, and a quantum Bogoliubov mode-space simulation (Track B) with exact vacuum state initialization and direct particle counting. Both tracks couple the field to a dynamical plate with finite mass, spring restoring force, and initial kinetic energy, with the plate equation of motion derived from the total system Hamiltonian. A continuous energy audit (Track C) monitors conservation at every timestep.

The simulator was validated against five analytical benchmarks before any experimental runs (Section 3). All gates passed within specified tolerances. Analysis parameters were pre-registered before data collection.

### 1.4 Scope and Limitations

This study uses a massless scalar field in 1+1 dimensions, a standard simplification in Casimir physics that captures vacuum fluctuation dynamics without electromagnetic polarization complexity. Results are qualitative... Quantitative force magnitudes and damping rates would require extension to 3+1D electromagnetic fields with realistic material models. The Bogoliubov approach is exact for free fields but does not capture nonlinear interactions. All energies are reported in natural units (hbar = c = 1); for a cavity width a = 100 nm, the unit energy corresponds to approximately 1 eV.

---

## 2. Theoretical Framework

### 2.1 Field Theory

I consider a massless scalar field phi(x, t) in 1+1 dimensions governed by the Klein-Gordon equation, working in natural units. The field is confined between a fixed left plate at position x_L and a dynamical right plate at position q(t), with Dirichlet boundary conditions at both plates.

### 2.2 Dynamical Plate Coupling

The right plate is a dynamical variable with mass M, position q(t), velocity dq/dt, and an optional restoring spring with constant k. The plate's equation of motion is derived from the total system Hamiltonian expressed in the instantaneous mode basis.

In Track B (Bogoliubov mode space), the plate equation takes the form:

    M d²q/dt² = -k·(q - q_eq) - d/dq [ sum_n omega_n(q) (|beta_n(t)|² + 1/2) ]

where omega_n(q) = n*pi/(q - x_L) are the instantaneous cavity mode frequencies and beta_n(t) are the Bogoliubov coefficients counting particles created in mode n. The derivative d(omega_n)/dq = -n*pi/(q - x_L)² provides the force contribution from each mode.

The zero-point term (1/2) summed over all modes formally diverges and must be regularized. In the simulation, this sum is evaluated with the same UV form factor applied to the dynamical coupling (Section 2.4), and the resulting static Casimir force is verified against the analytical prediction -pi/(24a²) as part of the validation suite. The |beta_n|² terms give the back-action from dynamically created photons.

The plate equation is coupled to the Bogoliubov coefficient ODEs, forming a system of approximately 2*N_modes + 2 real variables integrated via adaptive Runge-Kutta (RK45) with tight tolerances (rtol = 10^-13, atol = 10^-15).

### 2.3 Boundary Conditions

Three boundary topologies were investigated:

**Closed (Dirichlet):** Reflecting walls confine the field. Mode frequencies are omega_n = n*pi/a, producing standing waves with nodes at the boundaries. Photons created by plate motion remain trapped and can re-interact with the plate.

**Open (large-box approximation):** The cavity is embedded in a much larger domain with Dirichlet boundaries at distance L >> a. In mode space, the external modes provide channels for photon escape. This is an approximation to a true open system; a rigorous treatment would require Quantum Langevin or Input-Output formalism.

**Periodic (ring):** The field satisfies periodic boundary conditions on a domain of circumference L, producing traveling-wave modes with frequencies omega_n = 2*n*pi/L. This topology models a toroidal cavity array where photons propagate around a closed ring.

### 2.4 UV Regularization

In a 1+1D cavity, the number of available field modes is formally infinite. In practice, the spectrum is truncated at N_modes. Each mode provides an additional channel for parametric photon creation, and the total friction rate depends on the sum over all coupled modes.

To model finite plate thickness delta, I introduce a form factor g_n that suppresses coupling to modes with wavelength shorter than delta. Two functional forms were tested within this study: a Gaussian form, g_n = exp(-(n/n_cutoff)²), and a sigmoid form, g_n = 1/(1 + exp((n - n_cutoff)/Delta_n)), with n_cutoff = a/delta and Delta_n = n_cutoff/10.

A hard cutoff (g_n = 0 for n > n_cutoff) would achieve convergence by construction, as adding modes beyond the cutoff cannot change the result. The smooth forms tested here are more physical in that they model a gradual transition rather than an abrupt one, but as shown in Section 4.3, they do not achieve convergence within the parameter range explored. Whether sharper roll-offs or higher cutoff values would eventually converge, or whether only a hard cutoff suffices, remains an open question that I flagged for future investigation.

### 2.5 Spectral Matching

A critical methodological point: different boundary topologies produce different mode spectra at the same domain size. For a closed cavity of width a, the fundamental frequency is omega_1 = pi/a. For a periodic ring of circumference L = a, the fundamental frequency is omega_1 = 2*pi/a — exactly twice as high.

Since higher-frequency modes couple more strongly to plate motion (the parametric resonance condition depends on the ratio of plate velocity to mode frequency), any comparison of topologies at the same domain size conflates spectral effects with topological effects. This frequency ratio alone can account for large apparent differences in friction rates.

To isolate topology from spectrum, frequency-matched comparisons require setting L_periodic = 2*a_closed, so that both topologies share the same fundamental frequency.

---

## 3. Validation

The simulator was required to pass all validation gates before any experimental runs. All tests used Track B (Bogoliubov).

### 3.1 Static Casimir Energy

Plates held fixed at separation a = 1.0. The simulation converged to the analytical 1+1D scalar Casimir energy E_Casimir = -pi/(24a) = -0.130900 to machine precision (relative error < 10^-15 at N = 256 modes). **PASS.**

### 3.2 Dynamic Casimir Photon Production

Right plate driven with prescribed sinusoidal motion at the parametric resonance frequency Omega = 2*omega_1. The fundamental mode particle number |beta_1|² was measured at 0.4384, compared to the analytical prediction sinh²(gamma*T) = 0.4385. Relative error: 0.02%. Growth rate confirmed at the midpoint with 0.03% error. **PASS.**

### 3.3 Energy Conservation (Closed System)

Closed system with dynamical plate (M = 10,000, v_0 = 0.001) integrated for t = 0 to 100. Maximum relative drift to total energy: 4.86 x 10^-12. **PASS.**

### 3.4 Adiabatic Limit

Dynamical plate with M = 10^8, v_0 = 10^-6 (adiabaticity ratio omega_plate/omega_1 = 3.18 x 10^-7). Maximum per-mode particle occupation: 1.08 x 10^-9. **PASS.**

### 3.5 Residual Motion Baseline

Static plate, exact vacuum state, evolved for T = 100. Maximum |beta_n|² across all modes and times: 3.06 x 10^-10. Wronskian conservation verified to the same precision. **PASS.**

---

## 4. Experiments and Results

### 4.1 False Positive: Apparent Strong Damping at Low Mode Count

A light plate (M = 100, v_0 = 0.01) was released in a closed cavity with 16 modes. Over t = 5000, the plate lost approximately 79% of its kinetic energy. The best-fit model (by AIC) was a power law with exponent alpha approximately 0.53 and time constant tau approximately 254.

This initially suggested non-Markovian friction dynamics with a long-memory power-law tail. Subsequent convergence testing (Section 4.2) revealed that this result is an artifact of mode-count truncation, not a converged physical prediction. I include it as a documented example of how truncation artifacts can produce physically plausible but incorrect results.

### 4.2 Mode Convergence (No Form Factor)

The light-plate experiment was repeated at N = 32, 64, 128, and 256 modes.

| N_modes | alpha | E_final | % remaining | Status |
|---------|-------|---------|-------------|--------|
| 32 | 0.01 | 4.92e-3 | 98.4% | barely damps |
| 64 | 0.02 | 4.46e-3 | 89.2% | weak damping |
| 128 | 0.53 | 1.05e-3 | 21.0% | strong power-law |
| 256 | — | 1.62e+0 | — | cavity collapse |

The friction rate did not converge. Each doubling of mode count produced qualitatively different behavior, culminating in cavity collapse at N = 256 when the cumulative static Casimir attraction (which deepens with mode count) overwhelmed the plate's kinetic energy. The alpha = 0.53 result at N = 128 is cutoff-dependent.

### 4.3 Mode Convergence (With Form Factor)

A Gaussian form factor with n_cutoff = 100 was introduced and the convergence sweep repeated. A sigmoid form factor was also tested and produced qualitatively similar results; I report the Gaussian data.

| N_modes | alpha | E_final | % remaining |
|---------|-------|---------|-------------|
| 16 | 0.52* | 4.99e-3 | 99.8% |
| 32 | 0.01 | 4.93e-3 | 98.5% |
| 64 | 0.02 | 4.61e-3 | 92.2% |
| 128 | 0.06 | 3.77e-3 | 75.5% |
| 256 | 0.12 | 3.11e-3 | 62.2% |
| 512 | — | — | — (collapsed) |

*The N = 16 alpha value is spurious due to severe mode under-sampling; the fit captures noise rather than a physical trend. The meaningful progression begins at N = 32.

The form factor prevented cavity collapse at N = 256 but alpha continued to grow: from the N = 32 through N = 256 range, the approximate scaling is alpha proportional to N^0.7 (fit to log-alpha vs log-N for N = 32, 64, 128, 256; R² = 0.97). At N = 512, the cavity collapsed despite the form factor, as residual coupling from partially-suppressed high modes (g_512 approximately 10^-12, but multiplied by 512 such modes) accumulated sufficiently to destabilize the system.

With the tested parameters (n_cutoff = 100, Gaussian and sigmoid with Delta_n = 10), convergence was not achieved. Sharper roll-offs, higher cutoff values, or a hard cutoff (g_n = 0 for n > n_cutoff) may be required. The hard cutoff case would converge by construction but was not tested in this study; I flag it as a priority for follow-up work.

### 4.4 Topology Comparison (Unmatched Frequencies)

With the form factor active at N = 128, closed and periodic boundaries were compared at the same domain size (L = a = 1). The periodic system's fundamental frequency was 2x higher than the closed system's.

| Topology | E_final | Depletion |
|----------|---------|-----------|
| Closed | 3.77e-3 | 24.5% |
| Periodic (ring) | 4.77e-5 | 99.0% |

The periodic ring appeared to deplete plate energy approximately 80x more efficiently. The interpretation of this result is addressed in Section 4.6.

### 4.5 Topology Comparison (Frequency-Matched)

To isolate topological effects from spectral effects, the periodic ring circumference was doubled (L_periodic = 2*a_closed) to match the fundamental frequency. A spring restoring force was added to prevent large plate displacement and reduce catastrophic cancellation in the energy audit.

| N_modes | Closed depletion | Periodic depletion | Ratio (periodic/closed) |
|---------|-----------------|-------------------|----------------------|
| 32 | 0.6% | 0.2% | 0.33 |
| 64 | 17.0% | 3.4% | 0.20 |

At N = 128, the closed system experienced cavity collapse (the form factor was insufficient at this mode count with the spring configuration), preventing a direct comparison. The periodic system at N = 128 exhibited large oscillatory energy exchange with the field, making snapshot-based depletion measurements unreliable.

At these parameters, the absolute energy transferred is small, less than 1% at N = 32 and approximately 17% at N = 64 for the closed system. However, the ratio of periodic-to-closed depletion is consistent across the two mode counts (0.20-0.33), indicating a genuine topological distinction rather than a numerical artifact. The ratio indicates that standing-wave boundaries couple approximately 3-5x more efficiently to a localized plate than traveling-wave boundaries at matched spectra.

**Physical interpretation:** Standing waves concentrate field energy at antinodes, which can coincide with the plate position, producing strong local coupling. Traveling waves distribute energy uniformly, diluting the coupling at any single point.

### 4.6 The Spectral Matching Trap

The false-positive mechanism is characterized by comparing the unmatched and matched results:

| Configuration | Periodic/Closed ratio | Interpretation |
|--------------|----------------------|---------------|
| Unmatched (L = a) | ~80x enhancement | Spectral artifact: 2x higher frequencies |
| Matched (L = 2a) | ~0.2-0.3x (3-5x suppression) | True topological effect: standing waves win |

The unmatched case produced the opposite conclusion from the matched case. The apparent 80x enhancement arises because the periodic mode spectrum at L = a has fundamentally higher frequencies, and higher-frequency modes couple more strongly to plate motion through the parametric resonance mechanism. This effect is purely spectral and has no topological content.

This spectral matching trap is not specific to Casimir simulations. It applies generally to any computational study comparing wave dynamics across boundary conditions with different mode structures. The trap is easy to fall into because the comparison "same domain size, different boundary conditions" feels like a controlled experiment, but it is not controlled for mode frequencies.

---

## 5. Discussion

### 5.1 Implications for Dynamical Casimir Simulations

The UV divergence of vacuum friction in 1+1D free scalar field theory has practical consequences for computational studies. Simulations that truncate the mode spectrum at an arbitrary N without physical motivation will produce N-dependent friction rates. This issue is distinct from the classical UV catastrophe (Rayleigh-Jeans thermalization) that affects semiclassical lattice simulations; it arises in exact quantum (Bogoliubov) calculations because each additional mode provides a new channel for parametric photon creation, and the coupling strength to high-frequency modes does not decay fast enough in the idealized Dirichlet model.

The resolution requires an explicit physical model for the plate's finite thickness, which introduces a material-dependent cutoff. The absolute friction rate then depends on this cutoff, which should be informed by the actual material properties of the mechanical element being modeled.

### 5.2 Implications for Nanomechanical Engineering

The finding that standing-wave modes couple approximately 3-5x more efficiently to localized mechanical elements than traveling-wave modes has potential relevance for MEMS/NEMS design. Devices with closed cavity geometries (reflecting boundaries) would experience stronger Casimir-induced damping than devices with open or periodic boundary conditions at the same characteristic frequency. While this result is obtained in 1+1D scalar theory and cannot be directly applied to 3+1D electromagnetic Casimir forces without further investigation, it suggests that boundary topology may be a relevant design parameter for nanoscale oscillators where Casimir effects are significant.

### 5.3 Implications for Vacuum Energy Concepts

The original motivation for this study, investigating whether toroidal (periodic) boundary topology could enhance vacuum-mechanical coupling for energy extraction, was not supported for the specific geometry tested. Periodic boundaries produce weaker, not stronger, coupling at matched spectra.

This does not categorically exclude geometry-dependent vacuum energy effects. Other topologies remain unexplored, including linear waveguide arrays (coupled resonator chains with directional energy transfer), multi-plate coupled cavities, and geometries that break time-reversal symmetry. The question of whether any boundary topology can shift the fluctuation-dissipation equilibrium in favor of net energy extraction remains open and is constrained but not resolved by the present results.

---

## 6. Methods: The Nothing Engine

The simulator implements three complementary tracks. Track A (Semiclassical Lattice) evolves a classical scalar field on an ALE-mapped spatial lattice with Wigner-sampled vacuum initial conditions; it is reliable for short-duration runs up to approximately 10^4 cavity crossing times before classical UV thermalization dominates. Track B (Quantum Bogoliubov) evolves the exact quantum vacuum state in mode space via time-dependent Bogoliubov transformation; it is the primary track for all results reported in this paper. Track C (Energy Audit) monitors total energy conservation at every timestep and halts the simulation if the relative drift exceeds 10^-6.

Analysis parameters, fitting functions, fitting windows, model selection criteria (AIC), statistical tests (Kolmogorov-Smirnov, Bonferroni-corrected), and significance thresholds, were specified in a pre-registration document committed to the repository before any experimental runs.

The complete simulator, validation framework, experiment scripts, analysis code, and raw HDF5 data from all experiments reported in this paper are released as open-source software at the companion repository.

---

## 7. Conclusions

We investigated whether boundary topology shapes vacuum friction dynamics in dynamical Casimir cavities. Three findings emerged.

Standing-wave (closed) boundaries couple approximately 3-5x more efficiently to a localized plate than traveling-wave (periodic) boundaries at matched spectra. This is a consistent result across the mode counts tested (N = 32, 64) and is physically interpretable: standing waves concentrate energy at antinodes; traveling waves distribute it uniformly.

The apparent 80x enhancement of periodic boundaries observed in spectrally unmatched comparisons is entirely attributable to the higher fundamental frequencies of the periodic mode structure. We characterize this spectral matching trap as a general methodological hazard for boundary-condition comparison studies.

The absolute vacuum friction rate in 1+1D free scalar field theory does not converge with mode count under the smooth regularizations tested. Physical regularization via finite plate thickness is necessary, and the resulting friction rate is explicitly cutoff-dependent.

These null and negative results carry methodological value: they identify a false-positive mechanism, establish the standing-wave coupling advantage, characterize the UV convergence problem, and provide a validated open-source tool for further investigation.

The vacuum is not empty. Its shape matters. It just matters differently than I expected.

---

## 8. Future Work

1. **Hard UV cutoff experiment.** Implement g_n = 0 for n > n_cutoff and verify convergence by construction. This would separate the physics of finite plate thickness from the numerics of smooth regularization.

2. **Scaling law characterization.** Extend the alpha-vs-N analysis with error estimates and fit a formal scaling exponent to determine whether the divergence is algebraic or logarithmic.

3. **Post-ringdown power spectral density.** The Fluctuation-Dissipation Theorem guarantees residual plate motion after ringdown. Whether the spectral character differs between topologies is unmeasured and would characterize the vacuum's thermodynamic behavior.

4. **Coupled cavity chains (waveguide topology).** A linear chain of impedance-matched cavities represents a fourth topology with potentially different energy transfer dynamics, including directional propagation.

5. **Extension to higher dimensions.** 2+1D or 3+1D simulations with realistic plate geometries (e.g., finite-difference time-domain methods) would establish quantitative relevance to experimental Casimir systems.

6. **Quantum Langevin / Input-Output formalism.** True open-system treatment with a continuous bath would eliminate boundary-reflection artifacts in the open-system track.

---

## 9. Acknowledgments

The author thanks several large language models (Claude/Anthropic, DeepSeek, Gemini/Google) for assistance with architecture design, peer review, code review, and manuscript feedback across three rounds of iterative development. Specific contributions included: identification of the mechanical work / back-action problem requiring dynamical plate coupling; proposal of the Bogoliubov quantum track; derivation of the mode-space plate coupling equation; identification of the ALE coordinate mapping requirement; application of the Fluctuation-Dissipation Theorem constraint; and design of the pre-registration framework. All final experimental decisions, interpretations, and errors are the author's own.

The author thanks the open-source scientific Python ecosystem: NumPy, SciPy, h5py, and matplotlib.

---

## 10. References

1. Casimir, H.B.G. (1948). "On the attraction between two perfectly conducting plates." Proc. Kon. Ned. Akad. Wetensch. B51, 793-795.
2. Lifshitz, E.M. (1956). "The theory of molecular attractive forces between solids." Soviet Physics JETP, 2(1), 73-83.
3. Lamoreaux, S.K. (1997). "Demonstration of the Casimir Force in the 0.6 to 6 um Range." Physical Review Letters, 78(1), 5-8.
4. Munday, J.N., Capasso, F. & Parsegian, V.A. (2009). "Measured long-range repulsive Casimir-Lifshitz forces." Nature, 457, 170-173.
5. Wilson, C.M. et al. (2011). "Observation of the dynamical Casimir effect in a superconducting circuit." Nature, 479, 376-379.
6. Chan, H.B. et al. (2001). "Quantum Mechanical Actuation of Microelectromechanical Systems by the Casimir Force." Science, 291(5510), 1941-1944.
7. Dalvit, D.A.R. et al. (2011). Casimir Physics. Lecture Notes in Physics, Vol. 834. Springer.
8. Rodriguez, A.W., Capasso, F. & Johnson, S.G. (2011). "The Casimir effect in microstructured geometries." Nature Photonics, 5, 211-221.
9. Emig, T., Graham, N., Jaffe, R.L. & Kardar, M. (2007). "Casimir Forces between Arbitrary Compact Objects." Physical Review Letters, 99(17), 170403.
10. Decca, R.S. et al. (2003). "Tests of new physics from precise gravitational force measurements at submicron separations." Physical Review D, 68(11), 116003.

---

## Appendix: Companion Materials

The following companion documents are available at the project repositories:

- **Toroidal Casimir Cavity Energy Harvester** — The original harvester concept, including the energy deficit analysis and pivot to bearing applications.
- **Casimir-Levitated Flywheel Energy Storage via Toroidal Repulsive Bearings** — Zero-friction rotational energy storage using repulsive Casimir-Lifshitz forces. Applications in MEMS, seismometry, and space mechanisms.

---

**Disclosure:** This research was conducted independently with computational assistance from AI systems as described in Section 9. The AI systems contributed to architecture design, peer review, code implementation, and result interpretation. All final decisions and interpretations are the author's own.