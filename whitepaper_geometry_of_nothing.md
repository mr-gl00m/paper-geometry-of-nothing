# Geometry of Nothing: Spectral Matching, UV Divergence, and the Standing-Wave Advantage in Dynamical Casimir Simulations

**Author:** Cid (Independent Researcher)

**Date:** April 3, 2026

**Repository:** github.com/mr-gl00m/paper-geometry-of-nothing

**Companion Software:** Project Halo v2: open-source 1+1D quantum field theory simulator with dynamical plate coupling and multi-topology boundary support

**Status:** Preprint / Working Draft, **with 2026-04-16 Corrigendum (see notice below and Addendum at end).**

---

> **⚠ CORRIGENDUM NOTICE (2026-04-16).** After submission of this r2 draft, a normal-ordering bug (internally tracked as RT-009) was identified in the plate force right-hand side: the Casimir counter-term was subtracted as a single global constant rather than per mode, leaving an O(N³) UV remnant on the force. Reruns with the fix **retract Finding 1** ("standing waves couple 3–5× more than traveling waves at matched spectra") and **retract the quantitative form of Finding 3** ("vacuum friction UV-divergent as ~N^0.7 toward N³"). **Finding 2** (the spectral-matching trap) is unaffected. The main body below is preserved as it appeared at r2 submission. For the corrected per-finding status, new convergence and topology data, and the revised claim the paper is entitled to make, read the **[Addendum / Corrigendum](#addendum--corrigendum-2026-04-16)** at the end of this document before citing any quantitative claim from Sections 4–7.

---

## Abstract

*[Abstract as submitted at r2; see Corrigendum notice above; Findings 1 and 3 are retracted.]*

We present a computational investigation of vacuum friction in 1+1D dynamical Casimir cavities with closed, open, and periodic boundaries, testing the hypothesis that periodic (toroidal) topology enhances vacuum-mechanical coupling. Using a quantum field simulator validated against five analytical benchmarks (static Casimir energy to machine precision, dynamical photon production to 0.02%, energy conservation to 5e-12), we report three principal findings. First, naive topology comparisons produce dramatic apparent differences (99% energy depletion for periodic vs 24.5% for closed boundaries) that vanish under spectral matching; the false-positive mechanism is characterized as a "spectral matching trap" arising from a 2x frequency ratio between periodic and closed mode spectra at equal domain size. Second, after matching mode frequencies, standing-wave (closed) boundaries couple approximately 3-5x more efficiently to a moving plate than traveling-wave (periodic) boundaries (the opposite of the initial hypothesis) because standing waves concentrate field energy at the boundary while traveling waves distribute it uniformly. Third, the vacuum friction rate in 1+1D free scalar theory is UV-divergent with an approximately cubic scaling in mode count (sum of n² per-mode contributions), and the smooth form-factor regularizations tested (Gaussian and sigmoid) do not achieve convergence within the parameter range explored. Physical regularization via finite plate thickness is required, making absolute friction rates cutoff-dependent. These results constrain claims of topology-enhanced vacuum energy extraction and provide a validated open-source simulator for further study.

---

## 1. Introduction

### 1.1 Motivation

The Casimir effect, an attractive force between conducting surfaces arising from quantum vacuum fluctuations, was predicted by Casimir in 1948 and experimentally confirmed by Lamoreaux in 1997. The dynamic Casimir effect (DCE), predicted by Moore in 1970 and observed experimentally by Wilson et al. in 2011 using a superconducting circuit, demonstrates that the motion of a boundary converts virtual photons into real photon pairs. Together, these phenomena establish that the quantum vacuum is a physical medium capable of exerting measurable forces and exchanging real energy with mechanical systems.

A natural question follows: can boundary geometry be engineered to control the character of vacuum-mechanical coupling? Specifically, does the topology of the cavity (whether photons are trapped (closed boundaries), allowed to escape (open boundaries), or circulate in a loop (periodic boundaries)) alter the rate, functional form, or spectral character of vacuum friction on a moving plate?

This question has practical relevance for nanomechanical engineering, where Casimir forces create stiction and anomalous damping in MEMS/NEMS devices at sub-100nm scales, and for quantum computing, where vacuum coupling contributes to qubit decoherence.

### 1.2 Hypothesis

This study was originally motivated by the following hypothesis:

**H₁ (Topology Enhancement):** Periodic (toroidal) boundary conditions, by supporting traveling-wave modes that collectively couple to a moving plate, will produce a higher vacuum friction rate than closed (Dirichlet) boundary conditions at matched spectral content.

**H₀ (Null):** After controlling for spectral content, boundary topology does not enhance vacuum-mechanical coupling; any observed differences between periodic and closed boundaries are attributable to unmatched mode frequencies.

The hypothesis H₁ was not confirmed. The data support a result opposite to H₁: standing-wave (closed) boundaries couple more efficiently than traveling-wave (periodic) boundaries. We report these null and negative results in full, as they carry methodological value independent of the original motivation.

> *Corrigendum (2026-04-16):* The statement that "standing-wave (closed) boundaries couple more efficiently than traveling-wave (periodic) boundaries" is **retracted**. Reruns on the bug-fixed engine show the closed/periodic depletion ratio flips sign between N = 64 and N = 128 at matched spectra (0.08 → 0.40 → 8.73); no stable topological advantage survives. H₁ is therefore neither confirmed nor rejected by the corrected data. See the Addendum at the end of this document.

### 1.3 Background

The dynamical Casimir effect has been studied extensively since Moore's foundational analysis (1970), with comprehensive reviews by Dodonov (2010, 2020). Computational approaches include lattice field theory, Bogoliubov transformation methods, and semiclassical approximations. Most computational work has focused on validating analytical predictions for photon production rates in short-duration simulations with prescribed (externally driven) plate motion. Long-duration simulations with dynamical plate coupling, where the plate is a free mechanical element exchanging energy with the field via radiation pressure, are largely absent from the literature. The question of whether vacuum friction converges with respect to mode count, or whether the friction rate depends on boundary topology, has not been systematically investigated.

### 1.4 Approach

We constructed a 1+1D quantum field theory simulator (Project Halo v2) implementing two complementary simulation tracks: a semiclassical lattice simulation (Track A) with Arbitrary Lagrangian-Eulerian coordinate mapping for moving boundaries, and a quantum Bogoliubov mode-space simulation (Track B) with exact vacuum state initialization and direct particle counting. Both tracks couple the field to a dynamical plate with finite mass, spring restoring force, and initial kinetic energy, with the plate equation of motion derived from the total system Hamiltonian. A continuous energy audit (Track C) monitors conservation at every timestep.

The simulator was validated against five analytical benchmarks before any experimental runs (Section 3). All gates passed within specified tolerances. Analysis parameters were pre-registered before data collection.

### 1.5 Scope and Limitations

This study uses a massless scalar field in 1+1 dimensions, a standard simplification in Casimir physics that captures vacuum fluctuation dynamics without electromagnetic polarization complexity. Results are qualitative; quantitative force magnitudes and damping rates would require extension to 3+1D electromagnetic fields with realistic material models. The Bogoliubov approach is exact for free fields but does not capture nonlinear interactions. All energies are reported in natural units (hbar = c = 1); for a cavity width a = 100 nm, the unit energy hbar*c/a corresponds to approximately 2 eV (see Section 2.1 for the dimensional analysis).

---

## 2. Theoretical Framework

### 2.1 Field Theory

We consider a massless scalar field phi(x, t) in 1+1 dimensions governed by the Klein-Gordon equation in natural units (hbar = c = 1):

    (d²/dt² - d²/dx²) phi(x, t) = 0

The field is confined between a fixed left plate at position x_L = 0 and a dynamical right plate at position q(t), with Dirichlet boundary conditions at both plates:

    phi(0, t) = phi(q(t), t) = 0

For an instantaneous cavity width a(t) = q(t) - x_L, the mode expansion of the field in the instantaneous basis is:

    phi(x, t) = sum_n [ f_n(t) * sqrt(2/a(t)) * sin(n*pi*x/a(t)) ]

where f_n(t) are time-dependent mode amplitudes satisfying coupled ODEs derived from the Klein-Gordon equation with moving boundaries. The instantaneous mode frequencies are:

    omega_n(a) = n*pi/a

**Dimensional analysis.** In natural units with a = 1, the energy unit is hbar*c/a. For a physical cavity width a = 100 nm: E_unit = (1.055e-34 * 3e8) / (100e-9) = 3.16e-19 J approximately 2 eV. Reported energies should be interpreted at this scale.

### 2.2 Dynamical Plate Coupling

The right plate is a dynamical variable with mass M, position q(t), velocity v(t) = dq/dt, and an optional restoring spring with constant k. The plate's equation of motion is derived from the total system Hamiltonian expressed in the instantaneous mode basis.

**Bogoliubov mode ODEs.** The quantum state of each field mode n is described by mode functions f_n(t) = u_n(t) + i*v_n(t), where u_n and v_n are real. The time evolution of the mode functions under a moving boundary is:

    du_n/dt = omega_n(a) * v_n
    dv_n/dt = -omega_n(a) * u_n

with omega_n(a) = n*pi/a(t). The particle number (photons created from vacuum) in mode n is:

    N_n(t) = (1/2)(omega_n * |f_n|² + |df_n/dt|²/omega_n) - 1/2

This is the Bogoliubov particle count: N_n = |beta_n|² where beta_n is the standard Bogoliubov coefficient. The mode functions are initialized to the vacuum state: u_n(0) = 1/sqrt(2*omega_n(0)), v_n(0) = 0.

**Plate equation of motion.** The radiation pressure force on the plate is derived from the mode-space Hamiltonian:

    M d²q/dt² = -k*(q - q_eq) + sum_n [ (n²*pi²/a³) * g_n² * (|f_n|² - 1/(2*omega_n)) ]

The first term in the sum gives the back-action from dynamically created photons; the subtracted 1/(2*omega_n) removes the zero-point contribution, which is handled separately as the static Casimir force. The static Casimir force is:

    F_Casimir = -pi/(24*a²)     [1+1D scalar Dirichlet]

This is verified against analytical predictions as part of the validation suite (Section 3.1). The form factor g_n (Section 2.4) multiplies the coupling of each mode.

**Energy conservation.** The total energy of the coupled system is:

    E_total = (1/2)*M*v² + (1/2)*k*(q - q_eq)² + sum_n omega_n(a) * (|f_n|² * omega_n + |df_n/dt|²/omega_n) / 2

This quantity is conserved by the equations of motion (verified to relative precision < 5e-12 in the validation suite). The energy audit (Track C) monitors E_total at every timestep and halts if relative drift exceeds 10^-6.

**Integration.** The full state vector has 4*N_modes + 2 real components (two real parts per mode function plus plate position and velocity). The system is integrated via adaptive Runge-Kutta (RK45) with tight tolerances (rtol = 10^-13, atol = 10^-15).

### 2.3 Boundary Conditions

Three boundary topologies were investigated:

**Closed (Dirichlet):** Reflecting walls confine the field. Mode frequencies are omega_n = n*pi/a, producing standing waves with nodes at the boundaries. Photons created by plate motion remain trapped and can re-interact with the plate.

**Open (large-box approximation):** The cavity is embedded in a much larger domain with Dirichlet boundaries at distance L >> a. In mode space, the external modes provide channels for photon escape. This is an approximation to a true open system; a rigorous treatment would require Quantum Langevin or Input-Output formalism.

**Periodic (ring):** The field satisfies periodic boundary conditions on a domain of circumference L, producing traveling-wave modes with frequencies omega_n = 2*n*pi/L. This topology models a toroidal cavity array where photons propagate around a closed ring.

### 2.4 UV Regularization

In a 1+1D cavity, the number of available field modes is formally infinite. In practice, the spectrum is truncated at N_modes. Each mode provides an additional channel for parametric photon creation, and the total friction rate depends on the sum over all coupled modes.

To model finite plate thickness delta, we introduce a form factor g_n that suppresses coupling to modes with wavelength shorter than delta. Two functional forms were tested within this study: a Gaussian form, g_n = exp(-(n/n_cutoff)²), and a sigmoid form, g_n = 1/(1 + exp((n - n_cutoff)/Delta_n)), with n_cutoff = a/delta and Delta_n = n_cutoff/10.

A hard cutoff (g_n = 0 for n > n_cutoff) would achieve convergence by construction, as adding modes beyond the cutoff cannot change the result. The smooth forms tested here are more physical in that they model a gradual transition rather than an abrupt one, but as shown in Section 4.3, they do not achieve convergence within the parameter range explored. Whether sharper roll-offs or higher cutoff values would eventually converge, or whether only a hard cutoff suffices, remains an open question that we flag for future investigation.

### 2.5 Spectral Matching

A critical methodological point: different boundary topologies produce different mode spectra at the same domain size.

**The frequency mismatch.** For a closed cavity of width a, the mode frequencies are:

    omega_n^closed = n*pi/a,       n = 1, 2, 3, ...

For a periodic ring of circumference L, the mode frequencies are:

    omega_n^periodic = 2*n*pi/L,   n = 1, 2, 3, ...

At L = a (same domain size), the periodic fundamental is omega_1^periodic = 2*pi/a = 2*omega_1^closed, exactly twice as high. The entire periodic spectrum is shifted upward by a factor of 2.

**Why this matters quantitatively.** The parametric photon creation rate in mode n scales as the square of the coupling parameter (Dodonov 2010):

    Gamma_n ~ (v_plate * omega_n / c)²

where v_plate is the plate velocity. For a mode at frequency 2*omega, the creation rate is 4x that of a mode at frequency omega. Summed over N modes, the periodic system at L = a sees effective coupling approximately 4x stronger per mode, and the cumulative effect over many modes compounds to produce the observed ~80x apparent enhancement (Section 4.4). This enhancement is entirely spectral, not topological.

**The matching condition.** To isolate topology from spectrum, frequency-matched comparisons require:

    L_periodic = 2 * a_closed

so that omega_1^periodic = 2*pi/(2a) = pi/a = omega_1^closed. With this matching, both topologies share the same fundamental frequency and the same mode density, isolating the effect of wave character (standing vs. traveling) from spectral content.

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

This initially suggested non-Markovian friction dynamics with a long-memory power-law tail. Subsequent convergence testing (Section 4.2) revealed that this result is an artifact of mode-count truncation, not a converged physical prediction. We include it as a documented example of how truncation artifacts can produce physically plausible but incorrect results.

### 4.2 Mode Convergence (No Form Factor)

The light-plate experiment was repeated at N = 32, 64, 128, and 256 modes.

| N_modes | alpha | E_final | % remaining | Behavior |
|---------|-------|---------|-------------|----------|
| 32 | 0.01 | 4.92e-3 | 98.4% | sub-percent depletion; alpha indistinguishable from noise |
| 64 | 0.02 | 4.46e-3 | 89.2% | measurable depletion; alpha still near zero |
| 128 | 0.53 | 1.05e-3 | 21.0% | power-law decay dominates; qualitative regime change |
| 256 | n/a | 1.62e+0 | n/a | cavity collapse: static Casimir attraction exceeds plate KE |

The friction rate did not converge. Each doubling of mode count produced qualitatively different behavior, culminating in cavity collapse at N = 256 when the cumulative static Casimir attraction (which deepens with mode count) overwhelmed the plate's kinetic energy. The alpha = 0.53 result at N = 128 is cutoff-dependent.

### 4.3 Mode Convergence (With Form Factor)

> *Corrigendum (2026-04-16):* The numbers in this subsection (notably the 75.5% / 62.2% / "collapse at 512" progression and the derived α ∝ N^0.7 scaling) are **retracted**. They were produced under the force-RHS normal-ordering bug described in the Addendum. Reruns with the corrected engine show no cavity collapse at any N ≤ 256 and no fit-able decay envelope at matched parameters.

A Gaussian form factor with n_cutoff = 100 was introduced and the convergence sweep repeated. A sigmoid form factor was also tested and produced qualitatively similar results; we report the Gaussian data.

| N_modes | alpha | E_final | % remaining |
|---------|-------|---------|-------------|
| 16 | 0.52* | 4.99e-3 | 99.8% |
| 32 | 0.01 | 4.93e-3 | 98.5% |
| 64 | 0.02 | 4.61e-3 | 92.2% |
| 128 | 0.06 | 3.77e-3 | 75.5% |
| 256 | 0.12 | 3.11e-3 | 62.2% |
| 512 | n/a | n/a | n/a (collapsed) |

*The N = 16 alpha value is spurious due to severe mode under-sampling; the fit captures noise rather than a physical trend. The meaningful progression begins at N = 32.

The form factor prevented cavity collapse at N = 256 but alpha continued to grow: from the N = 32 through N = 256 range, the approximate scaling is alpha proportional to N^0.7 (fit to log-alpha vs log-N for N = 32, 64, 128, 256; R² = 0.97). At N = 512, the cavity collapsed despite the form factor, as residual coupling from partially-suppressed high modes (g_512 approximately 10^-12, but multiplied by 512 such modes) accumulated sufficiently to destabilize the system.

With the tested parameters (n_cutoff = 100, Gaussian and sigmoid with Delta_n = 10), convergence was not achieved. Sharper roll-offs, higher cutoff values, or a hard cutoff (g_n = 0 for n > n_cutoff) may be required. The hard cutoff case would converge by construction but was not tested in this study; we flag it as a priority for follow-up work.

### 4.4 Topology Comparison (Unmatched Frequencies)

With the form factor active at N = 128, closed and periodic boundaries were compared at the same domain size (L = a = 1). The periodic system's fundamental frequency was 2x higher than the closed system's.

| Topology | E_final | Depletion |
|----------|---------|-----------|
| Closed | 3.77e-3 | 24.5% |
| Periodic (ring) | 4.77e-5 | 99.0% |

The periodic ring appeared to deplete plate energy approximately 80x more efficiently. The interpretation of this result is addressed in Section 4.6.

### 4.5 Topology Comparison (Frequency-Matched)

> *Corrigendum (2026-04-16):* The quantitative conclusion of this subsection, "standing-wave boundaries couple approximately 3-5x more efficiently to a localized plate than traveling-wave boundaries at matched spectra", is **retracted**. Reruns on the bug-fixed engine at N = 32, 64, 128 give periodic/closed depletion ratios of 0.08, 0.40, 8.73 respectively; the sign of the advantage flips between N = 64 and N = 128. The end-of-run depletion observable is also not robust for the spring-restored plate because E_plate(t) oscillates. See the Addendum for the corrected numbers and observable critique.

To isolate topological effects from spectral effects, the periodic ring circumference was doubled (L_periodic = 2*a_closed) to match the fundamental frequency. A spring restoring force was added to prevent large plate displacement and reduce catastrophic cancellation in the energy audit.

| N_modes | Closed depletion | Periodic depletion | Ratio (periodic/closed) |
|---------|-----------------|-------------------|----------------------|
| 32 | 0.6% | 0.2% | 0.33 |
| 64 | 17.0% | 3.4% | 0.20 |

At N = 128, the closed system experienced cavity collapse (the form factor was insufficient at this mode count with the spring configuration), preventing a direct comparison. The periodic system at N = 128 exhibited large oscillatory energy exchange with the field, making snapshot-based depletion measurements unreliable.

At these parameters, the absolute energy transferred is small, less than 1% at N = 32 and approximately 17% at N = 64 for the closed system. However, the ratio of periodic-to-closed depletion is consistent across the two mode counts (0.20-0.33), indicating a genuine topological distinction rather than a numerical artifact. The ratio indicates that standing-wave boundaries couple approximately 3-5x more efficiently to a localized plate than traveling-wave boundaries at matched spectra.

**Uncertainty and limitations of the ratio estimate.** The 3-5x coupling advantage is derived from only two mode-count data points (N = 32, 64). The ratio shifts from 0.33 to 0.20 between these points, suggesting a possible N-dependence that cannot be resolved without additional data. Furthermore, the absolute depletions are small (0.2-17%), placing the measurement in a regime where numerical precision of the integrator matters. The energy conservation audit confirms relative drift below 5e-12 throughout these runs, ruling out integration error as a source. However, we cannot exclude the possibility that the ratio changes at higher N; the cavity collapse at N = 128 (closed) prevented verification. We report the ratio as approximately 5x (geometric mean of 1/0.33 = 3.0 and 1/0.20 = 5.0, giving approximately 3.9x) with the caveat that this is established at low mode counts only.

**Physical interpretation.** Standing waves concentrate field energy at antinodes, which can coincide with the plate position, producing strong local coupling. For a closed cavity, the nth mode has field amplitude proportional to sin(n*pi*x/a); at the plate position x = a, the spatial derivative (which determines radiation pressure) is proportional to n*pi*cos(n*pi) = +/-n*pi, and every mode couples maximally at the boundary. Traveling waves distribute energy uniformly around the ring, so the field amplitude at the plate position is 1/sqrt(L) regardless of the plate's location, so the coupling per mode is diluted by the ratio a/L.

### 4.6 The Spectral Matching Trap

> *Corrigendum note (2026-04-16):* The qualitative argument of this subsection, that naive same-domain comparisons across topologies are a methodological trap because mode spectra differ, is **unaffected** by the RT-009 bug fix and stands as Finding 2 of the paper. Only the claim in the table that frequency-matching reverses the apparent advantage to "3-5× suppression" changes: with the fix, the matched-case ratio is strongly N-dependent and does not support the "3-5× suppression" row. Read the table below as documenting the r2 state; see the Addendum for the corrected matched-case picture.

The false-positive mechanism is characterized by comparing the unmatched and matched results:

| Configuration | Periodic/Closed ratio | Interpretation |
|--------------|----------------------|---------------|
| Unmatched (L = a) | ~80x enhancement | Spectral artifact: 2x higher frequencies |
| Matched (L = 2a) | ~0.2-0.3x (3-5x suppression) | True topological effect: standing waves win |

The unmatched case produced the opposite conclusion from the matched case. The apparent 80x enhancement arises because the periodic mode spectrum at L = a has fundamentally higher frequencies, and higher-frequency modes couple more strongly to plate motion through the parametric resonance mechanism. As derived in Section 2.5, the per-mode coupling scales as omega_n², so a 2x frequency shift produces a 4x coupling increase per mode. The cumulative effect over N modes, combined with the different mode density and the nonlinear dynamics of the coupled system, produces the observed ~80x apparent enhancement, consistent in order of magnitude with the 4x-per-mode scaling.

This effect is purely spectral and has no topological content. The reversal from 80x enhancement to 3-5x suppression upon frequency matching demonstrates that the spectral contribution dominates the topological contribution by more than an order of magnitude.

**Generality of the trap.** This spectral matching trap is not specific to Casimir simulations. It applies to any computational study comparing wave dynamics across boundary conditions with different mode structures, including: acoustic resonator comparisons across open/closed pipes, electromagnetic cavity simulations with different boundary terminations, and condensed matter calculations comparing periodic vs. hard-wall potentials. The trap is easy to fall into because the comparison "same domain size, different boundary conditions" feels like a controlled experiment, but it is not controlled for mode frequencies. We recommend that any future boundary-condition comparison study explicitly verify spectral matching before drawing topological conclusions.

---

## 5. Discussion

### 5.1 Implications for Dynamical Casimir Simulations

The UV divergence of vacuum friction in 1+1D free scalar field theory has practical consequences for computational studies. Simulations that truncate the mode spectrum at an arbitrary N without physical motivation will produce N-dependent friction rates.

**Mechanism of the divergence.** In the instantaneous Dirichlet model, the coupling of mode n to plate motion enters through the frequency derivative d(omega_n)/dq = -n*pi/a², which grows linearly with n. The parametric photon creation rate per mode scales as Gamma_n ~ (v * n * pi / a²)², giving a per-mode contribution to friction that grows as n². Summing over N modes:

    F_friction ~ sum_{n=1}^{N} n² ~ N³/3

This cubic scaling is faster than the suppression provided by smooth form factors with fixed cutoff scale, explaining why the Gaussian and sigmoid regularizations tested in Section 4.3 do not achieve convergence: the partially-suppressed tails of the form factor still contribute a divergent sum.

This issue is distinct from the classical UV catastrophe (Rayleigh-Jeans thermalization) that affects semiclassical lattice simulations; it arises in exact quantum (Bogoliubov) calculations because each additional mode provides a new channel for parametric photon creation, and the coupling strength to high-frequency modes does not decay fast enough in the idealized Dirichlet model.

The resolution requires an explicit physical model for the plate's finite thickness, which introduces a material-dependent cutoff. The absolute friction rate then depends on this cutoff, which should be informed by the actual material properties of the mechanical element being modeled. This parallels the situation in quantum electrodynamics, where the electron self-energy diverges logarithmically and is rendered finite only by renormalization; here, the "renormalization" is physical rather than formal, provided by the plate's finite spatial extent.

### 5.2 Implications for Nanomechanical Engineering

The finding that standing-wave modes couple approximately 3-5x more efficiently to localized mechanical elements than traveling-wave modes has potential relevance for MEMS/NEMS design. Devices with closed cavity geometries (reflecting boundaries) would experience stronger Casimir-induced damping than devices with open or periodic boundary conditions at the same characteristic frequency. While this result is obtained in 1+1D scalar theory and cannot be directly applied to 3+1D electromagnetic Casimir forces without further investigation, it suggests that boundary topology may be a relevant design parameter for nanoscale oscillators where Casimir effects are significant.

### 5.3 Implications for Vacuum Energy Concepts

The original motivation for this study, investigating whether toroidal (periodic) boundary topology could enhance vacuum-mechanical coupling for energy extraction, was not supported for the specific geometry tested. Periodic boundaries produce weaker, not stronger, coupling at matched spectra.

This does not categorically exclude geometry-dependent vacuum energy effects. Other topologies remain unexplored, including linear waveguide arrays (coupled resonator chains with directional energy transfer), multi-plate coupled cavities, and geometries that break time-reversal symmetry. The question of whether any boundary topology can shift the fluctuation-dissipation equilibrium in favor of net energy extraction remains open and is constrained but not resolved by the present results.

---

## 6. Methods: Project Halo v2

The simulator implements three complementary tracks. Track A (Semiclassical Lattice) evolves a classical scalar field on an ALE-mapped spatial lattice with Wigner-sampled vacuum initial conditions; it is reliable for short-duration runs up to approximately 10^4 cavity crossing times before classical UV thermalization dominates. Track B (Quantum Bogoliubov) evolves the exact quantum vacuum state in mode space via time-dependent Bogoliubov transformation; it is the primary track for all results reported in this paper. Track C (Energy Audit) monitors total energy conservation at every timestep and halts the simulation if the relative drift exceeds 10^-6.

Analysis parameters (fitting functions, fitting windows, model selection criteria (AIC), statistical tests (Kolmogorov-Smirnov, Bonferroni-corrected), and significance thresholds) were specified in a pre-registration document committed to the repository before any experimental runs.

The complete simulator, validation framework, experiment scripts, analysis code, and raw HDF5 data from all experiments reported in this paper are released as open-source software at the companion repository.

---

## 7. Conclusions

> *Corrigendum (2026-04-16):* The conclusions below are preserved as submitted at r2. With the RT-009 fix, **H₁ is no longer rejected by this data**; the matched-spectra ratio flips sign with N, so neither "H₁" nor "opposite of H₁" is supported. **Finding 1 is retracted. Finding 2 stands. Finding 3 is retracted in its quantitative form (the ~N³ claim) and reduced to the qualitative observation that UV convergence of friction observables is not demonstrated by this study.** See the Addendum for the corrected per-finding status and for what the paper is still entitled to claim.

We tested the hypothesis (H₁) that periodic boundary topology enhances vacuum-mechanical coupling relative to closed boundaries. H₁ is rejected; the data support the opposite conclusion. Three findings emerged:

**Finding 1: Standing-wave coupling advantage.** Closed (Dirichlet) boundaries couple approximately 3-5x more efficiently to a localized plate than periodic boundaries at matched spectra (periodic/closed depletion ratio = 0.20-0.33, geometric mean approximately 0.26, measured at N = 32 and 64 modes). The physical mechanism is concentration of field energy at standing-wave antinodes versus uniform distribution in traveling waves.

**Finding 2: Spectral matching trap.** The apparent 80x enhancement of periodic boundaries observed in spectrally unmatched comparisons (L_periodic = a_closed) is entirely attributable to the 2x higher fundamental frequencies of the periodic mode structure. This spectral artifact reverses the apparent conclusion and constitutes a general methodological hazard for any boundary-condition comparison study involving wave phenomena.

**Finding 3: UV divergence of vacuum friction.** The absolute vacuum friction rate in 1+1D free scalar field theory does not converge with mode count under the smooth regularizations tested. The per-mode friction contribution scales as n², giving an aggregate scaling approximately proportional to N³. Physical regularization via finite plate thickness is necessary, and the resulting friction rate is explicitly cutoff-dependent.

These null and negative results carry methodological value: they identify a false-positive mechanism, establish the standing-wave coupling advantage, characterize the UV convergence problem, and provide a validated open-source tool for further investigation.

The vacuum is not empty. Its shape matters. It just matters differently than we expected.

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

The author thanks several large language models (Claude/Anthropic, DeepSeek, Gemini/Google, GPT/OpenAI) for assistance with architecture design, peer review, code review, and manuscript feedback across three rounds of iterative development. Specific contributions included: identification of the mechanical work / back-action problem requiring dynamical plate coupling; proposal of the Bogoliubov quantum track; derivation of the mode-space plate coupling equation; identification of the ALE coordinate mapping requirement; application of the Fluctuation-Dissipation Theorem constraint; and design of the pre-registration framework. All final experimental decisions, interpretations, and errors are the author's own.

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
9. Emig, T., Graham, N., Jaffe, R.L. & Kardar, M. (2006). "Casimir Forces between Arbitrary Compact Objects." Physical Review Letters, 99(17), 170403.
10. Decca, R.S. et al. (2003). "Tests of new physics from precise gravitational force measurements at submicron separations." Physical Review D, 68(11), 116003.
11. Moore, G.T. (1970). "Quantum theory of the electromagnetic field in a variable-length one-dimensional cavity." Journal of Mathematical Physics, 11(9), 2679-2691.
12. Dodonov, V.V. (2010). "Current status of the dynamical Casimir effect." Physica Scripta, 82(3), 038105.
13. Dodonov, V.V. (2020). "Fifty Years of the Dynamical Casimir Effect." Physics, 2(1), 67-104.
14. Law, C.K. (1994). "Effective Hamiltonian for the radiation in a cavity with a moving mirror and a time-varying dielectric medium." Physical Review A, 49(1), 433-437.
15. Lambrecht, A., Jaekel, M.-T. & Reynaud, S. (1996). "Motion Induced Radiation from a Vibrating Cavity." Physical Review Letters, 77(4), 615-618.
16. Ford, L.H. & Vilenkin, A. (1982). "Quantum radiation by moving mirrors." Physical Review D, 25(10), 2569-2575.

---

## Appendix: Companion Materials

The following companion documents are available at the project repositories:

- **Toroidal Casimir Cavity Energy Harvester: A Whitepaper.** The original harvester concept, including the energy deficit analysis and pivot to bearing applications.
- **Casimir-Levitated Flywheel Energy Storage via Toroidal Repulsive Bearings.** Zero-friction rotational energy storage using repulsive Casimir-Lifshitz forces. Applications in MEMS, seismometry, and space mechanisms.
- **Quality Assurance Protocol.** Detailed specification of all validation gates, conservation thresholds, and acceptance criteria.
- **Understanding the Friction of Nothing: A Primer.** Accessible introduction to the physics and methodology.

A complete project history, including the evolution from data center cooling discussions through toroidal harvester design to the present study, is available in the repository README.

---

**Disclosure:** This research was conducted independently with computational assistance from AI systems as described in Section 9. The AI systems contributed to architecture design, peer review, code implementation, and result interpretation. All final decisions and interpretations are the author's own.

---

## Addendum / Corrigendum (2026-04-16)

After submission of r2, a post-hoc audit of the force right-hand-side uncovered a normal-ordering bug in the plate equation of motion (tracked internally as RT-009). The corrected engine has been rerun on the halo-ring protocol at N ∈ {16, 32, 64, 128, 256} and on a *frequency-matched* closed-vs-periodic topology sweep at N ∈ {32, 64, 128}. This addendum summarizes what changed, what survives, and what does not.

### A.1 The bug

The force on the plate was computed as

F(t) = −∂E_vac/∂a(t)

with E_vac built from per-mode contributions, and a Casimir counter-term subtracted *after the sum* as a single global constant. Because the Casimir counter-term is itself the sum of per-mode zero-point energies, the correct per-mode cancellation between "mode n's dynamical energy" and "mode n's static vacuum energy" was being done *mode-by-mode in one term* and *globally in another*. For slowly-varying a(t) these two arrangements should agree, but in the fully coupled ODE they do not: the global subtraction allowed an O(N³) piece of the UV mode-sum to survive on the force right-hand side. The fix was to subtract the vacuum energy per mode, before summing. The r2 manuscript's UV-divergence claim (Finding 3: α ∝ N^0.7 toward N³ friction) and the quantitative closed-vs-periodic ratio (Finding 1: 3-5× closed advantage) are both downstream of this.

### A.2 Corrected convergence run

Same protocol as the original halo sweep (light plate M = 100, v₀ = 0.01, closed Dirichlet cavity a₀ = 1, T = 5000, smooth sigmoid form factor with plate thickness a₀/100), rerun at N ∈ {32, 64, 128, 256} with the per-mode subtraction:

| N   | a_min/a₀ | \|ΔE\|/scale      | E_plate(T)/E_plate(0) | Residual KE |
|-----|----------|-------------------|------------------------|-------------|
| 32  | 1.0000   | ~1e-8             | ~0.95                  | ~95%        |
| 64  | 1.0000   | ~1e-8             | ~0.94                  | ~94%        |
| 128 | 1.0000   | ~1e-8             | ~0.94                  | ~94%        |
| 256 | 1.0000   | ~1e-8             | ~0.91                  | ~91%        |

(Precise values are in `data/experiments/convergence_N*.h5`.) **The cavity no longer closes.** The r2 manuscript reported progressive cavity collapse with N (75.5% residual at N = 128, 62.2% at N = 256, trending toward 0). That collapse was an artifact of the bug. With the corrected force, friction is far smaller and the cavity remains open at all N tested.

Correspondingly, the power-law fits used to support α ∝ N^0.7 are no longer well-posed on these runs: the decay envelope is <10% over the full T = 5000, which is below the noise floor of the fitting procedure. The r2 claim that closed-system vacuum friction diverges with mode count is not supported by the corrected data. Whether a weaker, genuine divergence exists under a different protocol (harder drive, longer run, or UV-resolved cutoff scan without the form factor) is open.

### A.3 Corrected topology sweep

To test Finding 1 under the bug fix, the frequency-matched protocol (closed a₀ = 1.0 vs. periodic L = 2.0, so that ω₁ = π in both) was rerun at N = 32, 64, 128 with a spring-restored plate (k chosen to give T_osc = 100) under the corrected engine. End-of-run depletion:

| N   | closed depletion | periodic depletion | ratio (periodic/closed) |
|-----|------------------|--------------------|-------------------------|
| 32  | 23.4 %           | 1.8 %              | 0.08                    |
| 64  | 73.8 %           | 29.8 %             | 0.40                    |
| 128 | 10.7 %           | 93.0 %             | 8.73                    |

The ratio is not merely unconverged: **the sign of the advantage flips between N = 64 and N = 128.** Closed dominates by 13× at N = 32 and 2.5× at N = 64; periodic dominates by 8.7× at N = 128. Moreover, inspection of the E_plate(t) time series (segment logs in the experiment output) shows that under the spring restoring force the plate energy oscillates substantially rather than decaying monotonically: the N = 64 closed run, for instance, passes through E_plate = 1.5e-4 at t = 4000 and returns to 1.3e-3 at t = 5000. End-of-run depletion is a snapshot of an oscillation phase, not a robust dissipation observable in this regime.

The r2 manuscript's quantitative claim, "closed (Dirichlet) cavities dissipate 3-5× more energy per unit time than periodic (ring) topology at matched spectra", **does not survive this rerun.** The honest read of the corrected data is: at the parameters tested, the closed/periodic ratio is strongly N-dependent, non-monotone in N, and dominated at the final time by the oscillation phase of the restored plate rather than by an asymptotic dissipation rate. No stable topological advantage can be asserted from these three data points.

### A.4 Status of each finding

- **Finding 1 (closed > periodic by 3-5× at matched spectra):** Retracted. See A.3.
- **Finding 2 (spectral-matching methodological trap; unmatched spectra produce a spurious 80× periodic advantage that vanishes under frequency matching):** Unaffected. The comparison between the matched protocol and the naive a = L protocol does not depend on the force normal-ordering bug; the spectral-matching argument stands as a methodological warning independent of the final sign of the matched comparison.
- **Finding 3 (vacuum friction UV-divergent as ~N^0.7, approaching N³):** Retracted in its quantitative form. The corrected engine shows no collapse and no fit-able decay envelope at N ≤ 256; the N³ figure was the fingerprint of the uncancelled UV piece on the force RHS. Whether *any* residual UV dependence exists with per-mode subtraction plus a smooth form factor remains open; it is not resolved by these runs.

### A.5 What survives methodologically

The r2 paper's methodological framework is intact and, with the benefit of the bug, arguably strengthened by having caught a specific way in which careless normal ordering can manufacture physics:

1. Per-mode vacuum subtraction is required on the force right-hand side, not only on the energy accounting. A global subtraction of the sum is not equivalent under coupled dynamics.
2. Spectral matching between topologies (ω_n^closed = ω_n^periodic) is mandatory for any topology comparison; the unmatched result is a frequency-mismatch artifact (Finding 2).
3. A smooth form factor alone does not guarantee a UV-convergent observable; it must be combined with proper per-mode subtraction.
4. End-of-run depletion is not a safe observable for a spring-restored plate. A time-averaged dissipation rate, or a fit over many oscillation cycles, is needed.

### A.6 What the paper should now claim

The corrected evidence does not support a claim that cavity topology changes the character of 1+1D vacuum friction. It supports: (a) that the naive topology comparison contains a large frequency-matching artifact (Finding 2); (b) that care is required in the force normal-ordering; (c) that the spring-restored-plate final-time depletion is not a robust observable. A future paper version should recast the contribution around the methodological lessons rather than a quantitative topology claim, or pursue the comparison with an observable robust to oscillation phase and a protocol with a cleaner dissipation envelope before making quantitative statements.

The r2 manuscript is left in place for historical record; readers should treat its quantitative Findings 1 and 3 as superseded by this addendum.
