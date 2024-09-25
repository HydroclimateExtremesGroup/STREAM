# The Space-Time Rainfall Error and Autocorrelation Model (STREAM)

# Introduction

The Space-Time Rainfall Error and Autocorrelation Model (STREAM) provides an efficient way to reproduce the space-time correlation structure of precipitation fields in a randomly generated uniform noise field. This correlation structure can be useful for a number of applications, including generating precipitation fields based on expected errors in an imperfect precipitation product and post-processing precipitation fields with a known error structure (as in En-GARD).

STREAM was developed to combine space-time correlation structures of satellite precipitation fields with a pixel scale precipitation error model to generate precipitation ensembles that can “bracket” the magnitude and replicate the correlation structure of ground reference (i.e. true) rainfall. STREAM was developed and evaluated for a study area in the central U.S. using the NASA satellite precipitation product, IMERG-Early, at an hourly 0.1º scale. For more information about STREAM methodology, see Hartke et al. 2022.

Hartke, S. H., Wright, D. B., Li, Z., Maggioni, V., Kirschbaum, D. B., & Khan, S. (2022). Ensemble representation of satellite precipitation uncertainty using a nonstationary, anisotropic autocorrelation model. Water Resources Research, 58, e2021WR031650. https://doi.org/10.1029/2021WR031650 


# Layout
There are two src files: to simulate spatiotemporally correlated noise fields using a given precipitation field and associated wind fields:
- STREAM_Utils.py
- STREAM_PrecipSimulation.py

A config file where the user defines input files, start date, and whether to use advection:
- STREAM_config.py

Optional files:
- STREAM_PlotOutput.py (to visualize STREAM input and output fields)
- CSGD_TrainErrorModel.py (to train CSGD error model)

### Required python packages for STREAM 
Required packages: xarray, xesmf, scipy, numpy, random, datetime

Required packages for plotting STREAM output fields: cartopy, matplotlib

# Example cases
Several example uses of STREAM are included in the example_files directory. The required input data and output fields are located in the example_data and example_output directories, respectively.

## IMERG_CSGD
Global precipitation products rely on radar instruments aboard satellites in their estimates of high resolution precipitation, but these remote retrievals often lead to imperfect, uncertain precipitation estimates. This example case demonstrates how STREAM can be used to generate fields of possible "true" precipitation in the central U.S. based on NASA's IMERG-Early multi-satellite product. The errors in IMERG-Early are described by a calibrated Censored Shifted Gamma Distribution (CSGD) error model, with the model parameters provided in NLmodel_covars.nc. In this example case, correlated noise fields are generated at an hourly timestep for 10 days and then, based on these noise fields and the included CSGD model parameters, realistic hourly precipitation fields are generated for 10 days. If the user changes the seed number set in the IMERG_CSGD config file, they will notice that the noise fields will use different randomly generated fields and thus the resulting precipitation fields will change. If the user were to repeat this process many times, they would obtain an ensemble of fields describing what the true precipitation in the central U.S. may have actually been based on what IMERG-Early recorded.

## GARD (Generalized Analog Regression Downscaling method)
The En-GARD downscaling method utilizes spatially correlated random fields (SCRFs) to perturb the error field calculated during downscaling and apply it to the downscaled mean field. STREAM provides an efficient way to generate such random fields based on the spatial correlation structure of the downscaled mean field. For more information on En-GARD, see Gutmann et al. 2022. In the example case provided here, a downscaled daily precipitation field, gard_out_pcp.nc, is used to generate a 10-year long correlated random noise field with correlation structures based on gard_out_pcp.nc.

Gutmann, E. D., Hamman, Joseph. J., Clark, M. P., Eidhammer, T., Wood, A. W., & Arnold, J. R. (2022). En-GARD: A Statistical Downscaling Framework to Produce and Test Large Ensembles of Climate Projections. Journal of Hydrometeorology, 23(10), 1545–1561. https://doi.org/10.1175/jhm-d-21-0142.1
 

 
### Running STREAM 
Fill in all parameters and filenames in the Input Parameters Section of STREAM_config.py 

Run "python STREAM_config.py" from the command line.
 
### Formatting input data for STREAM 
**pcp_file** should be a netcdf file with coordinates (time,lat,lon). The user defines the variable name, pvar, and can optionally include a pre-processing function to get this dataset into the correct format for STREAM.

**wind_file** should be a netcdf file with coordinates (time,lat,lon). It should cover the same region and time steps as the pcp_file, although STREAM will automatically regrid this dataset to the resolution of the pcp_file and interpolate to fill in any time steps in pcp_file that are not in wind_file.

If using the CSGD error model:

**paramsInFname** should be a netcdf file with coordinates (time,lat,lon) at the same resolution as pcp_file and variables 'clim1','clim2','clim3', 'par1','par2','par3','par4','par5' 

### Retrieving data for STREAM in IMERG and CSGD application
- IMERG Early data downloaded from GESDISC – variable ‘precipitationCal’ 
- MERRA2 data downloaded from GESDISC – variables ‘U850’ and ‘V850’ in m/s 

### Optional: Training CSGD error model for STREAM 
Use functions in CSGD_utilities.py to train CSGD error model. 

An example training script is provided in CSGD_trainErrorModel.py.

For more details on the pixel-scale CSGD error model, see: 

Wright, D. B., Kirschbaum, D. B., & Yatheendradas, S. (2017). Satellite Precipitation Characterization, Error Modeling, and Error Correction Using Censored Shifted Gamma Distributions. Journal of Hydrometeorology, 18(10), 2801–2815. https://doi.org/10.1175/JHM-D-17-0060.1 

# Citation
Hartke, S., Wright, D., Liu, Y., Peng, K., Alexander, A., & Derin, Y. (2024). Space Time Rainfall Error and Autocorrelation Model (STREAM) (Version 1.1). Zenodo. https://doi.org/10.5281/zenodo.13840778

# License
STREAM is distributed under the MIT License:

Copyright 2022 Hartke

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to use without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

The software is provided “as is”, without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose and noninfringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability, arising from, out of or in connection with the Software or the use or other dealings in the Software.

# Support disclaimer
This code is made available on an as-is basis. There is no guarantee that it will run on a given platform or if it does, that it will run correctly.
