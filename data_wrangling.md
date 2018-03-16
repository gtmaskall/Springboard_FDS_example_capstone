RBD/RBMP data
=============

This report documents the process of obtaining and preparing the River
Severn basin district data. The main download page is
[here](http://environment.data.gov.uk/catchment-planning/RiverBasinDistrict/9).
There are a number of files already identified as potentially relevant.
The first step is to download them so they can be read into R. We shall
do this programmatically so thereby having a script that be run on
multiple computers and will obtain the original data on its first run.

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