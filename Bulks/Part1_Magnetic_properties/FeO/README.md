# Example 4: Determining the magnetic moment of an unknown system – FeO

This example demonstrates how to investigate a system when the magnetic moments and magnetic ordering are not known beforehand.

## 4.1. Task

* Investigate the magnetic properties of iron(II) oxide (FeO) using spin-polarised DFT calculations.
* In contrast to the elemental metals, FeO contains transition-metal cations coordinated by oxygen atoms. 
    * The local chemical environment and electron localisation can have a considerable influence on the magnetic moments.

By the end of this example, you should be able to:

1. identify potentially magnetic atoms in a compound;
2. select reasonable initial magnetic moments;
3. construct a magnetic unit cell;
4. investigate ferromagnetic and antiferromagnetic arrangements;
5. extract local magnetic moments from the OUTCAR file;
6. compare magnetic configurations using DFT+U.

## 4.2. Background: magnetism in FeO

* FeO has a rocksalt crystal structure and contains nominal Fe²⁺ ions.
* The Fe²⁺ ion has a (3d^6) electronic configuration. 
    * In an octahedral coordination environment, Fe²⁺ can adopt a high-spin configuration with four unpaired electrons.

The corresponding spin-only magnetic moment is approximately:

$$
\mu_{\mathrm{spin}} = g\sqrt{S(S+1)}\mu_{\mathrm B}
$$

where (g) is approximately 2 and (S=2).

The spin-only effective paramagnetic moment is therefore approximately 4.90 μB. This should not be confused with the ordered magnetic moment, which is approximately 4 μB before accounting for covalency and other solid-state effects.

FeO exhibits antiferromagnetic ordering at low temperatures. The magnetic arrangement is commonly described as Type-II antiferromagnetism (AFM-II), in which ferromagnetic (111) planes are stacked with alternating spin directions.

For bulk wüstite, the Néel temperature is approximately 198 K. Real wüstite can also be non-stoichiometric, so the ideal stoichiometric FeO model used here represents a simplified material.

## 4.3. Preparing the FeO magnetic structure

An ordinary conventional rocksalt cell does not directly represent the AFM-II ordering using the same primitive chemical cell.

A larger magnetic unit cell is therefore required.

The following rhombohedral cell contains two Fe and two O atoms and can represent the AFM-II arrangement.

```
POSCAR

FeO AFM-II
4.33
1.0 0.5 0.5
0.5 1.0 0.5
0.5 0.5 1.0
Fe O
2 2
Direct
0.000000 0.000000 0.000000
0.500000 0.500000 0.500000
0.250000 0.250000 0.250000
0.750000 0.750000 0.750000
```

The atom order in this POSCAR is:
```
Atom	Species	Initial position
1	Fe	(0, 0, 0)
2	Fe	(0.5, 0.5, 0.5)
3	O	(0.25, 0.25, 0.25)
4	O	(0.75, 0.75, 0.75)
```
* This magnetic cell is adapted from the rhombohedral:  AFM-II cell used in the VASP tutorial for NiO.

## 4.4. Why use DFT+U?

* The localised 3d electrons in transition-metal oxides can be difficult to describe accurately using conventional GGA functionals such as PBE.
* The self-interaction error associated with standard approximate exchange-correlation functionals can lead to excessive delocalisation of d electrons.
* DFT+U introduces an additional on-site correction to improve the description of localised electronic states.

For this example, we use the Dudarev formulation, where only the effective parameter enters the correction.

$$
(U_{\mathrm{eff}}=U-J) 
$$


The following is an illustrative INCAR setup using 4.0 eV for Fe 3d orbitals:

 $$
 U_{\mathrm{eff}}=4.0 eV
 $$
 
This value is a **starting point** rather than a universal parameter. 
* The appropriate U value depends on the chosen functional, PAW potential and property being investigated.

