# VASP Hands-on: Bulk Magnetism

# Part 1: Magnetic properties of bulk metals and transition-metal oxides

## Content

1. [Introduction to magnetism in DFT](#1-introduction-to-magnetism-in-dft)
2. [Magnetic configurations](#2-magnetic-configurations)
3. [VASP input parameters for magnetic calculations](#3-vasp-input-parameters-for-magnetic-calculations)
4. [Obtaining and preparing crystal structures](#4-obtaining-and-preparing-crystal-structures)
5. [Calculation setup and computational parameters](#5-calculation-setup-and-computational-parameters)
6. [Example 1: Non-magnetic and ferromagnetic nickel](#6-example-1-non-magnetic-and-ferromagnetic-nickel)
7. [Example 2: Magnetic configurations of iron](#7-example-2-magnetic-configurations-of-iron)
8. [Example 3: Investigating magnetism in platinum](#8-example-3-investigating-magnetism-in-platinum)
9. [Example 4: Determining the magnetic moment of an unknown system – FeO](#9-example-4-determining-the-magnetic-moment-of-an-unknown-system--feo)
10. [Extracting and comparing magnetic properties](#10-extracting-and-comparing-magnetic-properties)
11. [Questions](#11-questions)

---

## 1. Introduction to magnetism in DFT

Magnetism in solids originates primarily from the electronic spin and the interactions between electrons. Depending on the electronic structure and interactions within a material, the magnetic moments can arrange themselves in different configurations, resulting in different magnetic properties.

In density functional theory (DFT), the electronic spin can be included by performing a spin-polarised calculation. This allows the spin-up and spin-down electron densities to be treated separately. The resulting electronic structure can develop a net magnetic moment when the spin-up and spin-down electron populations are unequal.

In VASP, spin-polarised calculations are activated by setting **ISPIN = 2** in the INCAR file. This enables collinear spin-density functional theory (SDFT), in which the magnetic moments are aligned parallel or antiparallel to a common spin-quantisation axis.

For calculations where spin polarisation is not considered, **ISPIN = 1** is used. This corresponds to a spin-degenerate, non-spin-polarised calculation.

It is important to distinguish between a spin-polarised calculation and a magnetic material. Setting **ISPIN = 2** only allows the system to develop spin polarisation. It does not guarantee that the final structure will have a non-zero magnetic moment. The final magnetic state depends on the electronic structure, the initial magnetic moments and the convergence of the calculation.

### 1.1 Magnetic moment

The magnetic moment of an atom is related to the difference between the number of spin-up and spin-down electrons.

For a system containing only spin contributions, the magnetic moment can be expressed approximately as:

$$
\mu = (N_{\uparrow}-N_{\downarrow})\mu_{\mathrm B}
$$

where:

* \(N_{\uparrow}\) is the number of spin-up electrons.
* \(N_{\downarrow}\) is the number of spin-down electrons.
* \(\mu_{\mathrm B}\) is the Bohr magneton.

The total magnetic moment of a simulation cell is obtained by integrating the magnetisation density over the entire cell.

In VASP, the local magnetic moments associated with individual atoms can also be obtained using the **LORBIT** parameter. These provide information about how the magnetisation is distributed throughout the structure.

### 1.2 Why investigate magnetic properties?

Magnetic properties are particularly important when investigating materials containing transition metals. Elements such as Fe, Ni and Co have partially occupied d orbitals, which can contribute to the formation of local magnetic moments and magnetic ordering.

The magnetic configuration can also influence the calculated total energy, lattice parameters, electronic structure and chemical properties of a material.

For this tutorial, three elemental metals are investigated:

| Element | Crystal structure        | Expected bulk magnetic behaviour |
| ------- | ------------------------ | -------------------------------- |
| Ni      | Face-centred cubic (fcc) | Ferromagnetic                    |
| Fe      | Body-centred cubic (bcc) | Ferromagnetic                    |
| Pt      | Face-centred cubic (fcc) | Non-ferromagnetic                |

The calculations will demonstrate how to investigate non-magnetic and spin-polarised solutions and compare different magnetic configurations.

An additional example involving FeO will demonstrate how to investigate the magnetic properties of a system for which the local magnetic moments and magnetic ordering need to be established through calculations.

---

## 2. Magnetic configurations

There are several possible magnetic configurations in solids. For this tutorial, we focus on non-magnetic, ferromagnetic and antiferromagnetic arrangements.

### 2.1 Non-magnetic configuration

In a non-spin-polarised calculation, the spin-up and spin-down electron densities are constrained to be equal. Consequently, the system has no net spin magnetisation.

This configuration is obtained in VASP by setting:

```text
ISPIN = 1
```

No initial magnetic moments need to be specified.

A non-magnetic calculation is useful as a reference when investigating whether spin polarisation lowers the total energy of a material.

It is important to note that **ISPIN = 1** represents a spin-unpolarised electronic state. It does not describe a paramagnetic material with fluctuating local magnetic moments. Modelling disordered local magnetic moments requires more advanced approaches.

### 2.2 Ferromagnetic configuration

In a ferromagnetic (FM) material, neighbouring magnetic moments are aligned parallel to one another. This produces a non-zero net magnetic moment.

For example, in ferromagnetic Ni, the magnetic moments associated with the Ni atoms align in the same direction.

In VASP, an initial ferromagnetic configuration is specified by assigning positive magnetic moments to the magnetic atoms using **MAGMOM**.

For a system containing four equivalent magnetic atoms:

```text
ISPIN  = 2
MAGMOM = 4*1.0
```

This initialises all four atoms with a magnetic moment of +1.0 μB.

The initial magnetic moments are not fixed during the calculation. They provide an initial spin-density distribution from which VASP performs the self-consistent electronic minimisation.

### 2.3 Antiferromagnetic configuration

In an antiferromagnetic (AFM) material, neighbouring magnetic moments align antiparallel to one another. In a compensated antiferromagnet, the positive and negative magnetic moments cancel, resulting in a net magnetic moment close to zero.

For a simple system containing two magnetic atoms, an antiferromagnetic configuration can be initialised using:

```text
ISPIN  = 2
MAGMOM = 2.0 -2.0
```

The first atom is initialised with a positive magnetic moment, while the second atom is initialised with a negative magnetic moment.

However, an important consideration is that the simulation cell must be sufficiently large to accommodate the desired magnetic ordering. A primitive unit cell containing only one magnetic atom cannot represent an antiferromagnetic arrangement between different lattice sites.

For this reason, larger simulation cells or magnetic unit cells are often required.

### 2.4 Comparing the magnetic configurations

The relative stability of different magnetic configurations can be investigated by comparing their converged total energies.

For example, the following calculations can be performed:

| Configuration     | ISPIN | MAGMOM                        | Description                                     |
| ----------------- | ----: | ----------------------------- | ----------------------------------------------- |
| Non-magnetic      |     1 | Not required                  | Spin-up and spin-down densities are equal       |
| Ferromagnetic     |     2 | Positive moments              | Magnetic moments initially aligned parallel     |
| Antiferromagnetic |     2 | Positive and negative moments | Magnetic moments initially aligned antiparallel |

The lowest-energy converged configuration is the most favourable among the magnetic states investigated, provided that the calculations have been performed using consistent computational settings.

A lower total energy indicates a more favourable electronic and magnetic configuration within the chosen computational approximation. It does not automatically prove that the global magnetic ground state has been found. Other possible magnetic orderings may also need to be considered.

---

## 3. VASP input parameters for magnetic calculations

Several INCAR parameters are particularly important when performing magnetic calculations.

### 3.1 ISPIN

The **ISPIN** parameter determines whether spin polarisation is included in the calculation.

```text
ISPIN = 1
```

Non-spin-polarised calculation.

```text
ISPIN = 2
```

Spin-polarised calculation.

For the systems investigated in this tutorial, collinear spin polarisation is sufficient. Therefore, the standard VASP executable, **vasp_std**, can be used.

Non-collinear magnetism and spin-orbit coupling require additional settings and the **vasp_ncl** executable. These topics are outside the scope of this tutorial.

### 3.2 MAGMOM

The **MAGMOM** parameter specifies the initial magnetic moments of the atoms.

For example:

```text
MAGMOM = 4*2.0
```

is equivalent to:

```text
MAGMOM = 2.0 2.0 2.0 2.0
```

Similarly:

```text
MAGMOM = 2.0 -2.0 2*0.0
```

initialises two magnetic atoms with opposite moments, followed by two atoms with zero initial moments.

**Important:** The magnetic moments must be specified in the same order as the atoms appear in the POSCAR file. The number of entries must correspond to the total number of atoms.

The initial values should be chosen based on the expected oxidation states, electronic configurations and magnetic properties of the system.

The final magnetic moments may differ significantly from the initial values. VASP determines the final electronic spin density self-consistently.

The initial magnetic configuration can strongly influence the converged solution. Therefore, it is important to investigate more than one initial configuration when the magnetic ground state is unknown.

When restarting a magnetic calculation from a previous WAVECAR or CHGCAR, retain the appropriate **MAGMOM** specification. It also influences the symmetry used by VASP and removing it can cause the magnetic arrangement to be symmetrised away.

### 3.3 LORBIT

The **LORBIT** parameter controls the projection of the electronic states onto local atomic orbitals. It also allows the local magnetic moments to be printed in the OUTCAR file.

For this tutorial, use:

```text
LORBIT = 11
```

This produces atom-resolved magnetic moment information, including contributions from the s, p and d orbitals.

The resulting OUTCAR file contains a section similar to:

```text
magnetization (x)

# of ion       s       p       d       tot
------------------------------------------
    1        ...     ...     ...     ...
    2        ...     ...     ...     ...
    3        ...     ...     ...     ...
------------------------------------------
tot          ...     ...     ...     ...
```

The **tot** column gives the projected local magnetic moment for each atom.

These values are obtained by integrating the magnetisation density within the corresponding PAW projection spheres. They are useful estimates of local magnetic moments but do not necessarily add up to the total magnetic moment of the simulation cell.

### 3.4 LASPH

The **LASPH** parameter includes non-spherical contributions to the gradient corrections inside the PAW spheres.

For transition-metal systems, particularly when using DFT+U, it is recommended to include:

```text
LASPH = .TRUE.
```

### 3.5 Summary of the magnetic INCAR parameters

| Parameter        | Function                                                                                                                              |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `ISPIN = 1`      | Non-spin-polarised calculation                                                                                                        |
| `ISPIN = 2`      | Spin-polarised calculation                                                                                                            |
| `MAGMOM`         | Initial magnetic moment for each atom                                                                                                 |
| `LORBIT = 11`    | Outputs projected local magnetic moments                                                                                              |
| `LASPH = .TRUE.` | Includes non-spherical contributions inside PAW spheres                                                                               |
| `LMAXMIX = 4`    | Increases the angular momentum components included in charge-density mixing; particularly important for d-electron DFT+U calculations |

Further information is available in the [VASP documentation: Magnetic materials](https://vasp.at/wiki/Magnetic_materials), [ISPIN](https://vasp.at/wiki/ISPIN), [MAGMOM](https://vasp.at/wiki/MAGMOM) and [LORBIT](https://vasp.at/wiki/LORBIT).

---

## 4. Obtaining and preparing crystal structures

The first step in preparing a bulk calculation is obtaining the crystal structure.

For this tutorial, the structures of Ni, Fe, Pt and FeO can be obtained from the [Materials Project](https://materialsproject.org/).

### 4.1 Obtaining structures from the Materials Project

1. Open the [Materials Project website](https://materialsproject.org/).
2. Navigate to Materials Explorer.
3. Search for the desired element or compound, for example Ni, Fe, Pt or FeO.
4. Select the appropriate crystal structure.
5. Open the structure information page.
6. Select the export option and download the structure in CIF format.

The following crystal structures are required:

| Material | Structure to select                |
| -------- | ---------------------------------- |
| Ni       | Face-centred cubic (fcc)           |
| Fe       | Body-centred cubic (bcc), alpha-Fe |
| Pt       | Face-centred cubic (fcc)           |
| FeO      | Rocksalt structure                 |

Pt (https://next-gen.materialsproject.org/materials/mp-126)
Ni (https://next-gen.materialsproject.org/materials/mp-23)
Fe (https://next-gen.materialsproject.org/materials/mp-150)
FeO (https://next-gen.materialsproject.org/materials/mp-1274279)

For Ni and Pt, the conventional cubic fcc cell is useful because it contains four atoms. For bcc Fe, the conventional cubic cell contains two atoms.

**Important:** A crystallographic CIF does not necessarily contain the magnetic unit cell or magnetic ordering. In particular, an ordinary rocksalt FeO CIF does not automatically provide the AFM-II magnetic structure required for the final example. The magnetic cell must be constructed separately.




### 4.2 Installing pymatgen

Pymatgen (Python Materials Genomics) is an open-source Python library for analysing, manipulating and converting crystal structures.

It can be used to read CIF files, generate supercells, convert between structural formats and write VASP POSCAR files.

On an HPC system, it is recommended to install pymatgen in a separate Python environment rather than modifying the system Python installation.

For example, using Conda:

```bash
conda create -n pymatgen-env python=3.11 -y
conda activate pymatgen-env

conda install -c conda-forge pymatgen -y
```

Check that the installation was successful:

```bash
python -c "import pymatgen; print(pymatgen.__version__)"
```

Alternatively, pymatgen can be installed using pip:

```bash
python -m pip install pymatgen
```

The installation should preferably be performed on the HPC login node, subject to the cluster's software installation policies.

### 4.3 Converting CIF files to POSCAR using pymatgen

Once the CIF files have been downloaded, they can be converted to POSCAR format using pymatgen.

For example:

```python
from pymatgen.core import Structure
from pymatgen.symmetry.analyzer import SpacegroupAnalyzer

# Read the CIF file
structure = Structure.from_file("Ni.cif")

# Generate the conventional standard structure
conventional = SpacegroupAnalyzer(
    structure,
    symprec=0.01
).get_conventional_standard_structure()

# Export the structure as a POSCAR
conventional.to(
    filename="POSCAR",
    fmt="poscar"
)
```

The same procedure can be used for Fe and Pt.

For convenience, the following script converts all three structures:

```python
from pathlib import Path
from pymatgen.core import Structure
from pymatgen.symmetry.analyzer import SpacegroupAnalyzer

materials = ["Ni", "Fe", "Pt"]

for element in materials:

    # Read the downloaded CIF file
    structure = Structure.from_file(
        f"CIF/{element}.cif"
    )

    # Convert to the conventional standard structure
    conventional = SpacegroupAnalyzer(
        structure,
        symprec=0.01
    ).get_conventional_standard_structure()

    # Create an output directory
    output_dir = Path(element)
    output_dir.mkdir(exist_ok=True)

    # Write the POSCAR
    conventional.to(
        filename=str(output_dir / "POSCAR"),
        fmt="poscar"
    )

    print(f"{element}: {len(conventional)} atoms")
```

For the structures selected in this tutorial, the expected atom counts are:

* fcc Ni: 4 atoms.
* bcc Fe: 2 atoms.
* fcc Pt: 4 atoms.

Always inspect the generated POSCAR files to verify the number of atoms, lattice parameters and atomic positions.

### 4.4 Alternative conversion tools

CIF files can also be converted into POSCAR files using other crystallographic tools, including:

* **VASPKIT:** Provides structure-conversion functions for preparing VASP input structures.
* **qvasp:** Provides structure manipulation and format-conversion functionality, depending on the installed version.

The menu options may differ between versions, so consult the corresponding documentation.

Regardless of the conversion method, the resulting POSCAR should be inspected before beginning the calculation.

---

## 5. Calculation setup and computational parameters

A standard bulk VASP calculation requires four input files:

| File    | Purpose                                |
| ------- | -------------------------------------- |
| POSCAR  | Crystal structure and atomic positions |
| INCAR   | Calculation parameters                 |
| KPOINTS | Brillouin-zone sampling                |
| POTCAR  | PAW pseudopotentials                   |

### 5.1 Exchange-correlation functional and pseudopotentials

For this tutorial, the Perdew–Burke–Ernzerhof (PBE) generalised-gradient approximation (GGA) functional will be used.

The corresponding PBE PAW pseudopotentials must be selected.

Use the same pseudopotential variant throughout the magnetic-state comparisons for a given material. For new calculations, consult the [VASP recommended pseudopotentials](https://vasp.at/wiki/Recommended_pseudopotentials). The current recommendations include Fe_pv and Ni_pv for standard DFT calculations.

For a compound such as FeO, the POTCAR files must be concatenated in the same order as the elements appear in the POSCAR.

For example, if the POSCAR contains:

```text
Fe O
```

the POTCAR should contain the Fe potential followed by the O potential.

### 5.2 Energy cutoff

The plane-wave energy cutoff is controlled using:

```text
ENCUT = 520
```

The value of 520 eV is an illustrative starting value for this tutorial. The appropriate cutoff depends on the selected PAW potentials.

The ENMAX values can be found in the POTCAR files. The energy cutoff should be selected based on the largest ENMAX and subsequently tested for convergence.

When comparing magnetic configurations, it is particularly important to use the same ENCUT for every calculation.

### 5.3 K-point sampling

The KPOINTS file determines the sampling of the Brillouin zone.

For bulk metals, a relatively dense k-point mesh is needed because of the partially occupied electronic states near the Fermi level.

An example starting mesh for the conventional Ni, Fe and Pt cells is:

```text
K-Spacing Value to Generate K-Mesh: 0.030
0
Monkhorst-Pack
  10  10  10
0.0  0.0  0.0
```

This mesh is an initial choice, not a universally converged value. The k-point density must be tested to ensure that the total energies and magnetic moments are sufficiently converged.

For FeO, a separate mesh appropriate for its larger magnetic unit cell should be used.

### 5.4 Smearing

* Metallic systems require an appropriate treatment of partial electronic occupancies.
    * For structural relaxation of metals, the Methfessel–Paxton method is commonly used:

```text
ISMEAR = 1
SIGMA  = 0.2
```

* For more accurate static total-energy calculations, the tetrahedron method with Blöchl corrections can be used:

```text
ISMEAR = -5
```

* A Gamma-centred k-point mesh should be used with the tetrahedron method.

For the insulating FeO example, a small Gaussian smearing can be used during relaxation, followed by a tetrahedron calculation for the final static energy if the electronic structure is insulating.

See the [VASP documentation on smearing and k-point sampling](https://vasp.at/wiki/Number_of_k_points_and_method_for_smearing).

### 5.5 Structural relaxation

The following parameters can be used as a starting point for the elemental metals:

```text
IBRION = 2
ISIF   = 3
NSW    = 100
EDIFF  = 1E-6
EDIFFG = -0.02
```

* These settings allow the atomic positions and simulation-cell degrees of freedom to relax.

Each candidate magnetic configuration should be relaxed independently. The lattice parameters can change when the electronic magnetic state changes.

For accurate comparisons, the calculations should use consistent numerical settings, including ENCUT, k-point density, pseudopotentials and exchange-correlation functional.

* Note that bulk Pt and Pt nanoparticles or surfaces need not exhibit identical magnetic behaviour. The present calculation concerns the bulk fcc structure.

---

## 6. Example 1: Non-magnetic and ferromagnetic nickel

### 6.1 Task

Investigate the magnetic properties of face-centred cubic nickel by comparing non-spin-polarised and spin-polarised calculations.

By the end of this example, you should be able to:

* perform a non-spin-polarised calculation;
* perform a spin-polarised calculation;
* initialise a ferromagnetic magnetic configuration;
* extract the local magnetic moment of Ni;
* compare the total energies of different magnetic states.

Bulk nickel crystallises in the fcc structure and exhibits ferromagnetic behaviour below its Curie temperature.

### 6.2 Input files

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

### 6.3 Non-magnetic calculation

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

### 6.4 Ferromagnetic calculation

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


-------------------------------------------------------
###  Results
-------------------------------------------------------

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

    * The negative energy difference indicates that the FM configuration is energetically more favourable than the NM configuration. 

## 2. Total magnetic moment: 
   * The OSZICAR output gives:
```
1 F= ... mag= 2.4715
2 F= ... mag= 2.5048
3 F= ... mag= 2.4865
```

Which is the total magnetic moment of the four-atom simulation cell. 
* The average magnetic moment per Ni atom is therefore 2.4865/4 = 0.6216 μB
* This is a reasonable result for bulk ferromagnetic nickel.

## 3. Local magnetic moments

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

## Overall

* The results demonstrate that spin polarisation is important for describing bulk nickel. 
    * The ferromagnetic configuration has a lower energy than the non-magnetic configuration, with a total magnetic moment of approximately 2.49 μB per four-atom cell, corresponding to an average of 0.62 μB per Ni atom.
    * The calculated local magnetic moments are also consistent with the formation of ferromagnetic order, as all four Ni atoms exhibit positive magnetic moments of approximately 0.64 μB.

* This example demonstrates how spin-polarised DFT calculations can be used to investigate magnetic properties and determine whether allowing spin polarisation produces a more energetically favourable solution.

------------------------------------

## 7. Example 2: Magnetic configurations of iron
### 7.1 Task

* Investigate the magnetic properties of body-centred cubic iron by comparing: 
    * non-magnetic
    * ferromagnetic
    * antiferromagnetic

* Iron is a particularly useful example because its magnetic properties depend on its crystal structure and magnetic ordering.
    * Alpha-iron has a bcc structure (At ambient pressure).
    * Ferromagnetic (below its Curie temperature).

## 7.2 Preparing the bcc Fe structure

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

## 7.3 Non-magnetic configuration

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

## 7.4 Ferromagnetic configuration

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

## 7.5 Antiferromagnetic configuration

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
## 7.6 Analysis
-------------------------------------------------------

After completing the non-magnetic (NM), ferromagnetic (FM) and antiferromagnetic (AFM) calculations, compare the total energies and magnetic moments to determine the relative stability of the different magnetic configurations.

* The following results are extracted for the analysis: 
    1. Final total energy.
    2. Total cell magnetic moment.
    3. Local magnetic moments on each Fe atom.
    4. Lattice parameters of relaxed structures.

### 1. Total energy comparison

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

### 2. Total magnetic moments

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

### 3. Local magnetic moments

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

#### Overall

* The calculations demonstrate that spin polarisation is important for describing the magnetic properties of bcc iron. 
* Both the FM and AFM solutions are energetically more favourable than the non-magnetic solution. However, the FM configuration has the lowest total energy among the three configurations investigated.
* The calculated FM magnetic moment is approximately 2.17 μB per Fe atom, while the AFM configuration produces local moments of approximately ±1.28 μB per Fe atom.

    * This example demonstrates how different initial magnetic configurations can lead to different self-consistent electronic solutions. 
    * It also illustrates why both the total energy and the local magnetic moments must be examined when determining the magnetic properties of a material.

# 8. Example 3: Investigating magnetism in platinum (Self-Study)
## 8.1 Task

* Investigate the magnetic behaviour of bulk fcc platinum using non-spin-polarised and spin-polarised calculations.
* Bulk platinum is a useful comparison because it is not ferromagnetic under ordinary bulk conditions, despite having partially occupied d-derived electronic states.

## 8.2 Non-magnetic configuration

Obtain the fcc Pt structure from the Materials Project and prepare the POSCAR.

Use the following INCAR:
```
SYSTEM = fcc Pt non-magnetic

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

## 8.3 Spin-polarised calculation

To investigate whether the system develops a spontaneous magnetic moment, perform a spin-polarised calculation with small initial magnetic moments.

```
SYSTEM = fcc Pt spin-polarised

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
MAGMOM = 4*0.5

LORBIT = 11
LASPH  = .TRUE.

```

* The initial magnetic moments are deliberately small because the purpose is to test whether a finite spin polarisation is energetically sustained.

## 8.4 Analysis

* As with the previous examples:
    * Inspect the final magnetic moments in the OUTCAR file.
    * If spin-polarised calculation converges to very small local magnetic moments
        * The system has collapsed towards a non-magnetic solution within the chosen computational setup.

* This example demonstrates that setting ISPIN = 2 does not automatically produce a magnetic material. 
* The final magnetic solution is determined by the electronic structure and the self-consistent minimisation.


# 9. Example 4: Determining the magnetic moment of an unknown system – FeO
## 9.1 Task

Investigate the magnetic properties of iron(II) oxide (FeO) using spin-polarised DFT calculations.

In contrast to the elemental metals, FeO contains transition-metal cations coordinated by oxygen atoms. The local chemical environment and electron localisation can have a considerable influence on the magnetic moments.

This example demonstrates how to investigate a system when the magnetic moments and magnetic ordering are not known beforehand.

By the end of this example, you should be able to:

identify potentially magnetic atoms in a compound;
select reasonable initial magnetic moments;
construct a magnetic unit cell;
investigate ferromagnetic and antiferromagnetic arrangements;
extract local magnetic moments from the OUTCAR file;
compare magnetic configurations using DFT+U.

## 9.2 Background: magnetism in FeO

FeO has a rocksalt crystal structure and contains nominal Fe²⁺ ions.

The Fe²⁺ ion has a (3d^6) electronic configuration. In an octahedral coordination environment, Fe²⁺ can adopt a high-spin configuration with four unpaired electrons.

The corresponding spin-only magnetic moment is approximately:

[
\mu_{\mathrm{spin}} = g\sqrt{S(S+1)}\mu_{\mathrm B}
]

where (g) is approximately 2 and (S=2).

The spin-only effective paramagnetic moment is therefore approximately 4.90 μB. This should not be confused with the ordered magnetic moment, which is approximately 4 μB before accounting for covalency and other solid-state effects.

FeO exhibits antiferromagnetic ordering at low temperatures. The magnetic arrangement is commonly described as Type-II antiferromagnetism (AFM-II), in which ferromagnetic (111) planes are stacked with alternating spin directions.

For bulk wüstite, the Néel temperature is approximately 198 K. Real wüstite can also be non-stoichiometric, so the ideal stoichiometric FeO model used here represents a simplified material.

## 9.3 Preparing the FeO magnetic structure

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


The lattice parameter is an illustrative starting value in Å. It should be refined through structural relaxation.

The atom order in this POSCAR is:

Atom	Species	Initial position
1	Fe	(0, 0, 0)
2	Fe	(0.5, 0.5, 0.5)
3	O	(0.25, 0.25, 0.25)
4	O	(0.75, 0.75, 0.75)

This magnetic cell is adapted from the rhombohedral AFM-II cell used in the VASP tutorial for NiO.

## 9.4 Why use DFT+U?

The localised 3d electrons in transition-metal oxides can be difficult to describe accurately using conventional GGA functionals such as PBE.

The self-interaction error associated with standard approximate exchange-correlation functionals can lead to excessive delocalisation of d electrons.

DFT+U introduces an additional on-site correction to improve the description of localised electronic states.

For this example, we use the Dudarev formulation, where only the effective parameter (U_{\mathrm{eff}}=U-J) enters the correction.

The following is an illustrative INCAR setup using (U_{\mathrm{eff}}=4.0) eV for Fe 3d orbitals. This value is a starting point rather than a universal parameter. The appropriate U value depends on the chosen functional, PAW potential and property being investigated.

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

The order of the entries in LDAUL, LDAUU and LDAUJ must correspond to the species order in POSCAR and POTCAR.

Since the POSCAR contains Fe followed by O, the Hubbard correction is applied to the Fe d orbitals, while no correction is applied to oxygen.

## 9.5 Investigating different magnetic configurations

To investigate the magnetic state of FeO, perform calculations with different initial magnetic configurations.

It is important to use the same structure, lattice parameters, pseudopotentials, energy cutoff, k-point mesh and DFT+U parameters when comparing the initial magnetic states.

Prepare three separate directories:

FeO/
├── NM/
├── FM/
└── AFM-II/

Each directory should contain the same POSCAR, POTCAR and KPOINTS files. Only the magnetic settings in INCAR should differ.

Calculation A: Non-magnetic configuration

For the non-spin-polarised calculation:

ISPIN = 1

Remove the MAGMOM parameter.

Keep the same DFT+U settings if the purpose is to compare the magnetic states within the same DFT+U approximation.

Calculation B: Ferromagnetic configuration

For the ferromagnetic calculation, initialise both Fe atoms with positive magnetic moments:

ISPIN  = 2
MAGMOM = 2*5.0 2*0.0

Both Fe atoms initially have parallel magnetic moments, while the oxygen atoms start with zero magnetic moments.

Calculation C: Antiferromagnetic AFM-II configuration

For the AFM-II calculation, initialise the two Fe atoms with opposite magnetic moments:

ISPIN  = 2
MAGMOM = 5.0 -5.0 2*0.0

This initialises the Fe atoms with antiparallel magnetic moments, while the oxygen atoms start with zero magnetic moments.

The initial moments of 5 μB are starting guesses. The final local moments are determined self-consistently.

## 9.6 Analysis of the magnetic moments

After completing the calculations, inspect the OUTCAR files.

Search for:

magnetization (x)

The final table contains the atom-resolved magnetic moments.

For example, an AFM solution may produce a result similar to:

Atom	Species	Local magnetic moment
1	Fe	+3.6 μB
2	Fe	−3.6 μB
3	O	Small induced moment
4	O	Small induced moment

These values are illustrative and are not predetermined VASP results.

An antiferromagnetic configuration can have a total magnetic moment close to zero while retaining substantial local moments on the Fe atoms.

Compare the converged total energies of the NM, FM and AFM-II calculations to determine which configuration is most favourable among those investigated.

## 9.7 Important considerations

The magnetic moment obtained from DFT depends on several factors:

The exchange-correlation functional.
The PAW pseudopotential.
The Hubbard U parameter.
The crystal structure and local coordination.
The initial magnetic configuration.
The magnetic ordering represented by the simulation cell.

Consequently, a single spin-polarised calculation is not sufficient to establish the magnetic ground state of an unknown material.

It is recommended to investigate several reasonable initial magnetic moments and magnetic configurations before drawing conclusions.

For FeO, the AFM-II configuration is a physically motivated starting point based on its known low-temperature magnetic structure. However, other magnetic arrangements may also be investigated to assess their relative stability.

## 10. Extracting and comparing magnetic properties

After the calculations have completed, VASP provides information about the magnetic properties in the OUTCAR and OSZICAR files.

## 10.1 Total magnetic moment

The total cell magnetisation can be extracted from the OSZICAR file.

grep "mag=" OSZICAR | tail -1

The value following mag= represents the total magnetic moment of the simulation cell.

For a compensated AFM configuration, this value should be close to zero.

## 10.2 Local magnetic moments

The LORBIT = 11 parameter produces atom-resolved magnetic moments in the OUTCAR file.

Search for:

grep -n "magnetization (x)" OUTCAR

The final magnetization (x) table contains the local projected magnetic moments of the atoms.

A typical table is:

magnetization (x)

# of ion       s       p       d       tot
------------------------------------------
    1       ...     ...     ...     ...
    2       ...     ...     ...     ...
    3       ...     ...     ...     ...
    4       ...     ...     ...     ...
------------------------------------------
tot         ...     ...     ...     ...

The tot column gives the projected magnetic moment for each atom.

Important: The sum of the local projected moments is not necessarily equal to the total cell magnetic moment. This is because the local moments are integrated within the PAW projection spheres, while the total cell magnetisation also includes contributions from the interstitial region.

## 10.3 Comparing total energies

To compare different magnetic configurations, extract the final total energy from the OUTCAR:

grep "free  energy   TOTEN" OUTCAR | tail -1

The total energies should only be compared directly when the calculations use the same composition and equivalent computational settings.

If the simulation cells contain different numbers of formula units, normalise the energies accordingly.

For two configurations, the energy difference can be calculated as:

[
\Delta E = E_{\mathrm{AFM}}-E_{\mathrm{FM}}
]

If (\Delta E) is positive, the FM configuration has a lower energy. If it is negative, the AFM configuration has a lower energy.

This comparison should be performed after the calculations have converged and the candidate structures have been treated consistently.

## 10.4 Summary of the calculations

The following table summarises the purpose of each example.

System	Calculation	Main objective
Ni	NM vs FM	Demonstrate ferromagnetism in a bulk metal
Fe	NM vs FM	Investigate the ferromagnetic state
Fe	FM vs AFM	Compare competing magnetic arrangements
Pt	NM vs spin-polarised	Demonstrate a system that may converge to a non-magnetic solution
FeO	NM vs FM vs AFM-II	Determine local magnetic moments and investigate magnetic ordering

The results of these calculations provide a foundation for further investigations of magnetic materials using DFT.

In the next part of this tutorial, the electronic density of states (DOS), projected density of states (PDOS) and electronic band structures of spin-polarised systems will be investigated.

# 11. Questions
## 11.1 Introduction to magnetism
What is the difference between a spin-polarised and a non-spin-polarised DFT calculation?
What is the difference between ferromagnetic and antiferromagnetic ordering?
Why is a larger simulation cell sometimes required for an antiferromagnetic calculation?
Does setting ISPIN = 2 guarantee that the final system will have a non-zero magnetic moment?

## 11.2 VASP input parameters
What is the purpose of the ISPIN parameter?
What information is specified using MAGMOM?
Why must the MAGMOM values follow the atomic order in POSCAR?
What is the purpose of LORBIT = 11?
Why should the initial magnetic configuration be retained when restarting a spin-polarised calculation?

## 11.3 Magnetic calculations
Why is bulk Ni a suitable example for investigating ferromagnetism?
Why does bulk Pt provide a useful non-magnetic reference?
What is the difference between the local magnetic moment of an atom and the total magnetic moment of the simulation cell?
Why can an antiferromagnetic system have a total magnetic moment close to zero while individual atoms have large magnetic moments?
Why should the total energies of different magnetic configurations be compared using consistent computational settings?

## 11.4 Investigating an unknown magnetic system
Why is it necessary to test several initial magnetic configurations when investigating an unknown material?
Why are the Fe 3d electrons in FeO treated using DFT+U?
What is the purpose of the LDAUTYPE = 2 setting?
Why is LDAUL = 2 -1 used for FeO?
How would you determine whether the converged FeO structure is ferromagnetic or antiferromagnetic?
Why is the initial magnetic moment not necessarily equal to the final magnetic moment obtained from VASP?



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