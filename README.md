## Preprocessing for PISM


This is a collection of preprocessing routines for input data to be used in PISM
for Antarctic ice sheet simulations.
The preprocessing provides the steps from downloading the data to
making the data PISM-ready, i.e. no other steps in preprocessing should be needed
to use the data with PISM.

Initial conditions for PISM include bed topography, ice thickness and
ice temperature distribution.

Boundary conditions include ice surface temperature and ice surface mass balance or
atmospheric temperature at the snow surface and mass balance at the snow surface.
PISM needs ocean melt rates and ice shelf basal temperatures as boundary conditions
at the ice shelf base.
PISM includes simple ocean models that can provide such data from open
ocean properties (i.e. the PICO ocean model).


### Structure

Scripts for dataset-specific preprocessing are in the folder with the name
of the dataset. They should be run in the order listed below.
The `grids` folder holds scripts for the creation of target grids to which the
input data is regridded. The `pism_input` folder holds general program code.
The `merging` folder holds code to merge single datasets into
files that hold all the necessary initial and boundary conditions to
drive PISM.
Here is the graph:


```
pism_input
+-- grids
|   +-- create_cdo_targetgrids.py
+-- bedmap2
|   +-- download_and_extract_to_nc.py
|   +-- remap.py
+-- albmap
|   +-- download_and_rename_variables.py
|   +-- remap.py
+-- racmo
|   +-- merge_and_rename_variables.py
|   +-- remap.py
+-- zwally_basins
|   +-- download_and_extend_to_ocean.py
|   +-- remap.py (with local cdo rempnn)
+-- schmidtko
|   +-- download_and_write_to_netcdf.py
|   +-- calculate_potential_temps.py
|   +-- remap.py
|   +-- compute_basin_means.py
+-- accum
|   +-- download_and_extract_to_nc.py
|   +-- remap.py
+-- rignotvel
|   +-- preprocess_netcdf.py
|   +-- remap.py
+-- initmip
|   +-- preprocess.sh
|   +-- remap.py (asmb)
|   +-- postprocess.sh (incl. cdo remapnn abmb)
+-- merge_datasets.py
|   
+-- pism_input
    +-- pism_input.py (tools)
```

### Data sources

TODO: sortieren nach Variable statt Datenquelle


#### Arthern accumulation
Antarctic surface accumulation map on the same grid, with the same projection and in the same file formats as bedmap2.

Documentation: <https://secure.antarctica.ac.uk/data/bedmap2/resources/Arthern_accumulation/Arthern_accumulation_bedmap2_grid_readme.rtf>

Citation: Arthern, R. J., D. P. Winebrenner, and D. G. Vaughan (2006), Antarctic snow accumulation mapped using polarization of 4.3-cm wavelength microwave emission, J. Geophys. Res., 111, D06107, doi:10.1029/2004JD005667.

#### Albmap
5km resolution, NetCDF files of Antarctica gathered from various data sources and interpolated, when necessary, onto the same grid using polar stereographic projection

Documentation: <http://websrv.cs.umt.edu/isis/index.php/Present_Day_Antarctica>

Citation:

 - accumulation

    Vaughan, D. G., Bamber, J. L., Giovinetto, M., Russell, J., & Cooper, A. P. R. (1999). Reassessment of net surface mass balance in Antarctica. Journal of climate, 12(4), 933-946.

 - surface elevation

   Bamber, J. L., Gomez-Dans, J. L., & Griggs, J. A. (2009). A new 1 km digital elevation model of the Antarctic derived from combined satellite radar and laser data-Part 1: Data and methods. The Cryosphere, 3(1), 101.

   Griggs, J. A., & Bamber, J. L. (2009). A new 1 km digital elevation model of Antarctica derived from combined radar and laser data-Part 2: Validation and error estimates. The Cryosphere, 3(1), 113.

 - mean annual temperature

   Comiso, J. C. (2000). Variability and trends in Antarctic surface temperatures from in situ and satellite infrared measurements. Journal of Climate, 13(10), 1674-1696.

- geothermal flux

  Shapiro, N. M., & Ritzwoller, M. H. (2004). Inferring surface heat flux distributions guided by a global seismic model: particular application to Antarctica. Earth and Planetary Science Letters, 223(1), 213-224.


#### Bedmap 2

Dataset describing surface elevation, ice-thickness and the sea ﬂoor and subglacial bed elevation of the Antarctic south of 60◦ S. Dataset was derived using data from a variety of sources, including many substantial surveys completed since the original Bedmap compilation (Bedmap1) in 2001. The Bedmap2 ice thickness grid is made from 25 million measurements, over two orders of magnitude more than were used in Bedmap1.

Documentation: <https://www.bas.ac.uk/project/bedmap-2/>

Citation: Fretwell, P., Pritchard, H. D., Vaughan, D. G., Bamber, J. L., Barrand, N. E., Bell, R., Bianchi, C., Bingham, R. G., Blankenship, D. D., Casassa, G., Catania, G., Callens, D., Conway, H., Cook, A. J., Corr, H. F. J., Damaske, D., Damm, V., Ferraccioli, F., Forsberg, R., Fujita, S., Gim, Y., Gogineni, P., Griggs, J. A., Hindmarsh, R. C. A., Holmlund, P., Holt, J. W., Jacobel, R. W., Jenkins, A., Jokat, W., Jordan, T., King, E. C., Kohler, J., Krabill, W., Riger-Kusk, M., Langley, K. A., Leitchenkov, G., Leuschen, C., Luyendyk, B. P., Matsuoka, K., Mouginot, J., Nitsche, F. O., Nogi, Y., Nost, O. A., Popov, S. V., Rignot, E., Rippin, D. M., Rivera, A., Roberts, J., Ross, N., Siegert, M. J., Smith, A. M., Steinhage, D., Studinger, M., Sun, B., Tinto, B. K., Welch, B. C., Wilson, D., Young, D. A., Xiangbin, C., and Zirizzotti, A.: Bedmap2: improved ice bed, surface and thickness datasets for Antarctica, The Cryosphere, 7, 375-393, doi:10.5194/tc-7-375-2013, 2013.

#### Racmo

The file is based on HadCM3_c20_I2S_precip_Y.nc and HadCM3_c20_I2S_t2m_Y.nc which come from data for Frieler et al 2014.

HadCM3_c20_I2S_precip_Y.nc contains precipitation in mm/yr for 1980-1999 over Antarctica from RACMO2 run forced with HadCM3 data. HadCM3_c20_I2S_t2m_Y.nc contains temperature in Kelvin for 1980-1999 over Antarctica from RACMO2 run forced with HadCM3 data. This file should be used as PISM input with the atmosphere_given option.



#### Schmidtko
Southern Ocean dataset.

Citation: Schmidtko, Sunke, Heywood, Karen J., Thompson, Andrew F. and Aoki, Shigeru (2014) Multidecadal warming of Antarctic waters Science, 346 (6214). pp. 1227-1231.


#### Zwally
Drainage regions.

Documentation: <http://homepages.see.leeds.ac.uk/~earkhb/Basins_page.html>

Citation: Zwally, H. J., Giovinetto, M. B., Beckley, M. A., & Saba, J. L. (2012). Antarctic and Greenland drainage systems. GSFC Cryospheric Sciences Laboratory.
