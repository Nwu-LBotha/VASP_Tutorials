# VASP Hands-on: Bulk Magnetism

## Example 2: Magnetic configurations of iron

-------------------------------------------------------
### 1 Task
-------------------------------------------------------

* Investigate the magnetic properties of body-centred cubic iron by comparing: 
    * non-magnetic
    * ferromagnetic
    * antiferromagnetic

* Iron is a particularly useful example because its magnetic properties depend on its crystal structure and magnetic ordering.
    * Alpha-iron has a bcc structure (At ambient pressure).
    * Ferromagnetic (below its Curie temperature).

-------------------------------------------------------
## 2 Preparing the bcc Fe structure
-------------------------------------------------------

A conventional bcc cell contains two Fe atoms.
https://next-gen.materialsproject.org/materials/mp-13 

The POSCAR can be represented as:
```
cif2pos.py
1.0
     2.86303550     0.00000000     0.00000000
     0.00000000     2.86303550     0.00000000
     0.00000000     0.00000000     2.86303550
  Fe
  2
Direct
     0.00000000     0.00000000     0.00000000
     0.50000000     0.50000000     0.50000000
```
* Always remember that these structure should be relaxed during the calculation.
* The two Fe atoms allow us to initialise different collinear magnetic arrangements.

-------------------------------------------------------
## 3 Non-magnetic configuration
-------------------------------------------------------

**Use the following INCAR settings:**
```
SYSTEM = bcc Fe non-magnetic
PREC   = Accurate
ENCUT  = 520
EDIFF  = 1E-6

IBRION = 2
ISIF   = 3
NSW    = 100
EDIFFG = -0.02

ISMEAR = 1
SIGMA  = 0.2

ISPIN  = 1
```

-------------------------------------------------------
## 4 Ferromagnetic configuration
-------------------------------------------------------

**For the ferromagnetic calculation, both Fe atoms are assigned positive initial magnetic moments.**
```
SYSTEM = bcc Fe ferromagnetic

PREC   = Accurate
ENCUT  = 520
EDIFF  = 1E-6

IBRION = 2
ISIF   = 3
NSW    = 100
EDIFFG = -0.02

ISMEAR = 1
SIGMA  = 0.2

ISPIN  = 2
MAGMOM = 2*2.5

LORBIT = 11
LASPH  = .TRUE.
```

* Please note that a initial magnetic moment of 2.5 μB is used as a starting value and is not constrained during the calculation.

-------------------------------------------------------
## 5 Antiferromagnetic configuration
-------------------------------------------------------

To initialise a simple two-sublattice antiferromagnetic arrangement, assign opposite magnetic moments to the two Fe atoms:
* Note: 
    * The MAGMOM shows opposite magnetic sublattices which is an imposed collinear AFM configuration. 
    * This demonstrates how to initialise an antiferromagnetic calculation. 
        * It should not be assumed to represent the experimentally stable magnetic structure of alpha-iron.

```
SYSTEM = bcc Fe AFM

# Electronic minimisation
PREC   = Accurate
ENCUT  = 520
EDIFF  = 1E-6

# Structural relaxation
IBRION = 2
ISIF   = 3
NSW    = 100
EDIFFG = -0.02

# Electronic smearing
ISMEAR = 1
SIGMA  = 0.2

# Spin polarisation
ISPIN  = 2
MAGMOM = 2.5 -2.5

# Magnetic moment analysis
LORBIT = 11
LASPH  = .TRUE.

# Start from scratch
ISTART = 0
ICHARG = 2
```

-------------------------------------------------------
## 6 Analysis
-------------------------------------------------------

After completing the non-magnetic (NM), ferromagnetic (FM) and antiferromagnetic (AFM) calculations, compare the total energies and magnetic moments to determine the relative stability of the different magnetic configurations.

* The following results are extracted for the analysis: 
    1. Final total energy.
    2. Total cell magnetic moment.
    3. Local magnetic moments on each Fe atom.
    4. Lattice parameters of relaxed structures.

