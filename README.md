# Metal-Diffusion-in-Galaxy-clusters
Simulation of metal diffusion in the ICM for the Perseus Cluster based on the paper 'Impact of stochastic gas motions on galaxy cluster abundance profiles' by Rebusco et., al 2005. The aim of the project is to show the possible parameters for efficient metal distribution under reasonable diffusion timescales.(reproduce observed abundance profile). 

# Introduction to the relevant astrophysical scenario
Galaxy clusters are the most massive virialized systems in the universe. they consist of $10^2 - 10^3$ galaxies which orbit inside the gravitational well of the gas and Dark matter halo which takes up more then $80 \%$ of the total mass of the system. Given that the total mass is up to $10^{15} M_\odot$, the velocity dispersion of galaxies within the cluster is of the order of $\sigma \sim 10^3 km/s$ and the gravitational radius ranges from 1 to 3 Mpc. The aim of this project is the creation of a reliable model which can numerically recreate the observational data related
to  the iron abundance profile in the \textbf{Perseus Cluster}. Iron is injected from the BCG by supernova activity and stellar winds and subsequently diffused by stochastic gas motions in the ICM. In order to archive such goal the model is modified accordingly and its parameters vary to generate a progressively more realistic scenario.
Figure 1 shows the deprojected iron abundance of the Perseus cluster extracted from deep \textit{XMM-Newton} data, which is expressed as:

$$  Z_{Fe}(r)=0.3  \frac{2.2 + \left( \frac{r_{kpc}}{80}\right)^3}{1+ \left(\frac{r_{kpc}}{80}\right)^3} Z_{Fe\odot}$$

in the code it is multiplied by a factor 1.4 and the background abundance $Z_{Fe,out}=0.4 \,Z_{Fe\odot}$ is subtracted from all of the plots.

![Alt text](plots/zfe_totti5.png?raw=true)

## Hydrostatic equilibrium
The first requirement for the model is a condition of hydrostatic equilibrium:

 $$   \frac{dP}{dr}=-\frac{GM(r)}{r^2}\rho(r)$$

The term $M(r)$ represents the mass of the cluster within the radius r, given that we assume the structure to be spherical; P and $\rho$ are the pressure and density respectively. This equation is a first order ODE that we will need to integrate numerically to obtain the density profile of the gas within the cluster.

### Staggered grid
In order to integrate the Hydrostatic equilibrium equation we first need to discretize the radial distance $r$ from the center of the cluster, in fact we are considering the system to be in a spherical symmetry reducing the spacial dimensions of the problem from 3 to 1. I use a system of **staggered grids**. The first grid is defined as

$$    r_j=r_{min}+(j-1)\frac{r_{max}}{j_{max}-1}$$

where $r_{min}=0kpc$ and $r_{max}=3000kpc$ (just above the virial radius) while $j_{max}$ is the parameter that sets the smallest possible scale $\Delta r$ of the system. I decided to set $j_{max}=5000$. I used a second, superimposed grid in the middle of the first one 

$$     r_{j+\frac{1}{2}}=r_j+\frac{1}{2}(r_{j+1}-r_j)$$

as to set an average value for each shell of volume

$$   \Delta V_{j+\frac{1}{2}}=\frac{4}{3}\pi (r_{j+1}-r_j)^3$$

The values of the density will be computed in this second grid. This is useful for numerical methods, density values are defined at the center($r_{j+\frac{1}{2}}$ grid) of small 'control volumes' ($r_j$ grid) and are interpreted as the mean value inside that cell. This 'more physical' way of thinking actually provides for better approximations of the PDEs.

## Dark matter
To integrate the Hydrostatic equilibrium equation we need a mass profile $M(r)$ that is generating the gravitational potential. Since the cluster's mass is dominated by the dark matter halo contribution, as a first approximation DM is the only component generating the gravitational potential. We therefore consider the density profile for DM to be that of **Navarro-Frenk-White (NFW profile)**:

$$    \rho_{DM}(r)=\frac{\rho_{DM,0}}{(\frac{r}{r_s})(1+\frac{r}{r_s})^2}$$

where the central dark matter density is $\rho_{DM,0}=7.35\cdot 10^{-26} g/cm^3$ and the scale radius is $r_s=435.7 kpc$.
The dark matter mass value for each shell is then given by

$$    \Delta M_{DM,j+\frac{1}{2}}=\rho_{j+\frac{1}{2}} \cdot \Delta V_{j+\frac{1}{2}}$$

which can be compared to the analytical solution

 $$   M_{DM}(r)=4\pi \rho_{DM,0} r_s^3 \left[ ln \left( 1+\frac{r}{r_s} \right) -\frac{r}{r+r_s}\right]$$

![Alt text](plots/masse (2).png?raw=true)

