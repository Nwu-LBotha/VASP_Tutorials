# VASP Hands-on: Bulk Magnetism

# Part 1: Magnetic properties of bulk metals and transition-metal oxides

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
    * Each candidate magnetic configuration should be relaxed independently. 
    * The lattice parameters can change when the electronic magnetic state changes.

## Summary of the Example calculations

The following table summarises the purpose of each example.

System	Calculation	Main objective
Ni	NM vs FM	Demonstrate ferromagnetism in a bulk metal
Fe	NM vs FM	Investigate the ferromagnetic state
Fe	FM vs AFM	Compare competing magnetic arrangements
Pt	NM vs spin-polarised	Demonstrate a system that may converge to a non-magnetic solution
FeO	NM vs FM vs AFM-II	Determine local magnetic moments and investigate magnetic ordering

The results of these calculations provide a foundation for further investigations of magnetic materials using DFT.

In the next part of this tutorial, the electronic density of states (DOS), projected density of states (PDOS) and electronic band structures of spin-polarised systems will be investigated.

# 6. Questions
## 6.1 Introduction to magnetism
What is the difference between a spin-polarised and a non-spin-polarised DFT calculation?
What is the difference between ferromagnetic and antiferromagnetic ordering?
Why is a larger simulation cell sometimes required for an antiferromagnetic calculation?
Does setting ISPIN = 2 guarantee that the final system will have a non-zero magnetic moment?

## 6.2 VASP input parameters
What is the purpose of the ISPIN parameter?
What information is specified using MAGMOM?
Why must the MAGMOM values follow the atomic order in POSCAR?
What is the purpose of LORBIT = 11?
Why should the initial magnetic configuration be retained when restarting a spin-polarised calculation?

## 6.3 Magnetic calculations
Why is bulk Ni a suitable example for investigating ferromagnetism?
Why does bulk Pt provide a useful non-magnetic reference?
What is the difference between the local magnetic moment of an atom and the total magnetic moment of the simulation cell?
Why can an antiferromagnetic system have a total magnetic moment close to zero while individual atoms have large magnetic moments?
Why should the total energies of different magnetic configurations be compared using consistent computational settings?

## 6.4 Investigating an unknown magnetic system
Why is it necessary to test several initial magnetic configurations when investigating an unknown material?
Why are the Fe 3d electrons in FeO treated using DFT+U?
What is the purpose of the LDAUTYPE = 2 setting?
Why is LDAUL = 2 -1 used for FeO?
How would you determine whether the converged FeO structure is ferromagnetic or antiferromagnetic?
Why is the initial magnetic moment not necessarily equal to the final magnetic moment obtained from VASP?