# **Load Profile Generator**

Version 1.0

November 16 2020 

## **Introduction**

The Load Profile Generator is a stochastic bottom-up model to simulate electricity consumption in detail at regional level.

The developed model is able to synthesise site-specific social structures using widely available statistics, thus avoiding dependence on highly complex data sets.
In order to depict various determinants of energy demand, the model is divided into five modules: `environment`, `social_demographics`, `technologies`, `behavior` and `loadprofiles`. This allows weather influences, age structures, employment statistics, as well as distributions of household size and income to be depicted and technologies to be allocated via socio-demographic characteristics within society. The behavior of residents is then modelled using a Semi-Markov chain based on a time-use survey and linked to the use of technologies in order to calculate a time-resolved electricity consumption.

**Note:** To "read" the source code it is recommended to read the `run.py` chronologically. In this file the functions from all modules are called up step by step. Docstrings are provided.


## **List of Content**

1. [Setup](#1-Setup)
2. [Run](#2-Run)
3. [Configuration](#3-Configuration)
4. [Input](#4-Input)
5. [Output](#5-Output)
6. [Modifications](#6-Modifications)
7. [Extensions](#7-Extensions)


## **1. Setup**

To set up the Load Profile Generator a simulation can be started. Required modules are usually installed automatically by the [AutoStart extension](#7-Extensions) (prerequisite for automatic installation is an existing Internet connection). If errors occur during the simulation because certain modules could not be installed, the following modules must be installed manually (if *Anaconda* is used, most modules are already included):

* `collections`,
* `colorama`,
* `copy`,
* `csv`,
* `datetime`,
* `glob`,
* `itertools`,
* `json`,
* `math`,
* `numpy`,
* `os`,
* `pandas`,
* `pathlib`,
* `plotly`,
* `random`,
* `shutil`,
* `statistics`,
* `subprocess`,
* `sys`,
* `uuid`,
* `warnings`

**Note**: The required modules are also listed under  path *Simulator/extensions/AutoStart/requirements.txt*.

## **2. Run**

To start a simulation, the `run.py` file must be executed. In case *Jupyter Notebook* is used to run a simulation, the `run.ipynb` file can be executed alternatively (this works as a wrapper, executing the run-file with the command `'%run run.py jupyter'`).

The configuration of a simulation is done with the `config.json` file.



## **3. Configuration**

The `config.json` file can be used to make simulation-specific settings.
**Note:** If a config-file is placed in the `Input` folder, it will be used. Otherwise the config-file on the main level will be used.

To run a simulation the `system.mode` must be set to `default` or `simulation`. The other modes can be used to integrate data records from the `Input` folder into the `Simulator/data` folder on a long-term basis, or to provide template data records which can then be filled manually with specific data for simulations.

### **3.1 Options**

The following overview describes the configuration options in `config.json`.

|OPTION|DESCRIPTION|
|-|-|
|**GENERAL**|**Basic simulation-specific settings.**|
|`simulation-name`|Name of the simulation.|
|`nation`|Key to identify relevant input data via file prefix (preferred: region, state).|
|`state`|Key to identify relevant input data via file prefix (preferred: region, fallback: nation).|
|`region`|Key to identify relevant input data via file prefix (fallback: state, nation).|
|`DemanRegio`|Auto-acquisition of global radiation, temperature and household size distribution via DemandRegio-identifiers.|
|`residents`|Number of inhabitants at the simulated location (*int*). Zero requires absolute number of households.|
|`households`|Number of households at the site (*int*/*list*). Can also be provided via input-CSV.|
|`area-in-sq-km`*|Area of the simulated site.|
|`urbanisation`*|Assessment value for the urbanisation of the simulated site.|
|`timeline`|Specification of start date, end date and resolution of the simulation.|
|`consider`|Consideration of characteristics from environment, socio-demography, technology and behaviour.|
|`overwrite`|Overwriting of characteristics if ready-made module input data sets are used.|
|**INPUT**|**Specify how to handle input data from the Input folder.**|
|`integrate`|Integrate new input data permanently in data folder (*true*/*false*).|
|`update`|Update existing data sets in the data folder with new input data (*true*/*false*).|
|**OUPUT**|**Specify the output data created by a simulation.**|
|`csv`|Output in csv format (*true*/*false*). [Module results and intermediate statistics].|
|`json`|Output in json format (*true*/*false*). [Module Results Sociodemography].|
|`npy`|Output in npy format (*true*/*false*). [Module Result Behavior].|
|`graphics`|Automatic visualisation of the simulation results (*true*/*false*). [Python module `'plotly.graph_objects'` required].|
|`statistics`|Output of characteristic-related statistics of the simulated society (*true*/*false*).|
|`modules`|Output of separate module results (*true*/*false*). Module results can be used as input data.|
|`protocol`|Output of the simulation protocol (*true*/*false*).|
|`used_data`|Output of a list of all data sets used (*true*/*false*).|
|`configuration`|Save `config.json` file to output (*true*/*false*). The `config.json` file can be used as input.|
|**LOADPROFILES**|**Load profiles that are calculated. The total load profile is always calculated.**|
|`households`|Load profiles at household level (*true*/*false*).|
|`technologies`|Load profiles at technology level (*true*/*false*).|
|`appliances`|Load profiles at appliance level (*true*/*false*).|
|**LOG**|**Status reports that are reported during a simulation.**|
|`process`|Activate status of the running process (*true*/*false*). Recommended for transparency.|
|`progress`|Activate status of process progress (*true*/*false*). Requires a lot of computing power.|
|`warnings`|Activate warnings (*true*/*false*). Errors are always reported.|
|`infos`|Activate additional information (*true*/*false*).|
|`results`|Activate tabular display of process results during simulation (*true*/*false*).|
|**CALCULATION**|**Calculation relevant parameters.**|
|`randomize`|Start value for random numbers (*true*/*false*/int). With *true* a random start value is determined for each simulation. The start value is documented to reproduce the simulation. With *false* `0` is used as start value for the simulation. Alternatively any *natural number N* can be defined as start value.|
|`scaling`|Scaling (*true*/*false*) and specification how many households or persons are to be simulated. Fixed values for households or persons (*int*) can be specified, or upper and lower scaling borders in which scaling takes place automatically. **Fixed values recommended**. The simulation results are automatically scaled back to the scale of reality.|
|`resident-sample-size`|Number of residents compared to each other when allocating to a residential area.|
|**SYSTEM**|**Supplementary system functions.**|
|`mode`|Programme mode. Modes `default` and `simulation` run simulations, `templates` provides preformatted input data sets and `import`/`export` manage data sets (not recommended).|
|`breakpoints`|Activate pauses at which the simulation stops until it is continued manually (*true*/*false*).|
|`sound`|Acoustic signals for info, warnings, errors and end (*true*/*false*). Operating system *OS X* only.|
|`voice`|Spoken message for info, warnings, errors and end (*true*/*false*). Operating system *OS X* only.|
|`color`|Color scheme for graphics (*TUred*/*TUblue*/*TUgreen*).|

*Marked options are **deprecated** and might be removed in the future.


### **3.2 Recommendations**

#### For **General Settings**:

The location to be simulated is specified via the general settings. By specifying `nation`, `state` and `region` the model can identify appropriate data sets. For the identification, the given values for nation, state and region are compared with the prefixes in the names of the available data sets. The most specific records available are preferred.

_For example:_ If the location of *Braunschweig* in *Niedersachsen*, *Deutschland* is simulated, the following should be specified:

```json
"nation": "Deutschland",
"state":  "Niedersachsen",
"region": "Braunschweig"
```

Assume that there are two sets of data available on the age distribution:

1. *Niedersachsen_residents_by_age.csv*
2. *Braunschweig_residents_by_age.csv*

Thus the 2nd data set is used, as `region` is more specific than `state`. It is therefore recommended to identify the simulated site as precisely as possible.


#### For **Computing Time**:

The computing time is influenced in particular by the `scaling` and `timeline.resolution`.

Scaling can be used to set how many persons or households are actually to be simulated. However, the scaling goes along with the resolution. The higher the resolution of the simulation is, the longer a simulation takes - but the more accurate the results are.
Currently, a `resolution` of `15 minutes` is recommended, as higher resolutions require too much computing time and coarser resolutions produce inaccurate results (especially when simulating behavior).
In this context, a `scaling` of approximately `10,000` to `20,000` `residents` is therefore recommended. For short simulation periods (only a few days), up to 50,000 residents can also be an option.

_Computing time example:_ The simulation of a large city, scaled to 10,000 residents with a resolution of 15 minutes takes about 10 minutes per simulated day. If a whole year is simulated, the simulation takes almost 40 hours. These values naturally also depend on the computing power of the computer in use.
If the same simulation (a whole year) is scaled to 20,000 residents, the computing time doubles to about 80 hours.

Especially if multiple residents are simulated, the `resident-sample-size` has an effect on the computing time, too. This value influences how many people are compared when allocating residents to households in order to achieve the most realistic household compositions possible. A value of `800` is recommended.

## **4. Input**

Input data is placed in the `Input` folder. It is important that the prefix in the file name matches the value for `nation`, `state` or `region` (defined in `config.json`) so that the data set is recognised as relevant.
Records in the `Input` folder are preferred over records in the `Simulator/data` folder.
Which data sets were actually used during a simulation is documented in the `system` output *used_data.csv* and can still be viewed after a simulation.

The program can handle the following input data:

- The configuration file `config.json` can be used as input.
- Module results from previous simulations can be used as input:
    - *timeline_and_environment.csv*
    - *social_demographics_and_technologies.csv*
    - *behavior.npy*
    (The behavior file can only be reused if the social_demographics file is also reused.)
- Various statistics to specify the simulated site:
    - **environment**:
    - *seasons.csv*
    - *weekdays*
    - *holidays.csv*
    - *solar_irradiance.csv*
    - *temperature.csv*
    - *...*
    - **social_demographics**:
    - *households_by_size.csv*
    - *households_income_by_size.csv*
    - *households_by_age_gender.csv*
    - *households_by_combined_maturity.csv*
    - *residents_by_age.csv*
    - *residents_gender_by_age.csv*
    - *residents_occupation.csv*
    - *residents_income_by_occupation.csv*
    - *residents_age_combinations.csv*
    - *...*

    **Note**: To use simulation specific statistics, add a `'Prefix_'` to the filename according to `nation`, `state` or `region`. For example if `"region"`:`"Braunschweig"`, use *Braunschweig_solar_irradiance.csv*.

- Technology folders can be used as input.
    **Note**: It might be easiert to create technologies directly inside the *Simulator/data/technologies* directory.



## **5. Output**

Output data is stored in the `Output` folder.

For each simulation a separate folder is created in the `Output` folder, which is named after the `simulation-name`. In this simulation-specific output folder the outputs are subdivided according to type:

|TYPE|DESCRIPTION|
|-|-|
|`modules`|Contains results of the individual modules: *timeline_and_environment.csv*, *social_demographics_and_technologies.csv* and *behavior.npy*. These module outputs can be reused as inputs. Doing so, calculation time may be reduced.|
|`statistics`|Contains separate statistics that provide information on the composition of social_demographics, the distribution of technologies and appliances, and the behaviour of the simulated population.|
|`system`|Contains system information. *protocol.txt*, *used_data.csv* and *config.json*. The configuration file can be reused as input to run another simulation later with exactly the same settings.|
|`graphics`|Contains interactive plotly graphics in html format. These graphics can be viewed in a browser.|

**Note**: The corresponding outputs are only created if they are activated via config.json.



## **6. Modifications**

Some modifications can be made without having to change the source code (files in `Simulator/code`). Technologies and appliances als well as activities could be frequently affected by modifications.

#### Modify **Technologies and Appliances**:
Technologies can be added by creating new technology folders under `Simulator/data/technologies` and adding the technology names to `consider` in `config.json`. Appliances can also be added to the respective technology folders where specifications (consumption values) of existing appliances can be modified, too. In addition, the feature-related distribution probabilities can be set there.
In order to integrate new technologies, the template technology under `Simulator/docs/templates` can also be copied and adapted.

#### Modify **Behavior and Activities**:
Activities can be modified under `Simulator/data/behavior/SYS_DATA_activities.json`. Weighting factors for probability data sets can be modified under `Simulator/data/behavior/SYS_DATA_behavior_file_weights.json`. However, to modify the probabilities in the behavioural datasets it is recommended to use the [ZVE extension](#7-Extensions).



## **7. Extensions**

Under `Simulator/extension` some extensions are stored, which can be helpful - but are not necessary to run simulations.


### **7.1 AutoStart**

The `AutoStart` extension installs missing python modules.
The AutoStart extension is executed by the `run.py` file, and is used to install missing python modules automatically. If this fails, missing modules must be installed manually. The required modules are listed under `Simulator/extensions/AutoStart/requirements.txt`.


### **7.2 DemandRegio**

With the `DemandRegio` extension, data sets on household size distribution, the number of inhabitants or data on global radiation and temperature can be retrieved from the [DemandRegio database](http://opendata.ffe.de/project/demandregio/).

**Note**: Data will only be queried if the requested data is not already available in the Simulator, and a corresponding DemandRegio identifier is stored in `config.json`.

The DemanRegio extension is executed by the configuration module `config.py`. Via the configuration file `config.json`, DemandRegio identifiers can be specified to identify requested data sets in the database.

_For example:_ If the location of *Braunschweig* is simulated, one (or multiple) of following DemandRegio identifiers can be used:

```json
"id_region":     3101000,
"id_ags":        3101000,
"id_nuts3":      27600017,
"natcode_nuts3": "DE911"
```

The requested data is placed in CSV-files in the `Input` folder, and is read in the next step. In order for the data to be read in, it is necessary that the `region` in the `config.json` has the same spelling as the region name in the DemandRegio database.


### **7.3 ZVE**

(ZVE = Zeitverwendungserhebung, German for 'Time Use Survey')

The ZVE extension can be used to read the original `ZVE` data records and recreate probability files for activities.

The ZVE extension is NOT used within simulations. To use the extension the file `Simulator/extensions/ZVE/zve_reader.py` must be executed separately. The original ZVE data is available in anonymised form under `Simulator/extensions/ZVE/data/diaries.csv`. Further information on the original data is available under `Simulator/extensions/ZVE/destatis_info`. The 165 original ZVE activities can be regrouped via `activities.xlsx`. 

Running the `zve_reader.py` file creates new probability data records and places them in the `Simulator/extensions/ZVE/output` folder. From there, the new records can be copied to the `Simulator/data/behavior` folder to be considered in simulations.
As an alternative to the manual transfer, the newly read out data records can be modified with the `zve_modifier.py`. **Attention: The modifier automatically transports the data records into the simulation folder and overwrites the existing data records!**

For detailed description see `Simulator/extensions/ZVE/readme.md`.


