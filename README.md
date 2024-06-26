# Martinoid Peptoid CG

Originally written as part of the publication title "Martinoid: The Martini Peptoid Force Field", published in ***Phys. Chem. Chem. Phys.***. 

https://pubs.rsc.org/en/content/articlehtml/2024/cp/d3cp05907c

This module was inspired by martinize (http://cgmartini.nl/index.php/tools2/proteins-and-bilayers/204-martinize) and has been created to perform automatic topology building of peptoids within the MARTINI forcefield (v2.1) in the GROMACS program.

A key difference between Martinoid and Martinize is that the former does not require an input all-atom peptoid structure while Martinize does. This has obvious advantages but does mean the output guessed CG structures [generally] require a greater degree of minimization.

## Installation
```bash
pip install -U Martinoid
```
or
```bash
git clone https://github.com/Hamish-cmyk/MartinoidPeptoidCG
cd MartinoidPeptoidCG
pip install .
```

## Usage
### As a standalone program
The primary argument passed is the peptoid sequence, this must be in the correct format according to the Glasgow convention (https://doi.org/10.17868/strath.00085559).

Martinoid can either be used directly from the terminal once installed:
For example:
```bash
python -m Martinoid --seq "Na-Nt-Nfe"
```
Which will output two files: Na-Nt-Nfe.itp & Na-Nt-Nfe.pdb

The topology (Na-Nt-Nfe.itp) looks like:
```erlang
[ moleculetype ]
; Name         Exclusions
Peptoid   1

[ atoms ]
	1	Qd	1	Nx	BB	 1  1.0 ; 
	2	Na	2	Nt	BB	 2  0.0 ; 
	3	SP1	2	Nt	SC1	 3  0.0 ; 
	4	Nda	3	Nfe	BB	 4  0.0 ; 
	5	SC1	3	Nfe	SC1	 5  0.0 ; 
	6	SC5	3	Nfe	SC2	 6  0.0 ; 
	7	SC5	3	Nfe	SC3	 7  0.0 ; 
	8	SC5	3	Nfe	SC4	 8  0.0 ; 

[ bonds ]
	1	2	1	0.34		8515.0 ; BB-BB
	2	3	1	0.333		6627.0 ; BB-SC1
	2	4	1	0.34		8515.0 ; BB-BB
	4	5	1	0.298		7638 ; BB-SC1
	5	6	1	0.234		9500 ; SC1-SC2

[ constraints ]
	6	7	1	0.27 ; SC2-SC3
	7	8	1	0.27 ; SC3-SC4
	8	6	1	0.27 ; SC4-SC2

[ angles ]
	1	2	4	2	138.0	50.0 ; BB-BB-BB
	4	5	6	2	147	37 ; BB-SC1-SC2
	5	6	7	8	0	1 ; DW_Potential
	5	6	8	8	0	1 ; DW_Potential
	3	2	4	2	110	20 ; SC1-BB-BB
	5	4	2	2	65	80 ; SC1-BB-BB

[ dihedrals ]
	5	7	8	6	2	0	50 ; BB-SC2-SC3-SC1 

[ exclusions ]
	5 7
	5 8
}
```

The coordinates (Na-Nt-Nfe.pdb) has a guess structure which looks like (which is not a terible guess and can easily be minimized during the gromacs minimization step):

![alt text](images/Na-Nt-Nfe.png "Na-Nt-Nfe.pdb")

### As a Python module
Martinoid can be used as module in python which can make screening large numbers of peptoids in different simulation settings easier.

After installing Martinoid, simply import the module and pass the sequence and arguments. For instances if we wanted to make a helical sequence of Nf-Nfe-Nq-Nm-NmO-Nv-Nv-Nv with a neutral N-terminus:

```python
import Martinoid

peptoid = Martinoid.Martinoid(sequence = "Nf-Nfe-Nq-Nm-NmO-Nv-Nv-Nv", N_ter_charged=True, SS="Helical")
```

# Running Simulations

Martinoid uses a custom angle parameter to account for flexible ethyl sidechains (e.g., Nfe). For this reason you must include this .xvg file when you call mdrun.

```bash
gmx mdrun -tableb angle5_a0.xvg -deffnm Run
```

![alt text](images/CustomAngle.png "Custom Angle Parameter")
