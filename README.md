# LéXPLORE Idronaut Automatic Profiler


## Project Information / Abstract

[LéXPLORE](https://lexplore.info) is research platform on Lake Geneva for a broad range of limnological research. The platform results from a collaboration between five partner institutions ([Eawag](https://www.eawag.ch/en/), [EPFL](https://www.epfl.ch/en/), [INRAE](https://www6.lyon-grenoble.inrae.fr/carrtel/), [UNIGE](https://unige.ch), [UNIL](https://www.unil.ch/index.html)). The LéXPLORE platform is anchored since February 2019 at a position reaching 110 m depth off the lake's north-shore.
The data presented here is part of the core dataset maintained by the technical team of LéXPLORE.
The data is used and displayed on the [Datalakes website](https://www.datalakes-eawag.ch/). related data or products can be visualised and downloaded on the [Datalakes website](https://www.datalakes-eawag.ch/).


### References

Wüest, A., Bouffard, D., Guillard, J., Ibelings, B. W., Lavanchy, S., Perga, M. ‐E., & Pasche, N. (2021). LéXPLORE: a floating laboratory on Lake Geneva offering unique lake research opportunities. Wiley Interdisciplinary Reviews: Water, 8(5), e1544 (15 pp.). https://doi.org/10.1002/wat2.1544

See also the [360° virtual tour](https://www.eawag.ch/repository/lexplore/index.htm)

### Citation
Bouffard, D., Ballu, A., Cunillera, G., Fillion, R., Gios, M., Guillard, J., Ibelings, B., Lavanchy, S., Miesen, F., Pasche, N., Perga, M-E., Plüss, M., Quetin, P., Runnalls, J., Wüest, A. (2022). Data and products from LéXPLORE Therminster Chain, 2019 - 2023. DOI

DOI attribution pending.


### Time Frame
- Begin date: 29-06-2020
- End data: continuous data set - live

### Geographic coverage
Lake Geneva [46.50, 6.66]


## Sensors

Idronaut is a vertical profiler. Every two hours it collects a vertical profile of CTD (Conductivity, Temperature, and Depth), DO (Dissolved Oxygen), PH, Redox, CHL-a, Phycoerythrin, and Phycocyanin.
Data ranging from approx. 1 m to 90 m depth. The profiler is located in the LéXPLORE platform (46°30’0.819″ N, 6°39’39.007″ E) 

### CTD (Conductivity, Temperature, Depth, DO, PH, Redox, CHL-a, Phycoerythrin, Phycocyanin )

- **Brand, Model & SN**: Idronaut, OCEAN SEVEN 316Plus, SN 715681
- **System integration**: Indronaut winch installed over the internal moonpool of the platform, data download with REBUS software
- **Accuracy**: Conductivity ± 0.003 ms/cm, Temperature ± 0.003 °C, Depth ± 0,05 % FS, DO ± 1%, PH ± 0.01 pH, Redox 1mV
- **Setup** depth range: surface to 90 m depth, sampling period: 1 profile every 2 hours, profiling speed: 5 cm/s

## Code

[![License: MIT][mit-by-shield]][mit-by]

**You need to have [git](https://git-scm.com/downloads) installed in order to successfully clone the repository.**

- Clone the repository to your local machine using the command: 

 `git clone https://github.com/LeXPLORE-Platform/idronaut-automatic-profiler.git`
 
 Note that the repository will be copied to your current working directory.

- Use conda and install the requirements with:

`conda env create -f environment.yml`

## Usage

### Reprocess data

To reprocess data you can run the following command:

`python scripts/main.py`

Delete the Level1 and Level2 folders in data before running any reprocessing: existing Level 1 files and Level 2 profiles are skipped, not overwritten, so changes (e.g. to `notes/events.csv`) would not be applied.

### Live data

In order to process live data you need to copy `creds_example.json` to `creds.json` and complete the ftp credentials. You can then run:

`python scripts/main.py -s`

### Adapt/Extend data processing pipeline

The python script `scripts/main.py` defines the different processing steps while the python script `scripts/instruments.py` contains the python class TemperatureChain with all the corresponding 
class methods to process the data. To add a new processing or visualization step, a new class method can be created in the `instruments.py` file and the step can be added in `main.py` file.
Both above mentioned python scripts are independent of the local file system.

## Data

### Access

Data for this repository is stored in a remote object store. In order to work with the data you need 
to run `scripts/download_remote_data.py`, this will syncronise the local `data` folder with the remote 
data folder on the server. 

### License

[![CC BY 4.0][cc-by-shield]][cc-by] 

This data is released under the Creative Commons license - Attribution - CC BY (https://creativecommons.org/licenses/by/4.0/). This license states that consumers ("Data Users" herein) may distribute, adapt, reuse, remix, and build upon this work, as long as they give appropriate credit, provide a link to the license, and indicate if changes were made.
 
The Data User has an ethical obligation to cite the data source (see the DOI number) in any publication or product that results from its use. Communication, collaboration, or co-authorship (as appropriate) with the creators of this data package is encouraged. 
 
Extensive efforts are made to ensure that online data are accurate and up to date, but the authors will not take responsibility for any errors that may exist in data provided online. Furthermore, the Data User assumes all responsibility for errors in analysis or judgment resulting from use of the data. The Data User is urged to contact the authors of the data if any questions about methodology or results occur. 



### Data Structure

The data can be found in the folder `data`. The data is structured as follows:

- **Level 0**: Raw data collected from the different sensors.
- **Level 1**: Data is converted to NetCDF and quality assurance is applied. Quality flag "1" indicates that the data point didn't pass the quality checks and further investigation is needed, quality flag "0" indicates that the data has passed the quality assurance check. Do not forget to apply the quality check mask when analysing the data.
- **Level 2**: Profiles are resampled to a fix grid and combined to form a 2D grid of depth and time.

## Quality assurance

Quality checks include but are not limited to range validation, data type checking and flagging missing data.
The automatic quality check is controlled by the package [Envass](https://pypi.org/project/envass/). The specific methods implemented for this dataset are listed in `notes/quality_assurance.json`. 

### Events

Maintenance dates, sensor problems and other events not identified by the automatic quality assurance are listed in `notes/events.csv`.
One file covers all deployments (D1 to D4). The events are applied to the quality flags (`<variable>_qual = 1`) when the data are
processed to Level 1, and the flagged data are therefore NaN in Level 2. The file is semicolon separated with the columns `start;stop;parameter;depth;comments`:

- `start`, `stop`: UTC, format `YYYYMMDD HH:MM:SS` (inclusive). An empty `stop` means the event is ongoing.
  The event applies to every profile whose time range overlaps this window, and always to the whole profile: the exact times within a profile do not matter.
- `parameter`: `All` or a comma separated list of variable names as they appear in the Level 1 files (e.g. `Temp,OPTO%`).
  Only variables with a `_qual` variable can be flagged: `Press`, `Temp`, `Cond`, `Cond20`, `Sal`, `O2pc`, `O2ppm`, `OPTO%`, `OPTOppm`,
  `pH`, `eH`, `PAR`, `Chl`, `Chl2`, `Turb`, `PhycoEr`, `PhycoCy`. `All` flags all of them.
  Flagging `Temp` also removes it from the Level 2 `thermocline_depth` and `mixed_layer_depth`, which are calculated from the Level 2 temperature.
  Flagging `Press` does not change Level 2, which is gridded on `depth`.
- `depth`: empty or `All` flags the whole profile. Otherwise only the points of the profile whose depth (`Press / 0.981`) matches are flagged:
  a single depth (`5`, matching ±0.1 m, half the 0.2 m spacing of the Level 2 grid), a range (`10-20`) or a comma separated list of both (`2.5,10-20`).
  Depths are in metres. Depths can be slightly negative at the surface, so negative values are allowed (e.g. `-1.5-30`).
  The Level 2 grid bins within the flagged depth range are also set to NaN, so they are not interpolated from the data either side.
- `comments`: free text description of the event. If it contains a `;`, put it in double quotes.

Events added for profiles that were already processed only take effect after deleting the Level1 and Level2 folders and reprocessing the
complete dataset (see [Reprocess data](#reprocess-data)): existing files and profiles are not overwritten.
Problems in the events file are logged as warnings and do not stop processing.

Check also the `notes/sensors_history.csv` and the maintenance log `notes/metadata.csv`.

## Collaborators

- **Concept, finances, project management** Damien Bouffard, Jean Guillard, Bas Ibelings, Natacha Pasche, Marie-Elodie Perga, Alfred Wüest   
- **Installation, maintenance, data collection** Aurélien Ballu, Guillaume Cunillera, Roxane Fillion, Matteo Gios, Sébastien Lavanchy, Floreana Miesen, Michael Plüss, Philippe Quetin 
- **Data pipeline** James Runnalls
- **Data review** Damien Bouffard
- **Contact tech** Sébastien Lavanchy, sebastien.lavanchy@epfl.ch 
- **Contact science** Damien Bouffard. damien.bouffard@eawag.ch

[cc-by]: http://creativecommons.org/licenses/by/4.0/
[cc-by-shield]: https://img.shields.io/badge/License-CC%20BY%204.0-g.svg?label=Data%20License
[mit-by]: https://opensource.org/licenses/MIT
[mit-by-shield]: https://img.shields.io/badge/License-MIT-g.svg?label=Code%20License
