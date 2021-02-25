# Free-Energy-Plots

The free energy landscapes are very useful to interprete and analyse the biomolecular processes such as molecular folding, aggreagation and recognition. Molecule's free energy landscape can be calculated by the following formula
\
<img src="https://render.githubusercontent.com/render/math?math=\Delta G=-k_BTlnP(CV_1,CV2)"> 
\
where __*k<sub>B</sub>*__ and __*T*__ are the Boltzmann constant and absolute temperature, respectively, and __*P(CV<sub>1</sub>,CV<sub>2</sub>)*__ is the probability distribution of the molecular system along the reaction coordinates/ collective variables (_CV<sub>1</sub>_ and _CV<sub>2</sub>_). There is number of possibilities on selection of reaction coordinates, which includes contact distances between two atoms, the RMSD, the radius of Gyration, the angle, the dihedral angle, PCA, etc.

From MD simulation, we can get calculate data, lets assume PC1 and PC2.
How to calculate PCA from Gromacs MD?
```sh
gmx anaeig -f mdfit.xtc -s md.gro -v eigenvectors.trr -last 1 -proj pc1.xvg
gmx anaeig -f mdfit.xtc -s md.gro -v eigenvectors.trr -frist 2 -last 2 -proj pc2.xvg
```
