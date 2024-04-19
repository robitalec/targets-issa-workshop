# {targets} iSSA workshop

## Workshop on using the {targets} iSSA workflow 

This workshop is focused on developing familiarity with using the [{targets} iSSA workflow](https://github.com/robitalec/targets-issa). The workflow is intended as a detailed example to be used alongside the [How To iSSA online book](https://issa-guild.github.io/book). 
The details of iSSAs and how they are theory implemented in the workflow is well covered there, so this workshop centers on the code required to run the example iSSA and adapt it to your own data. 

This workshop is based on the [reproducible workflows workshop](https://github.com/robitalec/reproducible-workflows-workshop) 
originally developed for a
[workshop](https://github.com/robitalec/2023-CSEE-reproducible-workflows-workshop)
at [CSEE 2023](https://www.beepeg2023.ca/) by:

- Alec L. Robitaille (Memorial University of Newfoundland and Labrador)
- Isabella C. Richmond (Concordia University)


## Why {targets}?

The {targets} package developed by Will Landau and peer reviewed by rOpenSci is an R package for defining pipelines/workflows, coordinating interdependent analytical steps, input and output data files, and iterating across patterns. Each step in a defined pipeline is saved and only rerun when preceeding steps have been modified, saving us and energy only rerunning what needs to. These features are incredibly well suited to iSSAs given their resource intensiveness and levels of analysis eg. individuals, seasons, etc. 

{targets} emphasizes a function oriented programming style that improves on scipt based workflows. Logical chunks of code can be written as functions that can be reused throughout and across projects. See the [Functions](https://books.ropensci.org/targets/functions.html) chapter in the {targets} manual. 

Branching/iterating across patterns, eg. lists of files or data.frame columns, is flexible and built in. This means our functions can be typically built for one chunk (eg. a single individual) which is easily applied over every level using {targets}'s dynamic branching. See the [Dynamic branching](https://books.ropensci.org/targets/dynamic.html) chapter in the {targets} manual. 

Once the pipeline is defined, {targets} orchestrates the tasks. No need to sit at the computer running a series of scripts - just start the pipeline and go for a walk outside or make yourself cup of coffee/tea.


## Setup

This workshop is aimed at improving our ability to use and modify the {targets}
iSSA workflow. All the materials should be accessible from the side bar (slides,
exercises, resources for further reading, and the link to the GitHub repository
can be accessed by clicking on the GitHub icon).

We don't have any strict dependencies on specific versions of R or R packages, but it would be good to have at least R version 4.0 and a recent version of RStudio.

We are using Quarto to build the workshop's website and exercises, so it could
be helpful for you to install it too. If you don't have time to, you can always
complete exercises in an R script - so no pressure.

Install first the Quarto CLI from the
[here](https://quarto.org/docs/get-started/) then the package with the command
at the bottom.

Please install the following packages (after updating R):

```r
pkgs <- c(
    'targets',
    'tarchetypes',

    'qs',
    'visNetwork',
    'igraph',

    'data.table',
    'dplyr',

    'ggplot2',
    'ggthemes',

    'testthat',
    'janitor',
    'rlang',
    'renv',
    'conflicted',

    'palmerpenguins',

    'quarto',
    'xml2',
    'downlit',
    'usethis',

    'amt',
    'sf',
    'raster',

    'parsedate',

    'distanceto',

    'glmmTMB',
    'broom.mixed',
    'tidyr',
    'dplyr',
    'tibble'
)

install.packages(pkgs)
```


To download the workshop materials for a participant, use this command:

```r
library(usethis)

# (Set your own destination directory)
use_course(
  'robitalec/targets-issa-workshop', 
  destdir = '~/Documents'
)
```

Or by downloading and unzipping the ZIP file at this link: <https://github.com/robitalec/targets-issa-workshop>. 

Then open up the RStudio project. 

## Data

Example data for this workshop is borrowed from the Palmer Long-Term Ecological Research (LTER). Here is the study description from the [Palmer LTER site](https://pallter.marine.rutgers.edu/):

>  The Palmer Long-Term Ecological Research (LTER) study area is located to the west of the Antarctic Peninsula extending South and North of the Palmer Basin from onshore to several hundred kilometers off shore. Palmer Station is one of the three United States research stations located in Antarctica. It is on Anvers Island midway down the Antarctic Peninsula at latitude 64.7 South, longitude 64.0 West.

> The Palmer LTER studies a polar marine biome with research focused on the Antarctic pelagic marine ecosystem, including sea ice habitats, regional oceanography and terrestrial nesting sites of seabird predators. The Palmer LTER is one of more than 26 LTER research sites located throughout the United States, Puerto Rico and Tahiti; each focused on a specific ecosystem, that together constitute the LTER Network.

We gratefully acknowledge the Palmer LTER for releasing data freely and openly for diverse uses - in our case for training analytical skills of researchers in ecology.  


### Penguins

The first dataset is already available in R through the [`palmerpenguins` R package](https://allisonhorst.github.io/palmerpenguins/). There is a raw version and a cleaned version. They contain data for 344 penguins, with the following variables (cleaned version):

- species
- island
- bill_length_mm
- bill_depth_mm
- flipper_length_mm
- body_mass_g
- sex
- year

### Weather timeseries

The following datasets are available directly from the [Palmer LTER Data Catalog](https://pallter.marine.rutgers.edu/catalog/edi/). To download the data to the `raw-data/` directory, run the function `download_example_data()` (`R/download_example_data.R`). 

This second dataset contains monthly averaged weather timeseries from Palmer Station, Antarctica, with the following variables: 

- Date  
- Year  
- Month  
- Average Temperature  
- Temperature Count  
- Average Pressure  
- Pressure Count  
- Average Melted Precipitation  
- Precipitation Count  

[Data package summary](https://portal.edirepository.org/nis/mapbrowse?packageid=knb-lter-pal.189.8)

[Data package metadata](https://portal.edirepository.org/nis/metadataviewer?packageid=knb-lter-pal.189.8)

Link to data (CSV): 

[https://portal.edirepository.org/nis/dataviewer?packageid=knb-lter-pal.189.8&entityid=ab357b4c92531a07d98ff1c4f4809a1e](https://portal.edirepository.org/nis/dataviewer?packageid=knb-lter-pal.189.8&entityid=ab357b4c92531a07d98ff1c4f4809a1e)



### Monthly sea ice area

The third dataset contains monthly sea ice area from the region around the Palmer Station, Antarctica, with the following variables:

- Year
- Month
- Area

Note: this data is formatted with months as columns, years as rows, and cells filled with the corresponding area. 


[Data package summary](https://portal.edirepository.org/nis/mapbrowse?packageid=knb-lter-pal.34.7)

[Data package metadata](https://portal.edirepository.org/nis/metadataviewer?packageid=knb-lter-pal.34.7)

Link to data (TXT): 

[https://portal.edirepository.org/nis/dataviewer?packageid=knb-lter-pal.34.7&entityid=0fccb4e99aaa0c0cc85c23284288ec81](https://portal.edirepository.org/nis/dataviewer?packageid=knb-lter-pal.34.7&entityid=0fccb4e99aaa0c0cc85c23284288ec81)



### Adelie penguin adult and chick counts

The fourth dataset contains Adelie penguin adult and chick counts

- studyName  
- Date GMT  
- Time GMT  
- Island  
- Colony  
- Adults  
- Chicks

[Data package summary](https://portal.edirepository.org/nis/mapbrowse?packageid=knb-lter-pal.88.8)

[Data package metadata](https://portal.edirepository.org/nis/metadataviewer?packageid=knb-lter-pal.88.8)

Link to data (CSV): 

[https://portal.edirepository.org/nis/dataviewer?packageid=knb-lter-pal.88.8&entityid=b4062890db09a72628786650dacfbf1f](https://portal.edirepository.org/nis/dataviewer?packageid=knb-lter-pal.88.8&entityid=b4062890db09a72628786650dacfbf1f)



## LICENSE

This project is released under the GNU General Public License v3.0. Read it [here](https://github.com/robitalec/targets-issa-workshop/blob/quarto/devel/LICENSE).

