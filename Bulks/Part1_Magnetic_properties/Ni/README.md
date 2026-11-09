# VASP Hands-on: Bulk Magnetism

## Example 1: Non-magnetic and ferromagnetic nickel

---
### 1. Task
---

Investigate the magnetic properties of face-centred cubic nickel by comparing non-spin-polarised and spin-polarised calculations.

By the end of this example, you should be able to:

* perform a non-spin-polarised calculation;
* perform a spin-polarised calculation;
* initialise a ferromagnetic magnetic configuration;
* extract the local magnetic moment of Ni;
* compare the total energies of different magnetic states.

Bulk nickel crystallises in the fcc structure and exhibits ferromagnetic behaviour below its Curie temperature.

---
### 2. Input files
---

Prepare the following directory structure:

```text
Ni/
├── NM/
│   ├── POSCAR
│   ├── INCAR
│   ├── KPOINTS
│   └── POTCAR
│
└── FM/
    ├── POSCAR
    ├── INCAR
    ├── KPOINTS
    └── POTCAR
```

Both calculations must use the same initial crystal structure.

* Always make note of: 
    * ISPIN = 1: non-spin-polarised calculation.
    * ISPIN = 2 with positive MAGMOM: ferromagnetic initialisation.
    * ISPIN = 2 with positive and negative MAGMOM: antiferromagnetic initialisation.
    * When choosing the MAGMOM, remember that the initial magnetic configuration is not necessarily the final magnetic configuration and after the calculation, you can check the magnetic configuration.

---
### 3. Non-magnetic calculation
---

* In the NM directory, use the following INCAR:

```
SYSTEM = fcc Ni non-magnetic

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

# Non-spin-polarised calculation
ISPIN  = 1
```

Run the calculation using:

```
cd Ni/NM
srun vasp_std
```

Replace srun with the appropriate MPI or batch-job command used by your HPC system.

---
### 4.  Ferromagnetic calculation
---

In the FM directory, use:

```
SYSTEM = fcc Ni ferromagnetic

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
MAGMOM
4*1.0

# Magnetic moment analysis
LORBIT = 11
LASPH  = .TRUE.

```

* MAGMOM = 4*1.0, assigns an initial magnetic moment of +1.0 μB to each of the four Ni atoms.

Run the calculation:

```
cd Ni/FM
srun vasp_std
6.5 Analysis
```

After both calculations have converged, compare the final total energies.

* Extract the final energy from the OUTCAR file:
```
grep "TOTEN" OUTCAR | tail -1
```

* For the spin-polarised calculation, inspect the final magnetization (x) table in the OUTCAR file.

* You can also obtain the total cell magnetic moment from the last MD/electronic summary in OSZICAR:
```
grep "mag=" OSZICAR | tail -1
```

* The local Ni moments and the total cell moment should be considered separately.
* The ferromagnetic solution is energetically favourable over the non-magnetic solution if its converged total energy is lower.
* Remember to divide energies by the number of atoms or formula units when comparing calculations using different cell sizes.

---
### 5. Results
---

## 1. Comparison of Final Energy:
    * Non-magnetic (NM), free  energy   TOTEN  =    - 22.07729779 eV
    * Ferromagnetic (FM), free  energy   TOTEN  =   - 22.29746652 eV

    * Non-magnetic (NM), E0   = − 22.062146  eV
    * Ferromagnetic (FM), E0  = − 22.295294 eV

   * Energy difference:
       $$
       EFM - ENM :  −0.22017 eV 
       $$
       
       $$
       −0.05504 eV/atom
       $$

    The **negative energy** difference indicates that the **FM** configuration is energetically more **favourable** than the NM configuration. 

---
## 2. Total magnetic moment: 
---

   * The OSZICAR output gives:
```
1 F= ... mag= 2.4715
2 F= ... mag= 2.5048
3 F= ... mag= 2.4865
```

Which is the total magnetic moment of the four-atom simulation cell. 
* The average magnetic moment per Ni atom is therefore 2.4865/4 = 0.6216 μB
* This is a reasonable result for bulk ferromagnetic nickel.

---
## 3. Local magnetic moments
---

* The LORBIT = 11 parameter in the INCAR file allows the local magnetic moments to be extracted from the OUTCAR file.
* The final magnetization (x) table for the ferromagnetic calculation is:
```
magnetization (x)

# of ion       s       p       d       tot
------------------------------------------
    1       -0.007  -0.026   0.668   0.635
    2       -0.007  -0.026   0.668   0.635
    3       -0.007  -0.026   0.668   0.635
    4       -0.007  -0.026   0.668   0.635
------------------------------------------
tot         -0.027  -0.104   2.671   2.540
```
* The local magnetic moment of each Ni atom is approximately 0.635 μB. The largest contribution originates from the d orbitals, which contribute approximately 0.668 μB per atom.
* All four Ni atoms have the same positive magnetic moment, confirming that they have converged to a ferromagnetic arrangement.
    * Note that the sum of the atom-projected magnetic moments (2.540 μB) differs slightly from the total cell magnetic moment reported in the OSZICAR file (2.4865 μB). This difference arises because the local moments are obtained through projections onto PAW atomic spheres, whereas the total cell magnetisation includes contributions from the interstitial region.

---
## Overall
---

* The results demonstrate that spin polarisation is important for describing bulk nickel. 
    * The ferromagnetic configuration has a lower energy than the non-magnetic configuration, with a total magnetic moment of approximately 2.49 μB per four-atom cell, corresponding to an average of 0.62 μB per Ni atom.
    * The calculated local magnetic moments are also consistent with the formation of ferromagnetic order, as all four Ni atoms exhibit positive magnetic moments of approximately 0.64 μB.

* This example demonstrates how spin-polarised DFT calculations can be used to investigate magnetic properties and determine whether allowing spin polarisation produces a more energetically favourable solution.

---
## Author and Contact
---

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