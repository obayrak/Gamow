# Gamow States

**A browser-based solver for nuclear two-body bound states, resonances (Gamow states), scattering, α decay, fusion and coupled channels.**

Everything runs in one self-contained HTML page. There is nothing to install and no server: open `gamow_states.html` in a browser, pick a preset (for example α + ⁸Be → ¹²C, the Hoyle state) and the page finds the complex-energy pole, plots the wave function and gives widths, ANCs, transition strengths, cross sections and half-lives. A Theory tab with 26 sections and about 75 typeset equations documents every method used.

---

## Contents

- [What it does](#what-it-does)
- [Numerical methods](#numerical-methods)
- [Validation](#validation)
- [Quick start](#quick-start)
- [Building from source](#building-from-source)
- [Running the tests](#running-the-tests)
- [Repository layout](#repository-layout)
- [Known limitations](#known-limitations)
- [Data and credits](#data-and-credits)
- [License](#license)

---

## What it does

The page is organised in tabs. All of them share one potential model (Woods–Saxon, Gaussian, Yukawa, square well, Bencze analytic or a tabulated external potential, plus Coulomb, surface, spin–orbit and absorptive terms in FRESCO conventions).

| Tab | Capabilities |
|---|---|
| **Spectrum** | Bound states, resonances and virtual states for a given (n, l, j). Finds the state without an energy guess by deepening the well until the state is bound and following the pole back in the complex plane. Effective potential, radial wave function, c-product normalisation, Berggren-style expectation values, automatic checks and classification (bound / narrow / broad / above barrier). |
| **Fit to experiment** | Adjusts the depth (or another parameter) to reproduce a measured energy and/or width. |
| **ANC & reduced widths** | Asymptotic normalisation coefficients, reduced widths, R-matrix (Thomas-corrected) widths, real-energy flux widths and WKB estimates. |
| **EM transitions** | B(EL) between bound and Gamow states (θ-independent complex-scaled integrals), B(E2) along a band. |
| **Scattering** | Phase shifts, S-matrix, resonances from the phase shift compared with the poles, angular distributions (Rutherford/Mott limits), excitation functions, real-energy wave functions, effective-range expansion (Coulomb-modified). |
| **Astrophysics** | Astrophysical S-factor, Gamow window, numerical and narrow-resonance reaction rates, triple-α rate; **direct radiative capture** σ(E) and S(E) for E1 and E2 from the overlap of a scattering wave with a bound state. |
| **α-decay** | Half-lives with a global preformation factor, Geiger–Nuttall plot, the α-emitter table parsed from NUBASE2020 with filters; **deformation and fine structure**: coupled channels to the 2⁺ and 4⁺ daughter states, branching ratios and hindrance factors. |
| **Fusion** | Fusion σ(E) with incoming-wave boundary conditions, barrier distribution D(E) = d²(Eσ)/dE², comparison with Wong's formula. **Deformed and vibrating targets**: a JavaScript rewrite of CCFULL (isocentrifugal, all-order nuclear coupling, projectile and target excitation, two target modes, mutual excitations, anharmonic couplings, a transfer channel), the orientation integral, the R-matrix on a Lagrange mesh and four alternative propagators, with a method selector and comparison table. |
| **Coupled channels** | Bound and Gamow states of a particle coupled to a deformed rotor or a one-phonon vibrator: nucleon spin and spin–orbit, deformed Woods–Saxon volume R(θ), core states with complex energies ε − iΓ/2 (e.g. a broad ⁸Be(2⁺) core), automatic contour, pole continuation and depth fit following the pole, real-energy flux widths for deformed proton emitters (¹⁴¹Ho, ¹³¹Eu, …). Channel wave functions and coupled potentials are plotted. **Scattering at real energy**: S-matrix versus J, eigenphases and resonance parameters from them, elastic and inelastic (2⁺, 4⁺) cross sections versus E, and differential cross sections. |
| **Energy scan** | Map of log₁₀ \|W(E)\| of the matching Wronskian over the complex-energy plane; every minimum is refined and tested for θ- and contour-independence, separating genuine poles from the discretised rotated continuum. |
| **Wave function** | The solution along the complex contour r = R_m + s e^{iθ}. |
| **Pole trajectory** | The path of the pole during depth continuation. |
| **GAMOW** | A port of the GAMOW program (Vertse, Pál, Balogh 1982), with its published test run and the Bencze benchmark (Table 3 of the paper). |
| **Theory** | Equations, methods, numerical defaults, limits and extreme cases, validation summary, software structure, symbol list, preset parameters and references. |

Every plot exports PNG, SVG, CSV and DAT; tables export CSV. Heavy calculations run in a Web Worker with a progress bar and a Cancel button (with a fallback to the main thread when workers are unavailable). Day and night themes are included.

### Presets

Grouped in the preset menu:

- **α clusters**: α + α → ⁸Be, α + ⁸Be → ¹²C (Hoyle state and ground state), ³He + α → ⁷Be, α + ¹²C → ¹⁶O (1⁻ subthreshold), α + ¹⁶O → ²⁰Ne
- **Few-body and halo nuclei**: deuteron, ¹⁷F, ¹⁷O, ¹¹Be (single channel and particle–rotor with spin–orbit), ⁸B, ⁶Li, deformed proton emitters ¹⁴¹Ho and ¹³¹Eu
- **α decay**: ²¹²Po, ²³⁸U
- **Nuclear astrophysics**: p + ¹²C → ¹³N
- **Fusion**: ¹⁶O + ²⁰⁸Pb, ¹²C + ¹²C, ¹⁶O + ¹⁵⁴Sm and ¹⁶O + ¹⁴⁴Sm (the CCFULL test decks)
- **Benchmarks**: GAMOW 1982 test run, Bencze analytic potential, α + ⁸Be square well

---

## Numerical methods

| Problem | Method |
|---|---|
| Resonances and bound states | Exterior complex scaling with Numerov integration; outward on the real axis, inward on the rotated ray; complex secant on a scale-free Wronskian; c-product (Zel'dovich / Gyarmati–Vertse) normalisation |
| Independent check | Spectral complex scaling (Chebyshev collocation) with a complex eigenvalue solver; GAMOW 1982 port |
| Scattering | Numerov with Coulomb functions F, G by Steed's continued-fraction method; complex log-Γ by Lanczos |
| Capture | E1/E2 radial overlap of the scattering wave with the bound state |
| Fusion | Incoming-wave boundary conditions; Wong formula |
| Deformed / vibrating targets | CCFULL (Hagino, Rowley, Kruppa 1999) rewritten in JavaScript; orientation integral (Gauss–Legendre); R-matrix on a Lagrange mesh with sector propagation; Runge–Kutta, Fox–Goodwin, renormalised Numerov, Johnson log-derivative |
| Coupled-channel bound and Gamow states | Channels (l, j, I) recoupled with 6j symbols; re-orthogonalising coupled Numerov; pole condition = smallest eigenvalue of Y_in − Y_out at a matching radius inside the well; continuation with step doubling and smoothness tests |
| Coupled-channel scattering | Real-energy S-matrix, eigenphases (E_r, Γ = 2 / (dΣδ/dE)_max), sum over J, Clebsch–Gordan angular distributions |
| α fine structure | Bohr–Fröman–Mottelson deformed barrier and full coupled-channel roots |

---

## Validation

Each module was checked against an independent calculation, an exact result or published numbers. A selection (the full table is in Theory §21):

| Quantity | Reference | Agreement |
|---|---|---|
| Bound and resonance poles (WS, Gaussian, square well) | 30-digit RK4 + exact Whittaker functions (mpmath) | 10⁻⁹ – 10⁻¹¹ MeV |
| GAMOW 1982 test deck | published iteration printout | step by step |
| Coulomb functions F, G | mpmath | 10⁻¹⁵ |
| Phase shifts | DOP853 + mpmath | 10⁻⁹ rad |
| Direct-capture cross sections (E1, E2) | independent DOP853 + mpmath implementation | 3×10⁻⁵ – 5×10⁻⁶ |
| CCFULL (JavaScript) | original Fortran CCFULL, ¹⁵⁴Sm rotor and ¹⁴⁴Sm phonon decks | 3×10⁻⁴ (print precision) |
| CCFULL extensions (projectile, mutual, anharmonic, transfer) | Fortran CCFULL | ≤ 3×10⁻⁴ |
| R-matrix on a Lagrange mesh | Fortran R-matrix of the same package | 6.6×10⁻⁵ |
| Coupled-channel bound state (particle + rotor) | independent SciPy implementation | ≤ 1.7×10⁻¹⁰ MeV |
| Coupled-channel eigenphase resonances | complex-scaling poles | E_r 10⁻⁸ – 10⁻⁶ MeV, Γ 0.3 – 1.1 % |
| α-decay half-lives, 27 even–even emitters | experiment, one global P_α | rms 0.37 in log₁₀ T½ |

The regression suite (`tests/`) freezes 175 values from 53 cases so that later changes cannot silently alter results.

---

## Quick start

1. Download `gamow_states.html` (or open the GitHub Pages link).
2. Open it in a current Chrome, Edge, Firefox or Safari.
3. Choose a preset and press **Solve state**.

An internet connection is needed on first load for the two libraries taken from a CDN:

- [Plotly.js](https://plotly.com/javascript/) 2.35.2 (plots)
- [MathJax](https://www.mathjax.org/) 3.2.2 (equations in the Theory tab)

For fully offline use, download these two files and point the `<script>` tags in `template.html` to the local copies before building.

---

## Known limitations

These are also listed, with what the page does about each, in Theory §19.

- **Quasi-elastic barrier distribution** at backward angles is not offered: with couplings, the computed quasi-elastic cross section still depends on the matching radius.
- **Pauli blocking** is not projected out in coupled-channel bound states; channels with occupied core orbits (e.g. p3/2 ⊗ 2⁺ in ¹¹Be) are kept and can over-bind. Their weights are shown.
- **Core widths** are constant (on-shell); in strongly closed channels the core-decay share of Γ is flagged.
- **Vibrators** are one-phonon with linear coupling (multi-phonon states are available in the CCFULL fusion part only).
- **Differential cross sections** in coupled-channel scattering ignore the nucleon spin; long-range Coulomb excitation is not included.
- **Very small widths** (Γ ≲ 10⁻⁹ MeV) are below rounding noise of Im E; the page uses real-energy flux widths instead.
- Some experimental comparison values (e.g. the α fine-structure branchings) were entered from memory of the evaluated data and should be checked against ENSDF before use in a publication.

---

## Data and credits

- **NUBASE2020**: F. G. Kondev, M. Wang, W. J. Huang, S. Naimi, G. Audi, *Chin. Phys. C* **45** (2021) 030001. Used for the α-emitter table.
- **CCFULL**: K. Hagino, N. Rowley, A. T. Kruppa, *Comput. Phys. Commun.* **123** (1999) 143. The Fortran version with Numerov, discrete-basis and R-matrix solvers by Z. Liao and K. Hagino ([murry-liao/CCFULL_progame](https://github.com/murry-liao/CCFULL_progame)) served as the reference.
  *Note:* that Fortran code never sets its transfer switch `Ntrans`. The transfer comparison used a copy with `Ntrans = 1` when `Ftr ≠ 0` (with the transfer channel counted in `Nlevel`).
- **GAMOW**: T. Vertse, K. F. Pál, Z. Balogh, *Comput. Phys. Commun.* **27** (1982) 309.
- **R-matrix on Lagrange meshes**: P. Descouvemont, D. Baye, *Rep. Prog. Phys.* **73** (2010) 036301.
- Coulomb functions: A. R. Barnett, *Comput. Phys. Commun.* **27** (1982) 147; I. J. Thompson, A. R. Barnett, *J. Comput. Phys.* **64** (1986) 490.

The full reference list (26 entries) is in Theory §26.

---

## License

Choose a license and add it as `LICENSE` before publishing (MIT or BSD-3-Clause are common for scientific code). The NUBASE2020 data in `nubase.js` remain subject to the terms of the original publication.
