# Introduction

This document outlines my three initial capstone project ideas.

# Project outlines

These project idea outlines are not completely formed, but hopefully give enough of
a description to faciliate a decision as to which to consider in more depth.

## UK water quality

With climate change, weather events seem more extreme. We've seen many incidents of
heavy rain causing flash floods, likely exacerbated by urban development. Urban development
also can add to pollution and demand for clean water. Inland water
courses also face conflicting pressures for water use by farming, by swimmers and anglers, whilst
protecting resources for wildlife. This project idea would aim to predict aspects of
water quality to help better manage resources or take early action to maintain water quantity
and/or quality. Interesting looking data for the UK are available
[here](http://environment.data.gov.uk/water-quality/view/download#). There seem to be quite a lot
of available data files, but each one is quite self-contained for a given area and year.
Adjacent land use data isn't included, and neither is weather data, but an initial approach
could be to define a water quality metric to be dissolved oxygen, or perhaps nitrite or
ammonia, and see if these can be predicted in advance by monitoring some other parameter.

## UK road safety data

Cars have become safer over time, with improved standards. There are more vehicles on the
roads than ever. Having a few drinks before driving, pretty common and accepted in a previous
generation, is much less acceptable now. There are frequent debates raised on whether the
blood alcohol limit should be further reduced. Seatbelt use became compulsory within my memory.
The landscape around road accidents is clearly one with many aspects that have changed
considerably over time. This project idea would explore such factors. Possible main questions
might be to consider the evidence in favour of reducing the blood alcohol limit or perhaps
to advise on the safest model of car to buy. The main data page is 
[here](https://data.gov.uk/dataset/road-accidents-safety-data). There are quite a lot of files
here. A complication is that they don't seem self-contained; many variables are encoded and
require merging with metadata in an excel file. This would complicate any analysis.

## Plastic carrier bag charge effect

From 5 October 2015, large retailers in England have been required to charge at least 5p for
single-use plastic carrier bags. This idea would look at whether this change in the law can
be said to have reduced the amount of plastic litter. Some reporting data from retailers is
available [here](https://data.gov.uk/dataset/single-use-plastic-carrier-bags-charge-data-for-england).
There are some interesting aspects to this dataset. For example, the number of bags, the 
proceeds of the charge, and whether there were other business costs associated with the
introduction of the charge are columns in the data. However, whilst some interesting
statistics could be compiled, there doesn't seem much scope for interesting machine learning.
This would seem more immediately appealing if more data regarding bags issued before and
after were available, or some external source of data regarding plastic bag litter existed.
