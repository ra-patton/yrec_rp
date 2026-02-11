Control Options
===============

YREC is generally called with two namelist arguments: the "control" namelist, or `nml1`; and the "physics" namelist, or `nml2`.
This page is a reference that will document various current and deprecated possible values in the namelist files.


## Control (`.nml1`)

### Run parameters & Kind cards

YREC runs are separated into a number of "phases", referred to as `KINDRN`'s.
After completion of a `KINDRN`, execution will move onto the next `KINDRN`.
This functionality is designed so that different phases of rescaling, evolving,
or rescale & evolving, can be done in a single stellar evolution model run.

One `NUMRUN` should be defined, with the number of total `KINDRN`'s. Replace `i` with 1,2,3... etc, for the behavior of each step:

| Parameter     | Description    |
| ------------ | -------------- |
| `NUMRUN`      | Each YREC run can rescale a model, evolve a model, or do both (pre-MS only). This indicates the total number of such steps requested, *N*. |
| `KINDRN(i)`      | 1 = evolve 2= rescale (zero timestep, MS, or CHeB); 3 = rescale and evolve (pre-MS, where there is no zero timestep solution). This needs to be defined for each KINDRN in sequence. |
| `LFIRST(i)`      | `.TRUE.` = use stored starting model (`FFIRST`). `.FALSE.` = use result of prior `KINDRN` step |
| `RSCLM(i)`      | If positive, rescale to this mass ($M_\odot$). |
| `RSCLCM(i)`      | If positive, rescale to this core mass (CHeB only). |
| `RSCLX(i)`      | If positive, rescale to this hydrogen. |
| `RSCLZ(i)`      | If positive, rescale to this metallicity. |
| `NMODLS(i)`      | Stop after this number of models. An initial rescaling with 2 models is recommended, followed by an evolve run. |
| `XENV0A(i)`      | Envelope abundance label. |
| `ZENV0A(i)`      | Envelope abundance label. |
| `CMIXLA(i)`      | Rescale to this mixing length (or use for the run). |
| `LSENV0A(i)`      | If true, adjust outer fitting point mass location to `SENV0A(i)`. |
| `SENV0A(i)`      | Log of fractional envelope fitting point mass. 1e-4 standard, 1e-7 thin. |
| `ENDAGE(i)`      | Run stops if `NMODLS(i)` is reached or this age is reached. |
| `END_DCEN(i)`      | Run stops if `NMODLS(i)`  is reached or this central deuterium is reached (D Birthline, start: 2.75d-5, D Birthline, end: 2.75d-7). |
| `END_XCEN(i)`      | Run stops if `NMODLS(i)` is reached or this central hydrogen is reached (ZAMS: 0.714620, TAMS: 0.0001). |
| `END_YCEN(i)`      | Run stops if `NMODLS(i)` is reached or this central helium is reached (ZAHB: 0.9700, TAHB: 0.0001). |

### File Location Specifiers

#### Environment Variables

YREC supports substitution of path components within the CONTROL namelist files via a set of placeholders.
The placeholders may be used as a prefix in path values found in the CONTROL namelist to allow a generalized
file that will function in a variefy of contexts without requiring edits, e.g., if the location of
the inputs tree or starting model changes relative to the working directory.

Supported environment variables are:
`YREC_INPUT`
`YREC_START`
`YREC_OUTPUT`

Those names, enclosed in curly braces (`{}`) will be replaced by the
value of the corresponding environment variable as defined within the YREC execution environment.
Especially for the YREC_INPUT and YREC_START values, setting an absolute path can provide the
greatest flexibility in locating input files across multiple potential execution locations.

If any of those variables are not defined in the execution environment, a default value for the
path prefix that each represents will be used instead.

| Placeholder | Default value   |
| --------------  | -------------- |
| `{YREC_INPUT}`  |  `"../../input"`  |
| `{YREC_START}`  |  `"../../input/models"`  |
| `{YREC_OUTPUT}` |  `"output"`  (within the working directory where `yrec` is invoked)  |

Example:

In the CONTROL namelist,
`FOPALE06 = "{YREC_INPUT}/eos/opal2006/EOSOPAL06Z0.016492"` \

If the environment variable is not defined, the default prefix will be used and
the value becomes \
`FOPALE06 = "../../input/eos/opal2006/EOSOPAL06Z0.016492"` at runtime.

