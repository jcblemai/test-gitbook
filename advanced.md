# Advanced

(Any more advanced mathematical or computational methods used, or possible configuration options, that only specialized users would need to change)

### Numerical integration methods

### Resuming model runs

### Choosing/changing fixed parameters

Aka “magic numbers” - fixed parameters that may or may not be in config, like MCMC step size, dt, etc …

### Changing model modules

(Ie using a totally different compartmental model or outcomes model)

### File descriptions

#### COVIDScenarioPipeline

[https://github.com/HopkinsIDD/COVIDScenarioPipeline](https://github.com/HopkinsIDD/COVIDScenarioPipeline)

Current branch: main

This repository contains all the code underlying the mathematical model and the data fitting procedure, as well as ...

To actually run the model, this repository folder must be located inside a location folder (e.g. COVID19\\\_USA) which contains additional files describing the specifics of the model to be run (i.e. the config file), all the necessary data/parameters, and ... and

**/R**

**/R/scripts**

* filter\\\_MC.R
* full\\\_filter.R
* build\\\_US\\\_setup.R
* build\\\_nonUS\\\_setup.R
*

**/R/pkgs**

**covidcommon**

* config.R
* DataUtils.R
* file\\\_paths.R
* safe\\\_eval.R
* compartments.R

**hospitalization**

* hospdeath.R

**inference**

* groundtruth.R
* functions.R
* filter\\\_MC\\\_runner\\\_funcs.R

**config.writer**

* create\\\_config\\\_data.R
* process\\\_npi\\\_list.R
* yaml\\\_utils.R

**report.generation**

* DataLoadFuncs.R
* ReportBuildUtils.R
* ReportLoadData.R
* setup\\\_testing\\\_environment.R

**/gempyor\\\_pkg**

**/gempyor\\\_pkg/src/gempyor/**

* seir.py - Contains the core code for simulating the mathematical model. Takes in the model definition and parameters from the config, and outputs a file with a timeseries of the value of each state variable (# of individuals in each compartment)
* outcomes.py - Contains the core code for generating the outcome variables. Takes in the output of the mathematical model and parameters from the config, and outputs a file with a timeseries of the value of each outcome (observed) variable
* setup.py
* compartments.py
* parameters.py
* /NPI/

**/gempyor\\\_pkg/docs**

* Rinterface.Rmd
* interface.ipynb

#### COVID19\\\_USA Repository

[https://github.com/HopkinsIDD/COVID19\_USA](https://github.com/HopkinsIDD/COVID19/\_USA)

Current branch: `master`

**/R**

Contains R scripts for generating model input parameters from data, writing config files, or processing model output. Most of the files in here are historic (specific to a particular model run) and not frequently used. Important scripts include:

* get\\\_vacc\\\_rate\\\_and\\\_outcomes\\\_R13.R

**R/scripts/config\\\_writers**

Scripts to generate config files for particular submissions to the Scenario Modeling Hub. Most of this functionality has now been replaced by the config writer package ()

**R/scripts/process\\\_sims**

Scripts to process the output of model runs into data formats and plots used for Scenario Modeling Hub and Forecast Hub. Important scripts include:

**/data**

Contains data files used in parameterizing the model for COVID-19 in the US (such as creating the population structure, describing vaccine efficacy, describing parameter alterations due to variants, etc). Some data files are re-downloaded frequently using scripts in the pipeline () while others are more static ()

Important files and folders include

* geodata.csv
* geodata\\\_2019\\\_statelevel.csv
* mobility.csv\`
* mobility\\\_territories\\\_2011-2015\\\_statelevel.csv
* outcomes\\\_ratios.csv
* US\\\_CFR\\\_shift\\\_dates\\\_v3.csv
* US\\\_hosp\\\_ratio\\\_corrections.cs

**/shp**

"Shape-files" (.shp) that .....

**/outcomes**

* usa-geoid-params-output\\\_V2.parquet

**/intervention\\\_tracking**

Data files containing the dates that different non pharmaceutical interventions (like mask mandates, stay-at-home orders, school closures) were implemented by state

**/data/vaccination**

Files used to create the config elements related to vaccination, such as vaccination rates by state by age and vaccine efficacy by dose

**/data/variant**

Files created in the process of downloading and analyzing data on variant proportions

**/manuscripts**

Contains files for scientific manuscripts using results from the pipeline. Not up to date

**/config**

Contains an archive of configuration files used for previous model runs

**/old\\\_configs**

Same as above. Contains an archive of configuration files used for previous model runs

**/scripts**

Depreciated - to be removed? - contains rarely used scripts

**/notebook**

Depreciated - to be removed? - contains rarely used notebooks to check model input. Might be used in some unit tests?

**/NPI**

empty?

**/hit-covid**

Depreciated - to be removed?

**/ScenarioHub**

Depreciated - to be removed?

### Code structure

Perhaps something like [this](https://lucid.app/publicSegments/view/6d5b81c9-03a8-4af9-9b93-81ea7c0891ad)
