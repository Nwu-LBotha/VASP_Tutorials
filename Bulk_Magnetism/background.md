# Introduction

Magnetism in solids originates primarily from the **electronic spin** and the **interactions between electrons**. Depending on the electronic structure and interactions within a material, the **magnetic moments** can arrange themselves in **different configurations**, resulting in **different magnetic properties**.

* In density functional theory (DFT) calculation: Magnetic properties can be investigated using **spin-polarised calculations**. 
* These calculations treat the spin-up and spin-down electron densities separately, allowing the system to develop a magnetic moment.

This tutorial introduces:
* The calculation of magnetic properties using the Vienna Ab initio Simulation Package (VASP).
* It focuses on non-magnetic, ferromagnetic and antiferromagnetic configurations of bulk materials using Pt, Fe and Ni.
* Moreover, a transition-metal oxide (FeO) is also used to demonstrate how different materials can exhibit different magnetic behaviour.

## Learning objectives

By completing this tutorial, you will learn how to:

* Perform non-spin-polarised and spin-polarised DFT calculations.
* Initialise ferromagnetic and antiferromagnetic configurations.
* Select appropriate initial magnetic moments using the MAGMOM parameter.
* Extract total and local magnetic moments from VASP output files.
* Compare the relative stability of different magnetic configurations using total energies.
* Investigate magnetic ordering in transition-metal oxides using DFT+U.

## 1. Magnetic configurations

Three magnetic configurations are considered in this tutorial.
	
```
| Configuration           | Description                                                                                    |
| Non-magnetic (NM)	      | Spin-up and spin-down electron densities are equal, resulting in zero net spin magnetisation.  |
| Ferromagnetic (FM)	  | Magnetic moments are aligned parallel, producing a net magnetic moment.                        |
| Antiferromagnetic (AFM) | Magnetic moments are aligned antiparallel, potentially producing zero net magnetisation.       |
```

* A spin-polarised calculation does not necessarily produce a magnetic material. 
* The final magnetic configuration depends on the electronic structure, initial magnetic moments and convergence of the calculation.

## 2. Important VASP parameters

* The following INCAR parameters are particularly important for magnetic calculations.
```
Parameter	Description
ISPIN = 1	Performs a non-spin-polarised calculation.
ISPIN = 2	Enables collinear spin polarisation.
MAGMOM	Specifies the initial magnetic moments of the atoms.
LORBIT = 11	Provides atom-resolved projected magnetic moments in the OUTCAR file.
LASPH = .TRUE.	Includes non-spherical contributions inside the PAW spheres.
LMAXMIX = 4	Extends the angular-momentum components used in charge-density mixing; particularly important for d-electron DFT+U calculations.
```

* For example, a ferromagnetic configuration containing four equivalent magnetic atoms can be initialised using:

```
ISPIN  = 2
MAGMOM = 4*2.0
```

* An antiferromagnetic configuration containing two magnetic atoms can be initialised using:

```
ISPIN  = 2
MAGMOM = 2.0 -2.0
```

* NOTE: 
    * The initial magnetic moments **must** follow the **atomic ordering** in the POSCAR file. 
    * They are starting values and are not fixed during the self-consistent calculation.
    * The simulation cell must be sufficiently **large** to represent the desired magnetic ordering. 
        * The crystallographic unit cell and magnetic unit cell are not necessarily the same.

For further information, consult the VASP documentation on magnetic materials.

## 3. Obtaining crystal structures

* The crystal structures used in this tutorial can be obtained from the Materials Project.
    * Pt (https://next-gen.materialsproject.org/materials/mp-126)
    * Ni (https://next-gen.materialsproject.org/materials/mp-23)
    * Fe (https://next-gen.materialsproject.org/materials/mp-150)
    * FeO (https://next-gen.materialsproject.org/materials/mp-1274279)

* The following structures are investigated:

```
Material  |    Crystal structure      |	Magnetic behaviour investigated                     |
Ni	      | Face-centred cubic (fcc)  |	Non-magnetic and ferromagnetic                      |
Fe	      | Body-centred cubic (bcc)  |	Non-magnetic, ferromagnetic and antiferromagnetic   |
Pt	      | Face-centred cubic (fcc)  |	Non-magnetic and spin-polarised                     |
FeO	      | Rocksalt	              |  Non-magnetic, ferromagnetic and AFM-II             |
```

* Conventional crystal structures are used as the starting point for the elemental metals. 
* For FeO, a separate magnetic unit cell is constructed to represent the AFM-II ordering.

The downloaded CIF structures can be converted into POSCAR files using:

   * pymatgen, a Python library for materials analysis and structure manipulation.
   * VASPKIT, which includes structure-conversion utilities.
   * qvasp, depending on the installed version and available conversion functions.

## 4. General calculation workflow

The following workflow is used throughout the tutorial:

1. Obtain the crystal structure and prepare the POSCAR.
2. Prepare the INCAR, KPOINTS and POTCAR files.
3. Perform a non-spin-polarised calculation where applicable.
4. Perform spin-polarised calculations using appropriate initial magnetic moments.
5. Extract the final total energies and magnetic moments.
6. Compare the different magnetic configurations.

Note, when comparing magnetic configurations use consistent computational settings
* exchange-correlation functional
* pseudopotentials
* energy cutoff
* k-point sampling
* convergence criteria.
* electronic-smearing settings.