Whereas if one sets the environment variable,

```
$ export YREC_INPUT=/path/to/input/tree
```

the value will expand to \
`FOPALE06 = "/path/to/input/tree/eos/opal2006/EOSOPAL06Z0.016492"` at runtime.


#### Input files

Opacity tables & options:

| Parameter     | Description    |
| ------------ | -------------- |
| `LALEX06`      | If `.TRUE.`, use Ferguson+2005 molecular opacities (recommended). |
| `FALEX06`      | Molecular opacity table path. |
| `LOPAL95`      | If `.TRUE.`, use atomic opacities in the OP/OPAL format (recommended). |
| `ZOPAL951`      | Generate a surface mixture table with this Z. Speeds up models without diffusion. |
| `FLIV95`      | Atomic opacity table path. |
| `LPUREZ`      |  |
| `FPUREZ`      | Pure Y/Z  opacity table path. |
| `FcondOpacP`      | Conductive opacity table path. |

Equation of state tables:

| Parameter     | Description    |
| ------------ | -------------- |
| `FOPALE06`      | OPAL 2006 EOS table path. |
| `FATM`      | Model atmosphere look-up table for Kurucz and Kurucz/Castelli. |
| `FALLARD`      | Allard model atmosphere look-up table (solar Z only). |
| `FSCVH`      | SCZ EoS table for X. |
| `FSCVHE`      | SCZ EOS table for Y. |
| `FSCVZ`      | SCZ EoS table for Z (generated from YREC EOS, needed for consistency) |
| `FFERMI`      | Fermi integral tables for partial degeneracy. |

Starting model:

| Parameter     | Description    |
| ------------ | -------------- |
| `FFIRST`      | Starting model file. |

#### Output

The starting and last models include only abundances and structure variables. These options allow snapshots to be generated with more information.

Output options:

