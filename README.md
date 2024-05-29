# Metal-Diffusion-in-Galaxy-clusters
Simulation of metal diffusion in the ICM for the Perseus Cluster based on the paper 'Impact of stochastic gas motions on galaxy cluster abundance profiles' by Rebusco et., al 2005. The aim of the project is to show the possible parameters for efficient metal distribution under reasonable diffusion timescales.(reproduce observed abundance profile). 

# Introduction to the relevant astrophysical scenario
Galaxy clusters are the most massive virialized systems in the universe. they consist of $10^2 - 10^3$ galaxies which orbit inside the gravitational well of the gas and Dark matter halo which takes up more then $80 \%$ of the total mass of the system. Given that the total mass is up to $10^{15} M_\odot$, the velocity dispersion of galaxies within the cluster is of the order of $\sigma \sim 10^3 km/s$ and the gravitational radius ranges from 1 to 3 Mpc. The aim of this project is the creation of a reliable model which can numerically recreate the observational data related
to  the iron abundance profile in the \textbf{Perseus Cluster}. Iron is injected from the BCG by supernova activity and stellar winds and subsequently diffused by stochastic gas motions in the ICM. In order to archive such goal the model is modified accordingly and its parameters vary to generate a progressively more realistic scenario.
Figure 1 shows the deprojected iron abundance of the Perseus cluster extracted from deep \textit{XMM-Newton} data, which is expressed as:

$$  Z_{Fe}(r)=0.3  \frac{2.2 + \left( \frac{r_{kpc}}{80}\right)^3}{1+ \left(\frac{r_{kpc}}{80}\right)^3} Z_{Fe\odot}$$

in the code it is multiplied by a factor 1.4 and the background abundance $Z_{Fe,out}=0.4 \,Z_{Fe\odot}$ is subtracted from all of the plots.

![Alt text](plots/zfe_totti5.png?raw=true)
