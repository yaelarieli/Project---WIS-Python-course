# Calculate adiabatic fraction in a cloud

This project was originally implemented as part of the [Python programming course](https://github.com/szabgab/wis-python-course-2024-04) at the [Weizmann Institute of Science](https://www.weizmann.ac.il/) taught by [Gabor Szabo](https://szabgab.com/)

This project is a program that calculates the adiabatic fraction (AF) in a cloud.


### Short introduction

The cloud can be described as a mixture of undiluted – adiabatic parts that do not exchange energy and material with the surroundings and non-adiabatic parts that mix with the environment's air. Hence, the adiabatic fraction (AF) is a measure of adiabaticity, and we can use it as a measure of mixing. The AF is defined as $LWC/LWC_{ad}$, where $LWC_{ad}$ is the liquid water content that a parcel would gain while being lifted adiabatically from the cloud base. [Eytan at el. (2021)](https://acp.copernicus.org/articles/21/16203/2021/acp-21-16203-2021.html) tested different methods to calculate AF values by comparing them to an Eulerain passive tracer. The method found to be the most accurate follows the analytical derivation of [Korolev and Mazin (2003)](https://journals.ametsoc.org/view/journals/atsc/60/24/1520-0469_2003_060_2957_sowvic_2.0.co_2.xml?tab_body=abstract-display) of the supersaturation in an adiabatic ascending parcel while changing the time domain to vertical coordinates (and changing $q_w = \frac{LWC}{\rho_d}$).

(1)  $\frac{dlog(S+1)}{dz}=A_1-A_2\frac{dLWC}{dz}$  


(2)  $A_1=\frac{g}{T}(\frac{L_w}{c_pR_vT}-\frac{1}{R_a})$  

(3)  $A_2=\frac{1}{\rho_v}+\frac{L_w^2}{c_pR_vT^2\rho_d}$   

where $S$ is the [supersaturation](https://geo.libretexts.org/Bookshelves/Meteorology_and_Climate_Science/Practical_Meteorology_(Stull)/07%3A_Precipitation_Processes/7.01%3A_Supersaturation_and_Water_Availability), $g$ is the gravity acceleration, $T$ is the temperature, $L_w$ is the latent heat of water evaporation, $c_p$ is the specific heat of air at constant pressure, $R_v$ and $R_a$ are the specific gas constants of water vapor and dry air, respectively, and  $\rho_v$ and $\rho_d$ are the respective density of water vapor and dry air.
Since these equations do not include mixing effects, we can consider that $LWC = LWC_{ad}$. Additionally, for $S<<1$, Eq.1 can be approximated to

$LWC_{ad}(z)\approx \int_{z_0}^{z'}\frac{A_1(z')}{A_2(z')}dz'-\int_{z_0}^{z}\frac{1}{A_2(z')}\frac{dS}{dz'}dz'$

where $z_0 = 0$ at the cloud base.

For the calculation of $LWC_{ad}$, there is a need for thermodynamic profiles (temperature $T(z)$ and specific humidity $q_v(z)$ ).  These profiles can be taken from the regions that are considered to be the most adiabatic; hence, the temperature and humidity profiles of the average of the points with the highest $5$% updraft values at each level.

# Technicalities
## Inputs
The required input for this program is a NetCDF file containing data from an LES simulation of a [cloud](https://journals.ametsoc.org/view/journals/atsc/61/24/jas-3350.1.xml). 

The necessary fields are:

* Vertical velocity [m/s] - **w**
* Non-precipitating water content [g/kg]- **Qn**
* Rain water content [g/kg]- **Qp**
* Liquid mixing ration [g/kg] - **Qc = Qp+Qn**
* Vapor mixing ratio [g/kg] - **Qv**
* Temperature [K] - **T**
* Pressur [pascal] - **prs**
* Relative Humidity [%] - **RH**
* Level axis - **z**

**z** need to be a 1D vector, all the rest of the field can be 2D matrix.


The constants are :

* Gravity [m/s] : $g=9.8$
* Gas constant for dry air: $R_a=287$
* Gas constant for water vapor: $R_{v}=461$ 
* Heat capacity [J/kg/K]: $c_p=1000$
* Latent heat [J/kg]: $L_w= 2.5e6$

## Install the dependencies
The requirement installations are in `requirements.txt`
```
pip install -r requirements.txt
```

## Tests the program
To test the program run:
```
pytest
```

## Run the program
The main program is contained in the `project.py` file, while the required functions are defined in `func_for_project.py`.
The program will provide a figure of a cross-section of the AF field.

To run the program, you need to provide the input file name in the command line:
```
python project.py INPUT_FILE [OPTIONS]
```
Replace INPUT_FILE with the path to your netCDF file. You can use the file in the folder called `data_for_AFcalc.nc`.

### Options
`--CloudBase` or `-Zb`: Index of cloud base height. The default value set to 60.

`--CorePrecentage` or `-pact`: Percentage of the highest values in the core variable. The default value is set to 95.