```
# General settings
SYSTEM = FeO magnetic calculation

PREC   = Accurate
ENCUT  = 520
EDIFF  = 1E-6

# Structural relaxation
IBRION = 2
ISIF   = 3
NSW    = 100
EDIFFG = -0.02

# Electronic minimisation
ALGO   = Normal

# Electronic smearing
ISMEAR = 0
SIGMA  = 0.05

# Spin polarisation
ISPIN  = 2

# DFT+U
LDAU      = .TRUE.
LDAUTYPE  = 2
LDAUL     = 2 -1
LDAUU     = 4.0 0.0
LDAUJ     = 0.0 0.0

# Additional settings
LORBIT   = 11
LASPH    = .TRUE.
LMAXMIX  = 4
```

* The order of the entries in LDAUL, LDAUU and LDAUJ **must** correspond to the species order in POSCAR and POTCAR.
* Since the POSCAR contains Fe followed by O: 
    * the Hubbard correction is applied to the Fe-d orbitals, while no correction is applied to oxygen.

## 4.5. Important considerations

The magnetic moment obtained from DFT depends on several factors:
1. The exchange-correlation functional.
2. The PAW pseudopotential.
3. The Hubbard U parameter.
4. The crystal structure and local coordination.
5. The initial magnetic configuration.
6. The magnetic ordering represented by the simulation cell.

* Consequently, a single spin-polarised calculation is not sufficient to establish the magnetic ground state of an unknown material.
* It is recommended to investigate several reasonable initial magnetic moments and magnetic configurations before drawing conclusions.
    * For FeO, the AFM-II configuration is a physically motivated starting point based on its known low-temperature magnetic structure.
    * However, other magnetic arrangements may also be investigated to assess their relative stability.

## 4.6. Investigating different magnetic configurations

* To investigate the magnetic state of FeO, perform calculations with different initial magnetic configurations.
* It is important to use the same structure, lattice parameters, pseudopotentials, energy cutoff, k-point mesh and DFT+U parameters when comparing the initial magnetic states.

Prepare three separate directories:
```
FeO/
├── NM/
├── FM/
└── AFM-II/
```

* Each directory should contain: 
    * the same POSCAR
    * the same POTCAR
    * the same KPOINTS files
    * Only the magnetic settings in INCAR should differ.

### Calculation A: Non-magnetic configuration

For the non-spin-polarised calculation:
```
ISPIN = 1
```
* Remove the MAGMOM parameter.
* Keep the same DFT+U settings if the purpose is to compare the magnetic states within the same DFT+U approximation.


### Calculation B: Ferromagnetic configuration

For the ferromagnetic calculation, initialise both Fe atoms with positive magnetic moments:
```
ISPIN  = 2
MAGMOM = 2*5.0 2*0.0
```

* Both Fe atoms initially have parallel magnetic moments, while the oxygen atoms start with zero magnetic moments.

### Calculation C: Antiferromagnetic AFM-II configuration

For the AFM-II calculation, initialise the two Fe atoms with opposite magnetic moments:
```
ISPIN  = 2
MAGMOM = 5.0 -5.0 2*0.0
```

* This initialises the Fe atoms with antiparallel magnetic moments, while the oxygen atoms start with zero magnetic moments.
* The initial moments of 5 μB are starting guesses. The final local moments are determined self-consistently.

### 4.7. Results

The magnetic properties of FeO were investigated by comparing different configurations: 
* non-magnetic (NM).
* ferromagnetic (FM).
* Type-II antiferromagnetic (AFM-II).

#### 1. Total energy comparison

The final energies obtained from the OSZICAR and OUTCAR files are:

| Quantity          |         NM |         FM |     AFM-II |
| ----------------- | ---------: | ---------: | ---------: |
| **TOTEN** (eV/cell) | −23.714049 | −26.052644 | −26.156583 |
| **E0** (eV/cell)    | −23.714049 | −26.051258 | −26.154900 |

* The energy differences relative to the AFM-II configuration were calculated using:

$$
\Delta E_{\mathrm{FM-AFM}} =
E_{\mathrm{FM}}-E_{\mathrm{AFM}}
$$

$$
\Delta E_{\mathrm{FM-AFM}} =
0.103642\ \mathrm{eV/cell}
$$

Similarly:

$$
\Delta E_{\mathrm{NM-AFM}} =
E_{\mathrm{NM}}-E_{\mathrm{AFM}}
$$

$$
\Delta E_{\mathrm{NM-AFM}} =
2.440851\ \mathrm{eV/cell}
$$

