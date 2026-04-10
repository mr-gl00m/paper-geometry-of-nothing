# Geometry of Nothing

**Spectral Matching, UV Divergence, and the Standing-Wave Advantage in Dynamical Casimir Simulations**

*Cid (Independent Researcher) — April 2026*

---

## Overview

A computational investigation of vacuum friction in 1+1D dynamical Casimir cavities. Using a validated quantum field simulator ([Nothing Engine](https://github.com/mr-gl00m/nothing-engine)), this paper reports three principal findings:

1. **The Spectral Matching Trap** — Naive topology comparisons produce dramatic apparent differences (99% vs 24.5% energy depletion) that vanish entirely under spectral matching. The false-positive mechanism is characterized and shown to be a general hazard for boundary-condition comparison studies.

2. **Standing Waves Win** — After matching mode frequencies, standing-wave (closed) boundaries couple ~5x more efficiently to a moving plate than traveling-wave (periodic) boundaries — the opposite of the initial hypothesis.

3. **UV Divergence** — The vacuum friction rate in 1+1D free scalar theory is UV-divergent: damping grows with mode count, and smooth form-factor regularizations (Gaussian, sigmoid) do not achieve convergence. Physical regularization via finite plate thickness is required.

## Paper

[`whitepaper_geometry_of_nothing.md`](whitepaper_geometry_of_nothing.md)

## Companion Papers

| Paper | Description |
|-------|-------------|
| [`whitepaper_toroidal_casimir_cavity_energy_harvester.md`](companions/whitepaper_toroidal_casimir_cavity_energy_harvester.md) | The original harvester concept, including energy deficit analysis and pivot to bearing applications |
| [`whitepaper_casimir_flywheel.md`](companions/whitepaper_casimir_flywheel.md) | Zero-friction rotational energy storage using repulsive Casimir-Lifshitz forces |

## Companion Software

**Nothing Engine** — Open-source 1+1D quantum field theory simulator with dynamical plate coupling and multi-topology boundary support.

The simulator implements three tracks:
- **Track A** (Semiclassical Lattice) — Classical scalar field on an ALE-mapped lattice with Wigner-sampled vacuum initial conditions
- **Track B** (Quantum Bogoliubov) — Exact vacuum state evolution in mode space via time-dependent Bogoliubov transformation
- **Track C** (Energy Audit) — Continuous energy conservation monitoring at every timestep

All validation benchmarks, experiment scripts, analysis code, and raw HDF5 data are included.

## Key Results

| Comparison | Periodic / Closed Ratio | Conclusion |
|------------|------------------------|------------|
| Unmatched spectra (L = a) | ~80x enhancement | **Spectral artifact** |
| Matched spectra (L = 2a) | ~0.2-0.3x (3-5x suppression) | **Standing waves couple more efficiently** |

## Validation

The simulator passed five analytical benchmarks before any experimental runs:

- Static Casimir energy (relative error < 10^-15)
- Dynamic Casimir photon production (0.02% error)
- Energy conservation (drift < 5 x 10^-12)
- Adiabatic limit (max occupation < 10^-9)
- Residual motion baseline (max |beta_n|^2 < 3 x 10^-10)

## Citation

```
Cid. "Geometry of Nothing: Spectral Matching, UV Divergence, and the Standing-Wave
Advantage in Dynamical Casimir Simulations." April 2026. Independent research.
```

## License

This work is shared for open scientific review and discussion. See repository for license details.
