# Free-Energy-Plots

The free energy landscapes are very useful to interprete and analyse the biomolecular processes such as molecular folding, aggreagation and recognition. Molecule's free energy landscape can be calculated by the following formula
\
<img src="https://render.githubusercontent.com/render/math?math=\Delta G=-k_BTlnP(CV_1,CV2)"> 
\
where __*k<sub>B</sub>*__ and __*T*__ are the Boltzmann constant and absolute temperature, respectively, and __*P(CV<sub>1</sub>,CV<sub>2</sub>)*__ is the probability distribution of the molecular system along the reaction coordinates/ collective variables (_CV<sub>1</sub>_ and _CV<sub>2</sub>_). There is number of possibilities on selection of reaction coordinates, which includes contact distances between two atoms, the RMSD, the radius of Gyration, the angle, the dihedral angle, PCA, etc.

From MD simulation, we can get calculate data, lets assume PC1 and PC2.
How to calculate PCA from Gromacs MD?
1. Calculate covariance matrix and calculate the eigenvectors and eigenvalues.
```sh
gmx covar -s md.gro -f mdfit.xtc -o eigenvalues.xvg -v eigenvectors.trr -xpma covapic.xpm
```
2. Calculate PC1 and PC2
```sh
gmx anaeig -f md.xtc -s md.gro -v eigenvectors.trr -last 1 -proj pc1.xvg
gmx anaeig -f md.xtc -s md.gro -v eigenvectors.trr -frist 2 -last 2 -proj pc2.xvg
```
3. Concatenate PC1 and PC2 in one file.
```sh
paste pc1.xvg pc2.xvg  | awk '{print $1, $2, $4}' > PC1PC2.xvg
```
4. Calculate Gibbs Free Energy.
```sh
gmx sham -f PC1PC2.xvg -ls FES.xpm
```
5. Convert .xpm file to .dat file
```sh
python2.7 xpm2txt.py -f FES.xpm -o free-energy-landscape.dat
```
7. Plot FEL
```py
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
# Read file
data=pd.read_csv( 'free-energy-landscape.dat' ,sep = '\t' ,header = None )
fesvalue=np.array(data[ 2 ])
# Because the default is 32*32=1024, it is rounded to 32*32 grid points, and the horizontal and vertical coordinates can be discarded, because they are sequential and have little meaning.
plotvalue=np.reshape(test,( 32 , -1 )
# Drawing
plt.figure(figsize=( 8 , 8 ))
plt.imshow(newtest,cmap = 'jet' ,interpolation = 'bilinear' )
plt.xticks([],[])
plt.yticks([],[])
plt.xlabel( 'PC1' ,fontsize = 16 )
plt.ylabel( 'PC2' ,fontsize = 16 )
#plt.show()
plt.savefig( 'fel.png' ,dpi = 600 )
```
