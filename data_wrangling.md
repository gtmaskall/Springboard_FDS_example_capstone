RBD/RBMP data
=============

Introduction
------------

This report documents the process of obtaining and preparing the River
Severn basin district data. The main download page is
[here](http://environment.data.gov.uk/catchment-planning/RiverBasinDistrict/9).
There are a number of files already identified as potentially relevant.
The first step is to download them so they can be read into R. We shall
do this programmatically so thereby having a script that can be run on
multiple computers and will obtain the original data on its first run.

    library(tidyverse)

Download and verify RBD data
----------------------------

In the code chunk below we specify the data we want to obtain and ensure
it is available and as expected.

    library(tools) # for md5sum
    #NB previous exploration has shown us we end up with zip files
    class_file <- "Data/classifications.zip"
    class_url <- paste0("http://environment.data.gov.uk/catchment-planning/",
                        "RiverBasinDistrict/9/classification?item=all&status=all",
                        "&format=csv")
    class_md5 <- "6d90201502183a9b5bf489d7b6e5a212"

    measures_file <- "Data/measures.zip"
    measures_url <- paste0("http://environment.data.gov.uk/catchment-planning/",
                           "RiverBasinDistrict/9/Action?format=csv")
    measures_md5 <- "ac5eb5ce96871ab4d3dcc59d30a917e1"

    objectives_file <- "Data/objectives.zip"
    objectives_url <- paste0("http://environment.data.gov.uk/catchment-planning/",
                             "RiverBasinDistrict/9/outcome?item=all&status=all",
                             "&format=csv")
    objectives_md5 <- "7d93caa97c087115c9f9f17f1667a8eb"

    reasons_file <- "Data/reasons.zip"
    reasons_url <- paste0("http://environment.data.gov.uk/catchment-planning/",
                          "RiverBasinDistrict/9/ReasonsForNotAchievingGood?item=all",
                          "&format=csv")
    reasons_md5 <- "54f5363d8fad58ed9cdf9cff9c9b7d4c"

    waterbody_file <- "Data/waterbody_links.zip"
    waterbody_url <- paste0("http://environment.data.gov.uk/catchment-planning/",
                            "RiverBasinDistrict/9/pa/csv")
    waterbody_md5 <- "1a6bb38a52e22d04b7898aadcb7181bb"

    if (!dir.exists("Data")) {
        dir.create("Data")
    }

    if (!file.exists(class_file)) {
        download.file(class_url, class_file)
    }

    if (!file.exists(measures_file)) {
        download.file(measures_url, measures_file)
    }

    if (!file.exists(objectives_file)) {
        download.file(objectives_url, objectives_file)
    }

    if (!file.exists(reasons_file)) {
        download.file(reasons_url, reasons_file)
    }

    if (!file.exists(waterbody_file)) {
        download.file(waterbody_url, waterbody_file)
    }

    # check md5
    check_md5 <- function(filename, md5) {
        if (md5sum(filename) != md5) {
            message(filename, " does not match expected md5sum")
        } else {
            message(filename, " matches expected md5sum: ", md5)
        }
    }

    check_md5(class_file, class_md5)

    ## Data/classifications.zip matches expected md5sum: 6d90201502183a9b5bf489d7b6e5a212

    check_md5(measures_file, measures_md5)

    ## Data/measures.zip matches expected md5sum: ac5eb5ce96871ab4d3dcc59d30a917e1

    check_md5(objectives_file, objectives_md5)

    ## Data/objectives.zip matches expected md5sum: 7d93caa97c087115c9f9f17f1667a8eb

    check_md5(reasons_file, reasons_md5)

    ## Data/reasons.zip matches expected md5sum: 54f5363d8fad58ed9cdf9cff9c9b7d4c

    check_md5(waterbody_file, waterbody_md5)

    ## Data/waterbody_links.zip matches expected md5sum: 1a6bb38a52e22d04b7898aadcb7181bb

The md5sum checks above are against the md5sums calculated on an initial
download of the data. Files, particularly online, can change. Successful
md5sum checks here confirm that the files are identical to those
expected by this analysis and thus should yield reproducible results.

Initial data view
-----------------

### Classification data

Reading in the classification file (a CSV within a zip), and performing
a basic check, we see the following.

    classification <- read_csv("Data/classifications.zip")

    ## Parsed with column specification:
    ## cols(
    ##   `River basin district` = col_character(),
    ##   `Management catchment` = col_character(),
    ##   `Operational catchment` = col_character(),
    ##   `Waterbody ID` = col_character(),
    ##   `water body` = col_character(),
    ##   `Water body type` = col_character(),
    ##   Easting = col_integer(),
    ##   Northing = col_integer(),
    ##   Ngr = col_character(),
    ##   `Hydromorphological designation` = col_character(),
    ##   Year = col_integer(),
    ##   Status = col_character(),
    ##   Cycle = col_integer(),
    ##   `Classification Level` = col_character(),
    ##   `Classification Item` = col_character(),
    ##   `classification ID` = col_character(),
    ##   Certainty = col_character(),
    ##   Confidence = col_double(),
    ##   `Investigation Outcome` = col_character()
    ## )

    classification %>% glimpse

    ## Observations: 151,404
    ## Variables: 19
    ## $ `River basin district`           <chr> "Severn", "Severn", "Severn",...
    ## $ `Management catchment`           <chr> "South East Valleys", "South ...
    ## $ `Operational catchment`          <chr> "Ebbw Sirhowy", "Ebbw Sirhowy...
    ## $ `Waterbody ID`                   <chr> "GB109056026910", "GB10905603...
    ## $ `water body`                     <chr> "Ebbw R - conf Ebbw Fach R to...
    ## $ `Water body type`                <chr> "River", "River", "Lake", "Ri...
    ## $ Easting                          <int> 322226, 317475, 312903, 32095...
    ## $ Northing                         <int> 191421, 207225, 211633, 20463...
    ## $ Ngr                              <chr> "ST2222691421", "SO1747507225...
    ## $ `Hydromorphological designation` <chr> "heavily modified", "not desi...
    ## $ Year                             <int> 2015, 2009, 2012, 2014, 2014,...
    ## $ Status                           <chr> "Moderate", "Does not require...
    ## $ Cycle                            <int> 2, 1, 1, 2, 1, 1, 2, 1, 2, 2,...
    ## $ `Classification Level`           <chr> "Component", "Component", "El...
    ## $ `Classification Item`            <chr> "Supporting elements (Surface...
    ## $ `classification ID`              <chr> "http://environment.data.gov....
    ## $ Certainty                        <chr> "No Information", "No Informa...
    ## $ Confidence                       <dbl> NA, NA, NA, 0.00, 1.00, 1.00,...
    ## $ `Investigation Outcome`          <chr> NA, NA, NA, NA, NA, NA, NA, N...

    classification %>% summarise_all(funs(sum(is.na(.)))) %>% gather(column, num_NA)

    ## # A tibble: 19 x 2
    ##                            column num_NA
    ##                             <chr>  <int>
    ##  1           River basin district      0
    ##  2           Management catchment      0
    ##  3          Operational catchment      0
    ##  4                   Waterbody ID      0
    ##  5                     water body      0
    ##  6                Water body type      0
    ##  7                        Easting      0
    ##  8                       Northing      0
    ##  9                            Ngr      0
    ## 10 Hydromorphological designation      0
    ## 11                           Year      0
    ## 12                         Status      0
    ## 13                          Cycle      0
    ## 14           Classification Level      0
    ## 15            Classification Item      0
    ## 16              classification ID      0
    ## 17                      Certainty   3769
    ## 18                     Confidence  71205
    ## 19          Investigation Outcome 147976

    # remove the original endpoint URL
    classification <- classification %>%
        select(-`classification ID`)

From the output above we can see that the column data types have been
reasonably and correctly guessed. The only missing values are in the
Certainty, Confidence, and Investigation Outcome columns. This is no
concern at the moment. The main data issues so far are the lack of a
data dictionary. The interpretation of many columns is obvious, but some
require some care.

A description of the Management catchment can be found in the glossary
[here](http://environment.data.gov.uk/catchment-planning/glossary#m)
where we see a management catchment is

> an amalgamation of a number of river water body catchments that
> provide a management unit at which level actions are applied.
> Management catchments are hydrological management areas. They are
> built up from river water body catchments into similar sized
> management units, of a scale that suits management and planning from
> an Environment Agency perspective. All the land area of England and
> Wales is divided into management catchments, so they include the
> coastline as part of their definition. They do not have a formal
> statutory reporting role.

Similarly, an operation catchment is

> an amalgamation of a small number of river water body catchments.
> Operational catchments are smaller units, as a rule, than management
> catchments. They are used in the economic analysis process to identify
> packages of measures that can be applied to improve the ecological
> status of the water bodies within it

and a water body is

> a unit of surface water, being the whole (or part) of a stream, river
> or canal, lake or reservoir, estuary or stretch of coastal water. A
> groundwater water body is a defined area of an aquifer with geological
> and hydrological boundaries to ensure consistency and avoid
> fragmentation.

The hydromorphology of a water body is described to be

> the hydrological and geomorphological processes and attributes of
> surface water bodies. For example for rivers, hydromorphology
> describes the form and function of the channel as well as its
> connectivity (up and downstream and with groundwater) and flow regime,
> which defines its ability to allow migration of aquatic organisms and
> maintain natural continuity of sediment transport through the fluvial
> system. The Water Framework Directive requires surface waters to be
> managed in such a way as to safeguard their hydrology and
> geomorphology so that ecology is protected.

The classification hierarchy is described clearly and graphically
[here](http://environment.data.gov.uk/catchment-planning/help#help-classification-hierarchy).

### Measures data

The measures data contains a relatively small number of planned measures
(works) as shown below.

    measures <- read_csv("Data/measures.zip")

    ## Parsed with column specification:
    ## cols(
    ##   ID = col_integer(),
    ##   `Measure Type` = col_character(),
    ##   `River Basin District` = col_character(),
    ##   `Management Catchment` = col_character(),
    ##   `Operational Catchment` = col_character(),
    ##   `Waterbody ID` = col_character(),
    ##   `Measure Reference Code` = col_character(),
    ##   `Measure Category 1` = col_character(),
    ##   `Measure Category 2` = col_character(),
    ##   `Measure Category 3` = col_character(),
    ##   Title = col_character(),
    ##   `Estimated start date` = col_date(format = ""),
    ##   `Funding stream` = col_character(),
    ##   `Lead organisation` = col_character(),
    ##   `Sector of lead organisation` = col_character(),
    ##   Status = col_character(),
    ##   `Linked RNAGs` = col_character()
    ## )

    measures %>% glimpse

    ## Observations: 79
    ## Variables: 17
    ## $ ID                            <int> 24281, 25001, 15097, 15103, 1519...
    ## $ `Measure Type`                <chr> "WBLMA", "WBLMA", "WBLMA", "WBLM...
    ## $ `River Basin District`        <chr> "Severn", "Severn", "Severn", "S...
    ## $ `Management Catchment`        <chr> "Severn Middle Worcestershire", ...
    ## $ `Operational Catchment`       <chr> "Shropshire West", "Stour River ...
    ## $ `Waterbody ID`                <chr> "GB109054049390", "GB10905404457...
    ## $ `Measure Reference Code`      <chr> "1.3.16", "3.1.1", "1.3.16", "1....
    ## $ `Measure Category 1`          <chr> "To control or manage point sour...
    ## $ `Measure Category 2`          <chr> "Mitigate/Remediate point source...
    ## $ `Measure Category 3`          <chr> "Alter/change permits for sewage...
    ## $ Title                         <chr> "Additional treatment to reduce ...
    ## $ `Estimated start date`        <date> 2020-03-31, NA, 2020-03-31, 202...
    ## $ `Funding stream`              <chr> "Water company investment progra...
    ## $ `Lead organisation`           <chr> "Waste water treatment", NA, "Wa...
    ## $ `Sector of lead organisation` <chr> "Waste water treatment", NA, "Wa...
    ## $ Status                        <chr> "Affordable", "Affordable", "Aff...
    ## $ `Linked RNAGs`                <chr> "491610", "491801", "482269", "4...

    measures %>% summarise_all(funs(sum(is.na(.)))) %>% gather(column, num_NA)

    ## # A tibble: 17 x 2
    ##                         column num_NA
    ##                          <chr>  <int>
    ##  1                          ID      0
    ##  2                Measure Type      0
    ##  3        River Basin District      0
    ##  4        Management Catchment      0
    ##  5       Operational Catchment      0
    ##  6                Waterbody ID      0
    ##  7      Measure Reference Code      0
    ##  8          Measure Category 1      0
    ##  9          Measure Category 2      0
    ## 10          Measure Category 3      4
    ## 11                       Title      0
    ## 12        Estimated start date     10
    ## 13              Funding stream      3
    ## 14           Lead organisation      3
    ## 15 Sector of lead organisation      3
    ## 16                      Status      0
    ## 17                Linked RNAGs      0

Useful columns definitely seem to be Waterbody ID, which could be linked
back to the classifications data. RNAG means "Reason for not achieving
good" and will link to another table. Most columns are character,
although the estimated start date is correctly read in as a date. There
are a few missing values limited to estimated start date, a measure
category, and funding/organization. The meaning of Measure type (e.g.
"WBLMA") is not currently understood.

### Objectives

The value of the objectives data is less certain. Many columns have
already been seen and the main addition seems to be the ObjectiveType
column, which can hold either the value "Objective" or "Predicted". The
only missing values are in the "Reasons for Alternative Objectives"
column, which does not seem crucial.

    objectives <- read_csv("Data/objectives.zip")

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   Easting = col_integer(),
    ##   Northing = col_integer(),
    ##   Year = col_integer(),
    ##   Cycle = col_integer()
    ## )

    ## See spec(...) for full column specifications.

    objectives %>% glimpse

    ## Observations: 48,011
    ## Variables: 20
    ## $ `River Basin District`               <chr> "Severn", "Severn", "Seve...
    ## $ `River Basin District URI`           <chr> "http://environment.data....
    ## $ `Management Catchment`               <chr> "South East Valleys", "So...
    ## $ `Management Catchment URI`           <chr> "http://environment.data....
    ## $ `Operational Catchment`              <chr> "Ebbw Sirhowy", "Ebbw Sir...
    ## $ `Operational Catchment URI`          <chr> "http://environment.data....
    ## $ `water body`                         <chr> "Carno Reservoir", "Sirho...
    ## $ `Waterbody ID`                       <chr> "GB30940635", "GB10905603...
    ## $ `Water body type`                    <chr> "Lake", "River", "Lake", ...
    ## $ `Hydromorphological Designation`     <chr> "heavily modified", "not ...
    ## $ Easting                              <int> 316324, 318128, 316324, 3...
    ## $ Northing                             <int> 213130, 198659, 213130, 1...
    ## $ Ngr                                  <chr> "SO1632413130", "ST181289...
    ## $ Year                                 <int> 2021, 2021, 2021, 2021, 2...
    ## $ Cycle                                <int> 2, 2, 2, 2, 2, 2, 2, 2, 2...
    ## $ Status                               <chr> "High", "High", "Does not...
    ## $ `Classification Level`               <chr> "Component", "Element", "...
    ## $ `Classification Item`                <chr> "Specific pollutants", "A...
    ## $ ObjectiveType                        <chr> "Predicted", "Predicted",...
    ## $ `Reasons for Alternative Objectives` <chr> NA, NA, NA, NA, NA, NA, N...

    objectives %>% summarise_all(funs(sum(is.na(.)))) %>% gather(column, num_NA)

    ## # A tibble: 20 x 2
    ##                                column num_NA
    ##                                 <chr>  <int>
    ##  1               River Basin District      0
    ##  2           River Basin District URI      0
    ##  3               Management Catchment      0
    ##  4           Management Catchment URI      0
    ##  5              Operational Catchment      0
    ##  6          Operational Catchment URI      0
    ##  7                         water body      0
    ##  8                       Waterbody ID      0
    ##  9                    Water body type      0
    ## 10     Hydromorphological Designation      0
    ## 11                            Easting      0
    ## 12                           Northing      0
    ## 13                                Ngr      0
    ## 14                               Year      0
    ## 15                              Cycle      0
    ## 16                             Status      0
    ## 17               Classification Level      0
    ## 18                Classification Item      0
    ## 19                      ObjectiveType      0
    ## 20 Reasons for Alternative Objectives  45524

    # remove the original endpoint URLs
    objectives <- objectives %>%
        select(-c(`River Basin District URI`,
                  `Management Catchment URI`,
                  `Operational Catchment URI`))

### Reasons

The reasons data is understood to contain data pertaining to "Reasons
for not achieving good". As such, there is an Id column that should be
useful for linking with Linked RNAG in the measures data. Thus these two
datasets together could be interesting for relating water bodies that
failed to achieve good status with measures (planned) for remediating
that.

    reasons <- read_csv("Data/reasons.zip")

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   `Classification Year` = col_integer(),
    ##   Cycle = col_integer(),
    ##   Id = col_integer()
    ## )

    ## See spec(...) for full column specifications.

    reasons %>% glimpse

    ## Observations: 3,514
    ## Variables: 26
    ## $ `River Basin District`   <chr> "Severn", "Severn", "Severn", "Severn...
    ## $ `Management Catchment`   <chr> "South East Valleys", "South East Val...
    ## $ `Operational Catchment`  <chr> "Ebbw Sirhowy", "Ebbw Sirhowy", "Ebbw...
    ## $ `water body`             <chr> "Carno Reservoir", "Carno Reservoir",...
    ## $ `Water body id`          <chr> "GB30940635", "GB30940635", "GB309406...
    ## $ `Water body type`        <chr> "Lake", "Lake", "Lake", "Lake", "Lake...
    ## $ `Classification Year`    <int> 2013, 2013, 2013, 2013, 2015, 2014, 2...
    ## $ Cycle                    <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2...
    ## $ `Classification Status`  <chr> "Moderate or less", "Moderate or less...
    ## $ `Classification Level`   <chr> "Element", "Element", "Element", "Ele...
    ## $ `Classification Element` <chr> "Mitigation Measures Assessment", "Mi...
    ## $ Category                 <chr> "No sector responsible", "No sector r...
    ## $ `Category Certainty`     <chr> "Not applicable", "Not applicable", "...
    ## $ `Reason type`            <chr> "RFF", "RFF", "RFF", "RFF", "RFF", "R...
    ## $ `Business Sector`        <chr> "Not applicable", "Not applicable", "...
    ## $ Swmi                     <chr> "Physical modification", "Physical mo...
    ## $ `Swmi Certainty`         <chr> "Confirmed", "Confirmed", "Confirmed"...
    ## $ Activity                 <chr> "Other (not in list, must add details...
    ## $ `Activity Certainty`     <chr> "Confirmed", "Confirmed", "Confirmed"...
    ## $ `Pressure Tier 1`        <chr> "Water regulation (i. ii.)", "Water r...
    ## $ `Pressure Tier 2`        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, N...
    ## $ Id                       <int> 480952, 480952, 480951, 480951, 51435...
    ## $ `Rnag URL`               <chr> "http://environment.data.gov.uk/catch...
    ## $ `Pressure Tier 3`        <chr> "Physical modification", "Physical mo...
    ## $ `National Swmi Header`   <chr> "Physical modifications", "Physical m...
    ## $ `Investigation outcome`  <chr> "Reasons identified", "Measures ident...

    reasons %>% 
        summarise_all(funs(sum(is.na(.)))) %>% 
        gather(column, num_NA) %>%
        print(n = Inf)

    ## # A tibble: 26 x 2
    ##                    column num_NA
    ##                     <chr>  <int>
    ##  1   River Basin District      0
    ##  2   Management Catchment      0
    ##  3  Operational Catchment      0
    ##  4             water body      0
    ##  5          Water body id      0
    ##  6        Water body type      0
    ##  7    Classification Year      0
    ##  8                  Cycle      0
    ##  9  Classification Status      0
    ## 10   Classification Level      0
    ## 11 Classification Element      0
    ## 12               Category      0
    ## 13     Category Certainty      0
    ## 14            Reason type      0
    ## 15        Business Sector    111
    ## 16                   Swmi      0
    ## 17         Swmi Certainty      0
    ## 18               Activity      0
    ## 19     Activity Certainty      0
    ## 20        Pressure Tier 1   1760
    ## 21        Pressure Tier 2   2064
    ## 22                     Id      0
    ## 23               Rnag URL      0
    ## 24        Pressure Tier 3      0
    ## 25   National Swmi Header    779
    ## 26  Investigation outcome   1028

    # remove original endpoint for RNAG URL (we have the ID)
    reasons <- reasons %>%
        select(-`Rnag URL`)

We see there are currently unknown column values such as "RFF" in the
Reason type column. The columns with names referring to Pressure Tier
are also currently not understood, as as Swmi. There are some missing
values in the Pressure tier columns, for example, but these will be
assessed once the meaning of those columns is better known.

### Waterbody links

    waterbody <- read_csv("Data/waterbody_links.zip")

    ## Parsed with column specification:
    ## cols(
    ##   `River basin district` = col_character(),
    ##   `Management catchment` = col_character(),
    ##   `Operational catchment` = col_character(),
    ##   `Waterbody ID` = col_character(),
    ##   `protected area label` = col_character(),
    ##   `protected area code` = col_character(),
    ##   `protected area type` = col_character(),
    ##   `See also` = col_character()
    ## )

    waterbody %>% glimpse

    ## Observations: 1,140
    ## Variables: 8
    ## $ `River basin district`  <chr> "Severn", "Severn", "Severn", "Severn"...
    ## $ `Management catchment`  <chr> "South East Valleys", "South East Vall...
    ## $ `Operational catchment` <chr> "Ebbw Sirhowy", "Ebbw Sirhowy", "Ebbw ...
    ## $ `Waterbody ID`          <chr> "GB30940635", "GB30940604", "GB3094086...
    ## $ `protected area label`  <chr> "Carno Reservoir", "Llangynidr Reservo...
    ## $ `protected area code`   <chr> "UKGB30940635", "UKGB30940604", "UKGB3...
    ## $ `protected area type`   <chr> "Drinking Water Protected Area", "Drin...
    ## $ `See also`              <chr> NA, NA, NA, NA, NA, NA, NA, "https://d...

    waterbody %>% summarise_all(funs(sum(is.na(.)))) %>% gather(column, num_NA)

    ## # A tibble: 8 x 2
    ##                  column num_NA
    ##                   <chr>  <int>
    ## 1  River basin district      0
    ## 2  Management catchment      0
    ## 3 Operational catchment      0
    ## 4          Waterbody ID      0
    ## 5  protected area label      0
    ## 6   protected area code      0
    ## 7   protected area type      0
    ## 8              See also    961

Data deduplication
------------------

It is clear that there are a number of columns that appear in multiple,
if not all dataframes. These are (with some minor name differences):

-   Water body ID
-   Water body type
-   Water body
-   Operational catchment
-   Management catchment
-   River basin district

Not only does this mean we carry around redundant, duplicated columns,
we also risk confusion with multiple, but subtly different columns
occuring in the same dataframe after any join operation. A convenient
task now is to extract those columns into a separate dataframe and leave
only the water body ID in the original dataframe with which we can join
the data. This is on the understanding that the water body ID is the
unique key. Furthermore, we can check this assumption that water body ID
is unique.

    # Settle on the convention:
    # Waterbody_ID
    # Waterbody_type
    # Waterbody
    # Operational_catchment
    # Management_catchment
    # RBD
    # Easting
    # Northing
    # NGR

    # Assume here the easting, northing, and NGR refer to a particular
    # water body ID.
    class_wb <- classification %>%
        select(Waterbody_ID = `Waterbody ID`,
               Waterbody_type = `Water body type`,
               Waterbody = `water body`,
               Operational_catchment = `Operational catchment`,
               Management_catchment = `Management catchment`,
               RBD = `River basin district`,
               Easting,
               Northing,
               NGR = Ngr) %>%
        distinct

    class_wb %>% count(`Waterbody_ID`) %>% filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

    measures_wb <- measures %>%
        select(Waterbody_ID = `Waterbody ID`,
               Operational_catchment = `Operational Catchment`,
               Management_catchment = `Management Catchment`,
               RBD = `River Basin District`) %>%
        distinct

    measures_wb %>% count(`Waterbody_ID`) %>% filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

    # It's not clear at the moment whether hydromorphological designation
    # is constant for a given water body so we won't extract this now.
    objectives_wb <- objectives %>%
        select(Waterbody_ID = `Waterbody ID`,
               Waterbody_type = `Water body type`,
               Waterbody = `water body`,
               Operational_catchment = `Operational Catchment`,
               Management_catchment = `Management Catchment`,
               RBD = `River Basin District`,
               Easting,
               Northing,
               NGR = Ngr) %>%
        distinct

    objectives_wb %>% count(`Waterbody_ID`) %>% filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

    reasons_wb <- reasons %>%
        select(Waterbody_ID = `Water body id`,
               Waterbody_type = `Water body type`,
               Waterbody = `water body`,
               Operational_catchment = `Operational Catchment`,
               Management_catchment = `Management Catchment`,
               RBD = `River Basin District`) %>%
        distinct

    reasons_wb %>% count(`Waterbody_ID`) %>% filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

    waterbody_wb <- waterbody %>%
        select(Waterbody_ID = `Waterbody ID`,
               Operational_catchment = `Operational catchment`,
               Management_catchment = `Management catchment`,
               RBD = `River basin district`) %>%
        distinct

    waterbody_wb %>% count(`Waterbody_ID`) %>% filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

    class_meas <- full_join(class_wb, measures_wb)

    ## Joining, by = c("Waterbody_ID", "Operational_catchment", "Management_catchment", "RBD")

    class_meas %>% filter(is.na(Waterbody_type))

    ## # A tibble: 3 x 9
    ##                    Waterbody_ID Waterbody_type Waterbody
    ##                           <chr>          <chr>     <chr>
    ## 1 GB109053027640|GB109053027620           <NA>      <NA>
    ## 2 GB109053021840|GB109053022101           <NA>      <NA>
    ## 3 GB109053022200|GB109053022190           <NA>      <NA>
    ## # ... with 6 more variables: Operational_catchment <chr>,
    ## #   Management_catchment <chr>, RBD <chr>, Easting <int>, Northing <int>,
    ## #   NGR <chr>

    # This highlights something I remember - some IDs seem combined!
    # Find them and decide how to deal with them!

The combined water body IDs originate from the measures table, as shown
below:

    measures %>% 
        filter(str_detect(`Waterbody ID`, "\\|")) %>% 
        glimpse

    ## Observations: 4
    ## Variables: 17
    ## $ ID                            <int> 39579, 39575, 39573, 39577
    ## $ `Measure Type`                <chr> "WBLMA", "WBLMA", "WBLMA", "WBLMA"
    ## $ `River Basin District`        <chr> "Severn", "Severn", "Severn", "S...
    ## $ `Management Catchment`        <chr> "Avon Bristol and Somerset North...
    ## $ `Operational Catchment`       <chr> "Avon Bristol Rural", "Avon Bris...
    ## $ `Waterbody ID`                <chr> "GB109053027640|GB109053027620",...
    ## $ `Measure Reference Code`      <chr> "1.3.5", "1.3.5", "1.3.5", "1.3.5"
    ## $ `Measure Category 1`          <chr> "To control or manage point sour...
    ## $ `Measure Category 2`          <chr> "Mitigate/Remediate point source...
    ## $ `Measure Category 3`          <chr> "Install nutrient reduction", "I...
    ## $ Title                         <chr> "Install treatment to reduce con...
    ## $ `Estimated start date`        <date> 2018-12-31, 2018-12-31, 2018-12...
    ## $ `Funding stream`              <chr> "Water company investment progra...
    ## $ `Lead organisation`           <chr> "Waste water treatment", "Waste ...
    ## $ `Sector of lead organisation` <chr> "Waste water treatment", "Waste ...
    ## $ Status                        <chr> "Affordable", "Affordable", "Aff...
    ## $ `Linked RNAGs`                <chr> "489368|489363", "521029|489283"...

    # e.g.
    measures %>%
        filter(ID == 39579) %>%
        glimpse

    ## Observations: 1
    ## Variables: 17
    ## $ ID                            <int> 39579
    ## $ `Measure Type`                <chr> "WBLMA"
    ## $ `River Basin District`        <chr> "Severn"
    ## $ `Management Catchment`        <chr> "Avon Bristol and Somerset North...
    ## $ `Operational Catchment`       <chr> "Avon Bristol Rural"
    ## $ `Waterbody ID`                <chr> "GB109053027640|GB109053027620"
    ## $ `Measure Reference Code`      <chr> "1.3.5"
    ## $ `Measure Category 1`          <chr> "To control or manage point sour...
    ## $ `Measure Category 2`          <chr> "Mitigate/Remediate point source...
    ## $ `Measure Category 3`          <chr> "Install nutrient reduction"
    ## $ Title                         <chr> "Install treatment to reduce con...
    ## $ `Estimated start date`        <date> 2018-12-31
    ## $ `Funding stream`              <chr> "Water company investment progra...
    ## $ `Lead organisation`           <chr> "Waste water treatment"
    ## $ `Sector of lead organisation` <chr> "Waste water treatment"
    ## $ Status                        <chr> "Affordable"
    ## $ `Linked RNAGs`                <chr> "489368|489363"

Looking at the rows containing the "|" character, we find there are four
such measures rows, each with their own ID. We also see the Linked RNAGs
column contains merged IDs. From this we piece together the picture that
each of these four measures are associated with more than one water body
ID and its reason for not achieving good. This makes it harder to
associate a water body ID with an associated measure and so we prefer to
split these records out into separate rows. We can do this by splitting
and unnesting on the two affected columns:

    measures <- measures %>% 
        filter(str_detect(`Waterbody ID`, "\\|")) %>% 
        mutate(`Waterbody ID` = str_split(`Waterbody ID`, "\\|"), 
               `Linked RNAGs` = str_split(`Linked RNAGs`, "\\|")) %>% 
        unnest(`Waterbody ID`, `Linked RNAGs`)

    # e.g.
    measures %>%
        filter(ID == 39579) %>%
        glimpse

    ## Observations: 2
    ## Variables: 17
    ## $ ID                            <int> 39579, 39579
    ## $ `Measure Type`                <chr> "WBLMA", "WBLMA"
    ## $ `River Basin District`        <chr> "Severn", "Severn"
    ## $ `Management Catchment`        <chr> "Avon Bristol and Somerset North...
    ## $ `Operational Catchment`       <chr> "Avon Bristol Rural", "Avon Bris...
    ## $ `Measure Reference Code`      <chr> "1.3.5", "1.3.5"
    ## $ `Measure Category 1`          <chr> "To control or manage point sour...
    ## $ `Measure Category 2`          <chr> "Mitigate/Remediate point source...
    ## $ `Measure Category 3`          <chr> "Install nutrient reduction", "I...
    ## $ Title                         <chr> "Install treatment to reduce con...
    ## $ `Estimated start date`        <date> 2018-12-31, 2018-12-31
    ## $ `Funding stream`              <chr> "Water company investment progra...
    ## $ `Lead organisation`           <chr> "Waste water treatment", "Waste ...
    ## $ `Sector of lead organisation` <chr> "Waste water treatment", "Waste ...
    ## $ Status                        <chr> "Affordable", "Affordable"
    ## $ `Waterbody ID`                <chr> "GB109053027640", "GB109053027620"
    ## $ `Linked RNAGs`                <chr> "489368", "489363"

    #regenerate the measures_wb table
    measures_wb <- measures %>%
        select(Waterbody_ID = `Waterbody ID`,
               Operational_catchment = `Operational Catchment`,
               Management_catchment = `Management Catchment`,
               RBD = `River Basin District`) %>%
        distinct

    measures_wb %>% count(`Waterbody_ID`) %>% filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

We now have a separate row for each water body ID and Linked RNAG at the
cost of duplicate measure IDs. We should remember this if analysing data
at the measure-level in future.

The classification and objectives tables contain a full set of our
"water body" columns, namely:

-   Waterbody ID
-   Waterbody type
-   Waterbody
-   Operational catchment
-   Management catchment
-   River basin district
-   Easting
-   Northing
-   NGR

If we combine all these columns for both of these tables, we hope to get
no duplicated waterbody IDs, thus:

    full_wb <- bind_rows(class_wb, objectives_wb) %>%
        distinct 

    full_wb %>% 
        count(Waterbody_ID) %>% 
        filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

The above result shows that combining all rows from both tables yields
no duplicated water body IDs; each ID is associated with a unique
combination of type, name, location etc.

Similarly, the measures and waterbody tables share the columns

-   Waterbody ID
-   Operational catchment
-   Management catchment
-   River basin district

and we can repeat the above process to check for duplicates (non-unique
waterbody ID) thus:

    min_wb <- bind_rows(measures_wb, waterbody_wb) %>%
        distinct 

    min_wb %>% 
        count(Waterbody_ID) %>% 
        filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

The above null result confirms there are no duplicated waterbody IDs
(with different values in the other columns).

The reasons table contains columns for water body type and waterbody
that our new min\_wb does not. If we were to simply combine rows we
would possibly end up with duplicate waterbody IDs because of some NAs
for values in the min\_wb table. To merge these tables and check for
uniqueness of the waterbody ID we can employ another method and perform
a join that preserves all rows but effectives merges any duplicates:

    min_wb <- min_wb %>% 
        full_join(reasons_wb)

    ## Joining, by = c("Waterbody_ID", "Operational_catchment", "Management_catchment", "RBD")

    min_wb %>% glimpse

    ## Observations: 728
    ## Variables: 6
    ## $ Waterbody_ID          <chr> "GB109053027640", "GB109053027620", "GB1...
    ## $ Operational_catchment <chr> "Avon Bristol Rural", "Avon Bristol Rura...
    ## $ Management_catchment  <chr> "Avon Bristol and Somerset North Streams...
    ## $ RBD                   <chr> "Severn", "Severn", "Severn", "Severn", ...
    ## $ Waterbody_type        <chr> "River", "River", "River", "River", "Riv...
    ## $ Waterbody             <chr> "Cowage Bk - source to conf unnamed trib...

    min_wb %>%
        count(Waterbody_ID) %>%
        filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

    min_wb %>%
        summarise_all(funs(sum(is.na(.)))) %>%
        gather(column, num_NA)

    ## # A tibble: 6 x 2
    ##                  column num_NA
    ##                   <chr>  <int>
    ## 1          Waterbody_ID      0
    ## 2 Operational_catchment      0
    ## 3  Management_catchment      0
    ## 4                   RBD      0
    ## 5        Waterbody_type     88
    ## 6             Waterbody     88

The above results show that after the join, there are no rows with
duplicated waterbody ID and no rows where waterbody ID is missing. Thus
all rows describe a one-to-one mapping of waterbody ID to other
parameters. Note there are some waterbody IDs for which waterbody type
and waterbody are unknown.

Finally we can employ the same method again to do a last join between
the first full set of waterbody parameters and this recent, less
comprehensive, set of parameters.

    full_wb <- full_wb %>% 
        full_join(min_wb)

    ## Joining, by = c("Waterbody_ID", "Waterbody_type", "Waterbody", "Operational_catchment", "Management_catchment", "RBD")

    full_wb %>% glimpse

    ## Observations: 885
    ## Variables: 9
    ## $ Waterbody_ID          <chr> "GB109056026910", "GB109056032900", "GB3...
    ## $ Waterbody_type        <chr> "River", "River", "Lake", "River", "Rive...
    ## $ Waterbody             <chr> "Ebbw R - conf Ebbw Fach R to Maes-glas"...
    ## $ Operational_catchment <chr> "Ebbw Sirhowy", "Ebbw Sirhowy", "Ebbw Si...
    ## $ Management_catchment  <chr> "South East Valleys", "South East Valley...
    ## $ RBD                   <chr> "Severn", "Severn", "Severn", "Severn", ...
    ## $ Easting               <int> 322226, 317475, 312903, 320953, 318128, ...
    ## $ Northing              <int> 191421, 207225, 211633, 204633, 198659, ...
    ## $ NGR                   <chr> "ST2222691421", "SO1747507225", "SO12903...

    full_wb %>%
        count(Waterbody_ID) %>%
        filter(n > 1)

    ## # A tibble: 88 x 2
    ##      Waterbody_ID     n
    ##             <chr> <int>
    ##  1 GB109053022030     2
    ##  2 GB109053022300     2
    ##  3 GB109053027460     2
    ##  4 GB109053027680     2
    ##  5 GB109053027690     2
    ##  6 GB109053027780     2
    ##  7 GB109053027800     2
    ##  8 GB109054026510     2
    ##  9 GB109054026610     2
    ## 10 GB109054032470     2
    ## # ... with 78 more rows

    full_wb %>%
        summarise_all(funs(sum(is.na(.)))) %>%
        gather(column, num_NA)

    ## # A tibble: 9 x 2
    ##                  column num_NA
    ##                   <chr>  <int>
    ## 1          Waterbody_ID      0
    ## 2        Waterbody_type     88
    ## 3             Waterbody     88
    ## 4 Operational_catchment      0
    ## 5  Management_catchment      0
    ## 6                   RBD      0
    ## 7               Easting     88
    ## 8              Northing     88
    ## 9                   NGR     88

The above results show us there are now 88 duplicated waterbody ID and,
by a suspicious coincidence, 88 NAs for waterbody type, waterbody,
easting, northing, and NGR. A spot check of the first duplicated
Waterbody ID shows that, indeed, it is duplicated because one set of
values is NA:

    full_wb %>% 
        select(Waterbody_ID, Waterbody_type, Waterbody, Easting, Northing, NGR) %>% 
        filter(Waterbody_ID == "GB109053022030") %>% 
        glimpse

    ## Observations: 2
    ## Variables: 6
    ## $ Waterbody_ID   <chr> "GB109053022030", "GB109053022030"
    ## $ Waterbody_type <chr> "River", NA
    ## $ Waterbody      <chr> "Buckland Bk - source to conf Mells R", NA
    ## $ Easting        <int> 375858, NA
    ## $ Northing       <int> 151518, NA
    ## $ NGR            <chr> "ST7585851518", NA

We can attempt to resolve this with the following recipe:

1.  group by waterbody ID
2.  mutate each column affected with NAs, replacing NAs with the first
    non-NA
3.  retain only distinct rows

The step of taking the first NA value ensures the method should work
even if somehow there were multiple non-NA values. It would then be
arbitrary which value was used, but this won't matter if the values are
the same. If the values are different, a problem will still show up
later on.

    # It's worth documenting what this mutate_at is doing:
    # The first argument is the list of columns we want to operate on,
    # the . denotes the column currently being operated on,
    # the rule is then simply "if a value is NA, replace it with the
    # first non-NA value, else leave it unchanged.
    # (NB sometimes even the more experience of us are amazed and impressed
    # that something works as intended)!
    full_wb <- full_wb %>% 
        group_by(Waterbody_ID) %>% 
        mutate_at(c("Waterbody_type", "Waterbody", "Easting", "Northing", "NGR"),
                  funs(if_else(is.na(.), .[!is.na(.)][1], .))) %>% 
        ungroup

    full_wb %>% 
        select(Waterbody_ID, Waterbody_type, Waterbody, Easting, Northing, NGR) %>% 
        filter(Waterbody_ID == "GB109053022030") %>% 
        glimpse

    ## Observations: 2
    ## Variables: 6
    ## $ Waterbody_ID   <chr> "GB109053022030", "GB109053022030"
    ## $ Waterbody_type <chr> "River", "River"
    ## $ Waterbody      <chr> "Buckland Bk - source to conf Mells R", "Buckla...
    ## $ Easting        <int> 375858, 375858
    ## $ Northing       <int> 151518, 151518
    ## $ NGR            <chr> "ST7585851518", "ST7585851518"

    # now that we've rechecked the above ID, we can remove duplicates
    full_wb <- distinct(full_wb)

    full_wb %>%
        count(Waterbody_ID) %>%
        filter(n > 1)

    ## # A tibble: 0 x 2
    ## # ... with 2 variables: Waterbody_ID <chr>, n <int>

    full_wb %>%
        summarise_all(funs(sum(is.na(.)))) %>%
        gather(column, num_NA)

    ## # A tibble: 9 x 2
    ##                  column num_NA
    ##                   <chr>  <int>
    ## 1          Waterbody_ID      0
    ## 2        Waterbody_type      0
    ## 3             Waterbody      0
    ## 4 Operational_catchment      0
    ## 5  Management_catchment      0
    ## 6                   RBD      0
    ## 7               Easting      0
    ## 8              Northing      0
    ## 9                   NGR      0

    full_wb %>% saveRDS("Data/waterbody.rds")

This seems to confirm we have now gathered together and merged all our
waterbody details into a single table with no duplicated waterbody ID
and no missing values!

Final data tables
-----------------

We can now create our stripped down tables to be accompanied by this new
table of waterbody details.

    classification <- classification %>%
        select(-c(`River basin district`, 
                  `Management catchment`, 
                  `Operational catchment`, 
                  `water body`, 
                  `Water body type`, 
                  `Easting`, 
                  `Northing`, 
                  `Ngr`)) %>%
        rename(Waterbody_ID = `Waterbody ID`)

    classification %>% glimpse

    ## Observations: 151,404
    ## Variables: 10
    ## $ Waterbody_ID                     <chr> "GB109056026910", "GB10905603...
    ## $ `Hydromorphological designation` <chr> "heavily modified", "not desi...
    ## $ Year                             <int> 2015, 2009, 2012, 2014, 2014,...
    ## $ Status                           <chr> "Moderate", "Does not require...
    ## $ Cycle                            <int> 2, 1, 1, 2, 1, 1, 2, 1, 2, 2,...
    ## $ `Classification Level`           <chr> "Component", "Component", "El...
    ## $ `Classification Item`            <chr> "Supporting elements (Surface...
    ## $ Certainty                        <chr> "No Information", "No Informa...
    ## $ Confidence                       <dbl> NA, NA, NA, 0.00, 1.00, 1.00,...
    ## $ `Investigation Outcome`          <chr> NA, NA, NA, NA, NA, NA, NA, N...

    classification %>% saveRDS("Data/classification.rds")

    measures <- measures %>%
        select(-c(`River Basin District`,
                  `Management Catchment`,
                  `Operational Catchment`)) %>%
        rename(Waterbody_ID = `Waterbody ID`)

    measures %>% glimpse

    ## Observations: 8
    ## Variables: 14
    ## $ ID                            <int> 39579, 39579, 39575, 39575, 3957...
    ## $ `Measure Type`                <chr> "WBLMA", "WBLMA", "WBLMA", "WBLM...
    ## $ `Measure Reference Code`      <chr> "1.3.5", "1.3.5", "1.3.5", "1.3....
    ## $ `Measure Category 1`          <chr> "To control or manage point sour...
    ## $ `Measure Category 2`          <chr> "Mitigate/Remediate point source...
    ## $ `Measure Category 3`          <chr> "Install nutrient reduction", "I...
    ## $ Title                         <chr> "Install treatment to reduce con...
    ## $ `Estimated start date`        <date> 2018-12-31, 2018-12-31, 2018-12...
    ## $ `Funding stream`              <chr> "Water company investment progra...
    ## $ `Lead organisation`           <chr> "Waste water treatment", "Waste ...
    ## $ `Sector of lead organisation` <chr> "Waste water treatment", "Waste ...
    ## $ Status                        <chr> "Affordable", "Affordable", "Aff...
    ## $ Waterbody_ID                  <chr> "GB109053027640", "GB10905302762...
    ## $ `Linked RNAGs`                <chr> "489368", "489363", "521029", "4...

    measures %>% saveRDS("Data/measures.rds")

    objectives <- objectives %>%
        select(-c(`River Basin District`,
                  `Management Catchment`,
                  `Operational Catchment`,
                  `water body`,
                  `Water body type`,
                  Easting,
                  Northing,
                  Ngr)) %>%
        rename(Waterbody_ID = `Waterbody ID`)

    objectives %>% glimpse

    ## Observations: 48,011
    ## Variables: 9
    ## $ Waterbody_ID                         <chr> "GB30940635", "GB10905603...
    ## $ `Hydromorphological Designation`     <chr> "heavily modified", "not ...
    ## $ Year                                 <int> 2021, 2021, 2021, 2021, 2...
    ## $ Cycle                                <int> 2, 2, 2, 2, 2, 2, 2, 2, 2...
    ## $ Status                               <chr> "High", "High", "Does not...
    ## $ `Classification Level`               <chr> "Component", "Element", "...
    ## $ `Classification Item`                <chr> "Specific pollutants", "A...
    ## $ ObjectiveType                        <chr> "Predicted", "Predicted",...
    ## $ `Reasons for Alternative Objectives` <chr> NA, NA, NA, NA, NA, NA, N...

    objectives %>% saveRDS("Data/objectives.rds")

    reasons <- reasons %>%
        select(-c(`River Basin District`,
                  `Management Catchment`,
                  `Operational Catchment`,
                  `water body`,
                  `Water body type`)) %>%
        rename(Waterbody_ID = `Water body id`)

    reasons %>% glimpse

    ## Observations: 3,514
    ## Variables: 20
    ## $ Waterbody_ID             <chr> "GB30940635", "GB30940635", "GB309406...
    ## $ `Classification Year`    <int> 2013, 2013, 2013, 2013, 2015, 2014, 2...
    ## $ Cycle                    <int> 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2...
    ## $ `Classification Status`  <chr> "Moderate or less", "Moderate or less...
    ## $ `Classification Level`   <chr> "Element", "Element", "Element", "Ele...
    ## $ `Classification Element` <chr> "Mitigation Measures Assessment", "Mi...
    ## $ Category                 <chr> "No sector responsible", "No sector r...
    ## $ `Category Certainty`     <chr> "Not applicable", "Not applicable", "...
    ## $ `Reason type`            <chr> "RFF", "RFF", "RFF", "RFF", "RFF", "R...
    ## $ `Business Sector`        <chr> "Not applicable", "Not applicable", "...
    ## $ Swmi                     <chr> "Physical modification", "Physical mo...
    ## $ `Swmi Certainty`         <chr> "Confirmed", "Confirmed", "Confirmed"...
    ## $ Activity                 <chr> "Other (not in list, must add details...
    ## $ `Activity Certainty`     <chr> "Confirmed", "Confirmed", "Confirmed"...
    ## $ `Pressure Tier 1`        <chr> "Water regulation (i. ii.)", "Water r...
    ## $ `Pressure Tier 2`        <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, N...
    ## $ Id                       <int> 480952, 480952, 480951, 480951, 51435...
    ## $ `Pressure Tier 3`        <chr> "Physical modification", "Physical mo...
    ## $ `National Swmi Header`   <chr> "Physical modifications", "Physical m...
    ## $ `Investigation outcome`  <chr> "Reasons identified", "Measures ident...

    reasons %>% saveRDS("Data/reasons.rds")

    waterbody <- waterbody %>%
        select(-c(`River basin district`,
                  `Management catchment`,
                  `Operational catchment`)) %>%
        rename(Waterbody_ID = `Waterbody ID`)

    waterbody %>% glimpse

    ## Observations: 1,140
    ## Variables: 5
    ## $ Waterbody_ID           <chr> "GB30940635", "GB30940604", "GB30940869...
    ## $ `protected area label` <chr> "Carno Reservoir", "Llangynidr Reservoi...
    ## $ `protected area code`  <chr> "UKGB30940635", "UKGB30940604", "UKGB30...
    ## $ `protected area type`  <chr> "Drinking Water Protected Area", "Drink...
    ## $ `See also`             <chr> NA, NA, NA, NA, NA, NA, NA, "https://de...

    # more appropriate name for this file as it relates
    # water body IDs to protected area details
    waterbody %>% saveRDS("Data/protected_area.rds")

Summary
-------

The data seem to be of general good quality. The first issue was
understanding and interpreting some columns. There will be some work
remaining on this. The next issue was identifying some duplicate columns
across the various tables and some work was put in to extract those
columns into a new table and check for any duplicates and other issues,
one of which was found to be some IDs being merged in one table.
