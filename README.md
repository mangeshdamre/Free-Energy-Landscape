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
```sh
gnuplot <<EOL
reset
set terminal pngcairo  background "#ffffff" enhanced font "Times-New-Roman-Bold,20" fontscale 1.0 size 1280, 720 
set key on b c outside horizontal
set output 'FEL.png'
set title "Free Energy Landscape"
set xlabel "PC1 (\305)" rotate parallel
set ylabel "PC2 (\305)" rotate parallel
set zlabel "Gibbs Free Energy" rotate parallel
set grid
unset key
set pm3d implicit at b
set view map scale 1
unset surface
set dgrid3d $d1,$d2
set hidden3d
sp 'free-energy-landscape.dat' u 1:2:3 w l
EOL
```
