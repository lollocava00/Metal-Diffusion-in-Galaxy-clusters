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

![Alt text](plots/masse.png?raw=true)

## Gas density profile
Once we have $M_{DM}(r)$ we can integrate the Hydrostatic equilibrium equation to obtain the density profile of the intra cluster medium gas. For an ideal gas the pressure can be expressed as 

$$  P=\frac{k_b\rho T}{\mu m_p}$$

where $k_b$ is the Boltzmann constant, $\mu$ is the mean molecular weight and $m_p$ is the mass of the proton. Given that the gas is at constant temperature ($T=8.9 \cdot 10^7 K$ ) we can insert the Equation of state in equation 1 to obtain an expression for the gas density

$$    \frac{dln\rho_g}{dr}=-\frac{G M(r)}{r^2}\frac{\mu m_p}{k_b T}.$$

such ODE can be solved as an FDE - Finite Differences Equation:

 $$   ln \rho_{j+\frac{1}{2}}=ln \rho_{j-\frac{1}{2}} -\Delta r \frac{G M_j}{r_j^2}\frac{\mu m_p}{k_b T}$$

setting the initial value for $\rho_j$ becomes extremely important in order to reach the **cosmological barion fraction** of $f_b \sim 0.16$ at the virial radius, where 

$$    f_b=\frac{M_{gas}(r)}{M_{gas}(r)+M_{DM}(r)}.$$

In this first approximation I set:

    \rho_0=4.46\cdot 10^{-26} g/cm^3.    



![Alt text](plots/density6.png?raw=true)

The green line in the plot above stands for the corresponding analytical solution:

$$  \rho_g(r)=\rho_0 e^{-27b/2} \left(1+\frac{r}{r_s} \right)^{27b/(2r/r_s)}$$

where $b=\frac{8\pi G \mu m_p \rho_{DM,0} r_s^2}{27k_b T}$ .

## addition of a BCG and a temperature gradient
I add the density contribution of a central elliptical galaxy whose stellar mass profile follows the \textbf{1990 Hernquist profile} defined as

$$ M_\ast (r)=M_{BCG}\frac{r^2}{(r+a)^2}$$

where $(1+\sqrt{2})a=12 kpc$ and $M_{BCG}= 10^{12} M_{\odot}$ is the Mass of the brightest cluster galaxy(BCG) which is thought to have the highest impact when it comes to the stellar mass of the cluster. 

In an effort to upgrade the model to a more realistic version I introduced the following temperature profile:

 $$   \frac{T(r)}{T_{mg}}=1.35\frac{(\frac{x}{0.045})^{1.9}+0.45}{(\frac{x}{0.045})^{1.9}+1}\frac{1}{(1+(\frac{x}{0.6})^2)^{0.45}}$$

where $x=\frac{r}{r_{500}}$ ,  $r_{500} \sim 1.4 Mpc$ and $T_{mg}=8.9\cdot 10^7 K$ are normalization constants.
Since now temperature is no more constant we need to account for it in the Hydrostatic equilibrium equation which becomes 

$$   ln \rho_{j+\frac{1}{2}}=ln \rho_{j-\frac{1}{2}} -\Delta r \frac{G M_j}{r_j^2}\frac{\mu m_p}{k_b T_{j+\frac{1}{2}}}-(lnT_{j+1}-lnT_{j-1})$$

I set the initial density :

$$    \rho_0=2.07 \cdot 10^{-25}$$

in order to reach the cosmological barion fraction $f_b \sim 0.16$.
The overall trend for the initial gas density given the baryon fraction condition throughout all the possible cases is an increase; this is due to the addition of a central object, which causes an intrinsic baryon fraction decrease, and a temperature gradient which, in virtue of it being lower in the central regions, requires a higher initial gas density.

![Alt text](plots/density4.png?raw=true)

![Alt text](plots/temp.png?raw=true)

The temperature and density profiles that are being used are in accordance to those presented in the Rebusco et. al (2005) paper which are the ones based on the deprojected XMM-Newton data.