-------------------------------------------------------
### 1. Total energy comparison
-------------------------------------------------------

The final energies obtained from the OSZICAR and OUTCAR files are:

```
| Quantity          |         NM |         FM |        AFM |
| ----------------- | ---------: | ---------: | ---------: |
| **TOTEN** (eV/cell) | −15.664447 | −16.629806 | −15.749958 |
| **E0** (eV/cell)    | −15.663387 | −16.629860 | −15.749886 |
```

* The **E0** in metallic systems represents the energy extrapolated to zero electronic smearing and is useful for comparing the calculated energies.

The energy differences relative to the **FM configuration** are:

$$
\Delta E_{\mathrm{AFM-FM}} =
E_{\mathrm{AFM}}-E_{\mathrm{FM}}
$$

$$
\Delta E_{\mathrm{AFM-FM}} =
0.879974\ \mathrm{eV/cell}
$$

Similarly:

$$
\Delta E_{\mathrm{NM-FM}} =
E_{\mathrm{NM}}-E_{\mathrm{FM}}
$$

$$
\Delta E_{\mathrm{NM-FM}} =
0.966473\ \mathrm{eV/cell}
$$

* The results indicate that the FM configuration is energetically more favourable than both the AFM and NM configurations. Please note: Since the cells have the same number of atoms, I did not not express the energies per atom.

-------------------------------------------------------
### 2. Total magnetic moments
-------------------------------------------------------

The total magnetic moments are obtained from the final **mag=** values in the OSZICAR files.
```
| Configuration | Total magnetic moment (μB/cell) |
| ------------- | ------------------------------: |
| NM            |                               0 |
| FM            |                          4.3447 |
| AFM           |                 Approximately 0 |
```

* The FM calculation produces a net magnetic moment of approximately 4.34 μB per cell, corresponding to an average of 2.17 μB per Fe atom.
* In contrast, the AFM calculation has a total magnetic moment close to zero because the magnetic moments of the two Fe atoms cancel eachother out.

-------------------------------------------------------
### 3. Local magnetic moments
-------------------------------------------------------

The local magnetic moments can be extracted from the final **magnetization (x)** table in the OUTCAR file.

**Ferromagnetic configuration:**
```
| Fe atom | s (μB) | p (μB) | d (μB) | Total (μB) |
| ------- | -----: | -----: | -----: | ---------: |
| 1       | −0.013 | −0.057 |  2.241 |      2.171 |
| 2       | −0.013 | −0.057 |  2.241 |      2.171 |
```

* Both Fe atoms have positive magnetic moments, **confirming** the ferromagnetic arrangement.

**Antiferromagnetic configuration:**
```
| Fe atom | s (μB) | p (μB) | d (μB) | Total (μB) |
| ------- | -----: | -----: | -----: | ---------: |
| 1       | +0.014 | +0.022 |  1.245 |     +1.281 |
| 2       | −0.014 | −0.022 | −1.245 |     −1.281 |
```

* The two Fe atoms have equal and opposite local magnetic moments. 
* Consequently, the total magnetic moment of the simulation cell is approximately zero.

* Note: 
    * It is important to distinguish between the total magnetic moment of the cell and the local magnetic moments of individual atoms. 
    * An antiferromagnetic system can have a zero net magnetic moment while retaining substantial local magnetic moments.

-------------------------------------------------------
#### Overall
-------------------------------------------------------

* The calculations demonstrate that spin polarisation is important for describing the magnetic properties of bcc iron. 
* Both the FM and AFM solutions are energetically more favourable than the non-magnetic solution. However, the FM configuration has the lowest total energy among the three configurations investigated.
* The calculated FM magnetic moment is approximately 2.17 μB per Fe atom, while the AFM configuration produces local moments of approximately ±1.28 μB per Fe atom.

    * This example demonstrates how different initial magnetic configurations can lead to different self-consistent electronic solutions. 
    * It also illustrates why both the total energy and the local magnetic moments must be examined when determining the magnetic properties of a material.

---

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