# Semantic Search use case using ACTRIS data

This repository is a part of the [ENVRI-FAIR project](https://envri.eu/home-envri-fair/) and was developed as a part of the working group on semantic search in WP8 - Implementation of atmospheric sub-domain.

The working group collected use cases from all the research infrastructures involved in the working group. The ACTRIS use case was selected for further investigation. Representatives from WP7 (Common implementation and support) was involved in developing a demonstrator for the ACTRIS use case. The result is this GitHub repository that contains Jupyter Notebooks demonstrating the semantic search elements from the ACTRIS use case.
Furthermore, the demonstrator is showcasing the semantic search feasibility for a couple of selected ACTRIS datasets, across multiple vocabularies where researchers can find long time series of aerosol optical properties across multiple vocabularies including WIGOS, NERC/CF and the ACTRIS In Situ internal vocabulary for research conducted in the atmospheric science domain.

Note that due to time limitation, this demonstrator is only addressing parts of the original use case. It is meant as a starting point for showcasing what could be done and further steps for improvement.

## Background

There are currently around 60 stations providing aerosol optical properties data in ACTRIS. Some of these stations are in large European cities and some in rural and remote areas. 
There are different instruments measuring this type of data (including lidar, filter absorption photometer, nephelometer and condensation particle counter). 

**In ACTRIS we consider the following observations as aerosol optical properties:**
* light scattering coefficient (various wavelengths) 
* light hemispheric backscattering coefficient (various wavelengths) 
* light absorption coefficient (various wavelengths) 

## Use Case

*A researcher is interested in finding “long time series of aerosol optical properties in remote areas*
 
The search is three-fold. What defines "Long time series", what is included in "aerosol optical properties" and what do we consider "remote-areas". All of these elements in the search are in themselves complicated. 
Since the scope of this assignment was limited, the group decided to focus on one of these three elements in the search. It was decided to focus the "aerosol optical properties" part of the search, looking at different vocabularies and mapping of these vocabularies so that we could find the same data across the use of different vocabularies for variable names. Furthermore, for this initial demonstrator the group looked at the aerosol absorption coefficient component measured by the filter absorption photometer instrument.

## Demonstrator

### Components of the demonstrator

This use case demonstrates the semantic search feasibility over the ACTRIS dataset, across multiple vocabularies. A triple file is created from the dataset attributes. The relation between the instrument filter_absorption_photometer and the variable it measures, aerosol_absorption_coefficient, is recorded in the triple file. A sparql query displays the relation between that instrument and the variable, also shows the synonyms of that variable.

![Demonstrator](https://folk.nilu.no/~richard/envri-fair/demonstrator.png)
*Figure 1: Parts of the demonstrator*

#### Description

It was decided to link "aerosol absorption coefficient” in the [ACTRIS dataset](https://github.com/xiaofengleo/actris/blob/master/DK0025G.20030101000000.20170713083448.filter_absorption_photometer.aerosol_absorption_coefficient.aerosol.12y.1h.US08L_Magee_AE16_SUM.US08L_aethalometer.lev2.nc) with the CF standard name “volume_absorption_coefficient_in_air_due_to_dried_aerosol_particles” in the NERC vocabulary, and further link this with "Light absorption coefficient” in the WIGOS vocabulary. In order to do this a Jupyter Notebooks was created for extracting metadata from the NetCDF files which includes both the internal ACTRIS variable name and the CF standard name. The link between these could be found in the metadata attributes of the dataset. 

**For example**
> string aerosol_absorption_coefficient_prec1587:standard_name = "volume_absorption_coefficient_in_air_due_to_dried_aerosol_particles" ;
> string aerosol_absorption_coefficient_amean:standard_name = "volume_absorption_coefficient_in_air_due_to_dried_aerosol_particles" ;
> string aerosol_absorption_coefficient_perc8413:standard_name = "volume_absorption_coefficient_in_air_due_to_dried_aerosol_particles" ;
> string :keywords = "NOAA-ESRL, DK0025G, aerosol_absorption_coefficient, Summit, aerosol, GAW-WDCA, volume_absorption_coefficient_in_air_due_to_dried_aerosol_particles" ;

For the WIGOS voccabulary, the mapping is not standardized since the mapping between WIGOS codes and CF standard names are in a CSV file from an extract of the WIGOS database. 

As a result, we can map the URI below to the "aerosol absorption coefficient" variable name used in ACTRIS (In this repository, the yaml file links the vocabularies together).

Then we have the aerosol absorption coefficient data with the uri:
* http://actrisexample.eu/ns/Component (will not resolve)
* http://vocab.nerc.ac.uk/collection/P07/current/3TUNI9CM/
* http://codes.wmo.int/wmdr/ObservedVariableAtmosphere/316
* http://codes.wmo.int/wmdr/ObservedVariableAtmosphere/317
* http://codes.wmo.int/wmdr/ObservedVariableAtmosphere/318

Note above that the WIGOS vocabulary separates the variable name by matrix definition, which is not the case for the other vocabularies.

> As a result we have the ACTRIS Use Case where "Filter absorption photometer" <-- Measures--> aerosol absorption coefficient <--PartOf--> aerosol optical properties

So for example a search on "aerosol absorption coefficient" will also give datasets that are listed only as "volume_absorption_coefficient_in_air_due_to_dried_aerosol_particles" as well as "Light absorption coefficient, pm1",  "Light absorption coefficient, pm10",  "Light absorption coefficient, total aerosol".
### Workflow
The workflow for executing the pipeline is described referring to the components shown in the Figure 1 above.
1. Input: DK0025G.20030101000000.20170713083448.filter_absorption_photometer.aerosol_absorption_coefficient.aerosol.12y.1h.US08L_Magee_AE16_SUM.US08L_aethalometer.lev2.nc

2. Tools to extract info:
Run the [Attrs2Vocab.ipynb](https://github.com/xiaofengleo/actris/blob/master/Attrs2Vocab.ipynb) to generate [vocab.yaml](https://github.com/xiaofengleo/actris/blob/master/vocab.yaml).
The [vocab.yaml](https://github.com/xiaofengleo/actris/blob/master/vocab.yaml) file contains:
*  1): the terms used by the head of the .nc file, 
*  2): the mapping between the variable name, "aerosol absorption coefficient" and the CF standard name “volume_absorption_coefficient_in_air_due_to_dried_aerosol_particles” in the NERC vocabulary, and further link this with "Light absorption coefficient” in the WIGOS vocabulary are encoded in the output file vocab.yaml.
 These vocabularies are described in the step 3.

3. The mapping described in previous step 2.1.b is build with the help of following two files:
* 1): [qry_mapping_variables_ebas_wmdr.csv](https://github.com/xiaofengleo/actris/blob/master/qry_mapping_variables_ebas_wmdr.csv) is an extract from the the WIGOS database (World Meteorological Organization vocabulary). See line 113-115 in the file.
* 2): [ebas_cfname_mapping.txt](https://github.com/xiaofengleo/actris/blob/master/ebas_cfname_mapping.txt) is the ACTRIS In Situ database. See line 186 in the file.

4. Run the [ActrisExample.ipynb](https://github.com/xiaofengleo/actris/blob/master/ActrisExample.ipynb) to generate outputs/data.ttl, outputs/data.trig

5. The data.trig or data.ttl file is hosted in a Jena Fuseki server. 

6. Run the [queries.ipynb](https://github.com/xiaofengleo/actris/blob/master/queries.ipynb), you can see the results demonstrating the linking between "aerosol absorption coefficient" and  “volume_absorption_coefficient_in_air_due_to_dried_aerosol_particles” , and "Light absorption coefficient”.

7. A user interface, which is still under construction.


### Visualization of the Sparql query
Besides the results demonstrated in [queries.ipynb](https://github.com/xiaofengleo/actris/blob/master/queries.ipynb), we also provide a visualization graph for users to easy see the link of the "aerosol absorption coefficient" with other terms.
![Visualization of sparql query](https://folk.nilu.no/~richard/envri-fair/query-results.png)
*Figure 2: Visualization of the sparql query showing the relation of the "aerosol absorption coefficient" with other terms*

#### Suggested improvements
There are seveal improvements expected in the near future.
1. Utlize the [netcdf-ld](https://github.com/binary-array-ld/bald) library to extract the head attributes from the .nc file. An example can be found at https://github.com/ferrighi/netcdf-ld-prototype
2. Some NLP library or toolkits, like [NLTK](https://www.nltk.org) can be utilized to help the task like finding and extracting entities, relations between entities, etc.
3. How to build the mapping among ontologies/vocabularies. A reference could be the I-ADOPT project.
4. More advanced queries is expected to display the content from the RDF triple store.
5. Complete the GUI.

![Improvements](https://folk.nilu.no/~richard/envri-fair/improvements.png)
*Figure 3: Suggested improvements for the demonstrator*