| Parameter     | Description    |
| ------------ | -------------- |
| `LSTORE`      | If `.TRUE.`, store model structures at specified points to the `.store` file. |
| `LSTCH`      | If `.TRUE.`, writes out interior, envelope, and atmosphere output (as controlled by `LSTATM` and `LSTENV`) in a single, consistent format (recommended). The default is different formats for each. |
| `LSTATM`      | If `.TRUE.`, append an atmosphere to the stored model file. |
| `LSTENV`      | If `.TRUE.`, envelope to the stored model file. If `LSTENV` and `LSTATM` = `.FALSE.`, no information is output beyond the fitting point. |
| `LSTMOD`      | Set the same as `LSTORE`. May be redundant. |
| `LSTPHYS`      | If `.TRUE.`, output microphysics (e.g. epsilon, kappa) to `.store` file. |
| `LSTROT`      | If `.TRUE.`, append rotation data to `.store` file. |
| `NPRTMOD`      | Output detailed model structures every `NPRTMOD` models. Negative = skip intermediate structures (can still output the final model if `LSTPCH`=`.TRUE.` |
| `NPRTPT`      | Output details for every `NPRTPT` points (if 1000 points and `NPRTPT`=10, 100 lines are output). |
| `LSTPCH`      | If `.TRUE.`, output details of the final model to the `.store` file. |
| `LTRACK`      | If `.TRUE.`, global properties are output to the `.track` file. This should almost always be on. |
| `ITRVER`      | Toggle for different track file formats. 0 (one line per model) strongly recommended. |
| `LRWSH`      | If `.TRUE.`, the `.short.` file retains only the details from the last model. This minimizes output file size. |
| `LCORR`      | If `.TRUE.`, output verbose details on model convergence to `.short`. |
| `LPULSE`      | If `.TRUE.`, generate pulsation output. `LSTATM` AND `LSTENV` are needed to get data for those regions (needed if `LPULSE` is `.TRUE.`) |
| `IPVER`      | Format for pulsation output. |
| `LSTPCH`      | If `.TRUE.`, output details of the final model to the `.store` file. |
| `LSTPCH`      | If `.TRUE.`, output details of the final model to the `.store` file. |

Output files:

| Parameter     | Description    |
| ------------ | -------------- |
| `FLAST`      | The last converged model is stored here. |
| `FSTOR`      | Model snapshots during the run are stored here. |
| `FTRACK`      | Global variables as a function of time are stored here. |
| `FSHORT`      | Details about the numerics of the model run are stored here. |
| `FPMOD`      | Pulsation output for the interior model. |
| `FPENV`      | Pulsation output for the envelope model. |
| `FPATM`      | Pulsation output for the atmosphere model. |

Legacy output files:

| Parameter     | Description    |
| ------------ | -------------- |
| `FMODPT`      | No longer used. |
| `FSNU`      | No longer used (formally, output if `LSNU` = `.TRUE.`). |
| `FSCOMP`      | No longer used (light elements vs. time, now included in `.track` file) |

### Mixtures
In addition to X, Y, & Z, YREC stores light elements (D He3 Li6 Li7 Be9) and CNO isotopes (C12 C13 N14 O16 O18) B10 B11 N15 O17 to be added.

These options adjust their relative abundances. It is recommended to tie these to preset mixtures used in opacity tables. This should be done at the start of a model run, and only work if `LFIRST` = `.TRUE.` Option to adjust the CNO mixture to be consistent with the mixture used in the opacity tables.

| Parameter     | Description    |
| ------------ | -------------- |
| `ISETMIX`      | 0 = do not adjust, 1 = use specified mixture (`AMIX`), 2 = custom mixture (variables at the end of the namelist) |
| `AMIX`      | current implemented solar mixtures are `GS98`, `AAG21`, `M22P`, and `M22M`. |

Option to adjust the D/He3/Li6/Li7/Be9 abundances and the CNO isotope ratios to be consistent with a specified solar reference

| Parameter     | Description    |
| ------------ | -------------- |
| `ISETISO`      | 0 = do not adjust, 1 = use specified mixture (`AISO`), 2 = custom mixture (variables at the end of the namelist) |
| `AISO`      | `L21`: Lodders 2021 is currently the only supported mixture, for a different one use the custom option. |

Custom mixture controls:

CNO enabled if `ISETMIX = 2`, Light elements if `ISETISO=2`

Total CNO (isotope ratios controlled with `ISETISO`):

| Parameter     | Description    |
| ------------ | -------------- |
| `FRAC_C`      | Mass fraction of metals in the form of carbon. GS98: 0.172148 |
| `FRAC_N`      | Mass fraction of metals in the form of nitrogen. GS98: 0.050426 |
| `FRAC_O`      | Mass fraction of metals in the form of oxygen. GS98: 0.468195 |

Isotope ratios and light elements:

| Parameter     | Description    |
| ------------ | -------------- |
| `R12_13`      | C12/C13 ratio, L21 mix (88.26 L25) |
| `R14_15`      | N14/N15 ratio, L21 mix (412.3 L25) - placeholder,N15 not currently used |
| `R16_17`      | O16/O17 ratio, L21 mix (2644 L25)- placeholder,O17 not currently used |
| `R16_18`      | O16/O18 ratio, L21 mix (468.2 L25) |

Light element abundances:

| Parameter     | Description    |
| ------------ | -------------- |
| `XH2_INI`      | Initial D mass fraction, L21 mix (2.780e-5 L25) |
| `XHE3_INI`      | Initial He3 mass fraction, L21 mix (3.443e-5 L25) |
| `XLI6_INI`      | Initial Li6 mass fraction, L21 mix (7.830e-10 L25) |
| `XLI7_INI`      | Initial Li7 mass fraction, L21 mix (1.112e-8 L25) |
| `XBE9_INI`      | Initial Be9 mass fraction, L21 mix (1.810e-10 L25) |
| `XB10_INI`      | Initial B10 mass fraction, L21 mix (1.099e-9 L25) - placeholder, not currently used |
| `XB11_INI`      | Initial B11 mass fraction, L21 mix (4.885e-9 L25) - placeholder, not currently used |

### Calibration

| Parameter     | Description    |
| ------------ | -------------- |
| `LCALS`      | Toggle to auto-calibrate solar Y and alpha to match the solar R and L at the age of the Sun. |
| `LCALSOLZX`      | Enforce correct Z/X (`CALSOLZX`) for mixture. If this is false Z is held constant in the calibration loop. |
| `CALSOLZX`      | Target solar Z/X. (0.02292 is GS98) |
| `TOLL`      | Tolerance target for L in the calibration routines. |
| `TOLR`      | Tolerance target for R in the calibration routines. |
| `CALSOLAGE`      | Tolerance solar age in the calibration routines. |