* The simulation cell contains two FeO formula units. 
* Therefore, the FM–AFM-II energy difference corresponds to approximately 51.82 meV per FeO formula unit.
    * The AFM-II configuration has the lowest total energy among the three configurations investigated, indicating that it is the most energetically favourable magnetic arrangement within the chosen computational setup.

#### 2. Total magnetic moments

The total magnetic moments are obtained from the final **mag=** values in the OSZICAR files.

```
| Configuration | Total magnetic moment (μB/cell) |
| ------------- | ------------------------------: |
| NM            |                               0 |
| FM            |                          8.0027 |
| AFM-II        |                 Approximately 0 |
```

* The FM configuration produces a substantial net magnetic moment, while the AFM-II configuration has a total magnetic moment close to zero because the magnetic moments of the two Fe atoms cancel.

#### 3. Local magnetic moments

The **LORBIT = 11** parameter allows the local magnetic moments to be extracted from the OUTCAR file.

**Ferromagnetic configuration:**
```
| Atom | Species | s (μB) | p (μB) | d (μB) | Total (μB) |
| ---- | ------- | -----: | -----: | -----: | ---------: |
| 1    | Fe      |  0.026 |  0.020 |  3.758 |      3.804 |
| 2    | Fe      |  0.026 |  0.020 |  3.758 |      3.804 |
| 3    | O       |  0.022 |  0.188 |  0.000 |      0.211 |
| 4    | O       |  0.022 |  0.188 |  0.000 |      0.211 |
```

* Both Fe atoms have positive magnetic moments of approximately 3.80 μB, confirming the ferromagnetic arrangement.
* The oxygen atoms also exhibit small induced magnetic moments.

**Antiferromagnetic AFM-II configuration:**
```
| Atom | Species | s (μB) | p (μB) | d (μB) | Total (μB) |
| ---- | ------- | -----: | -----: | -----: | ---------: |
| 1    | Fe      |  0.009 |  0.006 |  3.693 |     +3.707 |
| 2    | Fe      | −0.009 | −0.006 | −3.693 |     −3.707 |
| 3    | O       |  0.000 |  0.000 |  0.000 |      0.000 |
| 4    | O       |  0.000 |  0.000 |  0.000 |      0.000 |
```

* The two Fe atoms have approximately equal and opposite magnetic moments, resulting in a compensated antiferromagnetic configuration with a total magnetic moment close to zero.
* The local Fe moments remain substantial despite the zero net magnetisation of the simulation cell.

#### Overall

The results demonstrate the importance of spin polarisation and magnetic ordering when describing FeO. 
* Both the FM and AFM-II configurations are energetically more favourable than the non-magnetic configuration. 
* However, the AFM-II arrangement has the lowest energy among the three configurations investigated, with an energy difference of approximately 51.82 meV per FeO formula unit relative to the FM configuration.
    * The calculated Fe magnetic moments are approximately 3.80 μB in the FM configuration and ±3.71 μB in the AFM-II configuration. 
* These results are consistent with the formation of local magnetic moments associated with the Fe 3d electrons.

* This example demonstrates: 
    * How spin-polarised DFT calculations, combined with DFT+U, can be used to investigate local magnetic moments and compare different magnetic orderings in transition-metal oxides.


## Author and Contact

**Dr Louise Magdalene Botha**

Postdoctoral Research Fellow  
HySA Infrastructure  
North-West University  
South Africa

[![ORCID](https://img.shields.io/badge/ORCID-0000--0002--1249--2706-A6CE39?logo=orcid&logoColor=white)](https://orcid.org/0000-0002-1249-2706)

| Contact | Details |
|:---|:---|
| **ORCID** | [0000-0002-1249-2706](https://orcid.org/0000-0002-1249-2706) |
| **Email** | [louise.botha@nwu.ac.za](mailto:louise.botha@nwu.ac.za) |

I welcome feedback, suggestions and opportunities for collaboration in computational materials science, density functional theory (DFT), VASP and magnetic materials modelling.

Please feel free to contact me with questions about the tutorial, suggestions for improvement, difficulties reproducing the calculations, potential research collaborations or opportunities for scientific peer review.