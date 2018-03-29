# Data wrangling overview

## Sourcing the data

Data for Severn River Basin District were downloaded from the environment
agency [website](http://environment.data.gov.uk/catchment-planning/RiverBasinDistrict/9).
The download link URLs were copied into a script for reproducibility.

## Understanding the data

The data files thus obtained can be broadly described as

* classification of the water quality
* reasons a water body did not achieve a rating of good
* the connection between water bodies and any protected areas
* measures listing planned improvement/remediation work
* the nature of the desired objective (currently probably the least
understood data.

## Checking the data

No major issues with missing fields were discovered. Only some minor fields
had any missing values. There were some minor variations in spelling of some
columns that were common across multiple files. These common columns were
extracted into a new table. Some issues were discovered in the measures data
where some measures related to more than one water body and the water body
references had been combined (pasted together). These were split apart so
each water body ID could be considered individually. All the resultant water
body data were finally merged and verified to have unique mappings from
water body ID to water body data. 

## Creating working data set

The columns that were extracted into the new water body table were removed
from their original data tables. Only the water body ID was left as the key
to use in joining the data back up again. Those data, along with the newly
created master table of water body data, were saved to disk for subsequent
analysis. The detailed record of this data processing can be found
[here](data_wrangling.md).
