# Gyrochrone Model Grid README

`nml2` settings are identical to the `Test_solar_dif_rot_fast` case unless otherwise called out here. Modifications are:

* **SBC:** Allard SBC, rather than Grey SBC.
* **FK:** `FK = 10.8` for the differentially rotating case, `FK = 7.5` for the solid body case (chosen for approximate agreement with $0.9 M_{\odot}$ cluster stars).
* **TDISK:** Set to 10 Myr for all models.
* **PDISK:** Mass dependent:
    * 8 days for $M > 0.4 M_{\odot}$
    * $\log P \sim 3.82 \times M(M_{\odot})^{-0.62}$ for $M < 0.4 M_{\odot}$ (based on Somers et al. 2017 Upper Sco data).
* **CODM:** Mass dependent: $9 \times 10^4 \times M(M_{\odot})^{7.3}$ (based on Somers et al. 2016).
* **Model Runs:** Models are run either with full rotation, mixing, and transport, or as solid bodies.

---

### Grid Properties

Each model in the grid has: 
1.  A different mass
2.  A starting period
3.  A constant AM (Angular Momentum) diffusion coefficient

> **Note:** For precision work, the deuterium burning birthline start for the low-mass models in particular can inject significant rotational energy. You should instead launch with rotation high on the Hayashi track.