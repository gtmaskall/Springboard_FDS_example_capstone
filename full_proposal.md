# UK river basin management plan review

## Introduction

### The Water Framework Directive

>Rivers, lakes and coastal waters are vital natural resources: they provide 
>drinking water, crucial habitats for many different types of wildlife, and 
>are an important resource for industry and recreation. A significant proportion 
>of them are environmentally damaged or under threat. Protecting and improving 
>the environment is an important part of achieving sustainable development and 
>is vital for the long term health, well being and prosperity of everyone. 
>The EC Water Framework Directive  is a welcome and radical improvement on 
>earlier, piecemeal EU water legislation. It expands the scope of water 
>protection to all waters and sets out clear objectives that must be achieved 
>by specified dates.
>
>In October 2000 the 'Directive 2000/60/EC of the European Parliament and of 
>the Council of 23 October 2000 establishing a framework for Community action 
>in the field of water policy' (Water Framework Directive or WFD) was adopted 
>and came into force in December 2000. The purpose of the Directive is to 
>establish a framework for the protection of inland surface waters (rivers and 
>lakes), transitional waters (estuaries), coastal waters and groundwater. It 
>will ensure that all aquatic ecosystems and, with regard to their water needs, 
>terrestrial ecosystems and wetlands meet 'good status' by 2015.
>
>The Directive requires Member States to establish river basin districts (RBD) and for 
>each of these a river basin management plan (RBMP). The Directive envisages a 
>cyclical process where river basin management plans are prepared, implemented 
>and reviewed every six years. There are four distinct elements to the river 
>basin planning cycle: characterisation and assessment of impacts on river 
>basin districts; environmental monitoring; the setting of environmental 
>objectives; and the design and implementation of the programme of measures 
>needed to achieve them.

>JNCC's work in relation to the Water Framework Directive is discharged through
>various groups. These include the Inter-Agency Water Framework Directive Nature
>Conservation Group  (NCG), and the UK Technical Advisory Group's (UKTAG’s)
>Marine Task Team (MTT) and its Alien Species Group (ASG).

(Source: [JNCC](http://jncc.defra.gov.uk/page-1375))

### River basin districts

>A RBD covers an entire river system, including river, lake, groundwater,
>estuarine and coastal water bodies. The RBD RBMPs are designed to protect and
>improve the quality of our water environment. Good quality water is essential
>for wildlife, agriculture and business to thrive. And is one of the means for
>boosting regeneration (both structural and economic), recreation and tourism.
>
>These updated 2015 plans build on the work already done to protect and improve
>over 9,320 miles of our rivers over the last 5 years. They set out how
>a minimum of 680 (14%) of waters will improve over the next 6 years from around
>£3 billion investment.
>
>The RBMPs support the government’s framework for the 25-year environment plan.
>And will allow local communities to find more cost-effective ways to take
>action to further improve our water environment.

(Source: [UK government](https://www.gov.uk/government/collections/river-basin-management-plans-2015))

## Scope of project

ACME data science consulting has been engaged on behalf of the UK
Technical Advisory Group (UKTAG) to conduct a review of the basin management
plan for the Severn river basin as part of the six-year framework cycle.
The work will review the planned investments in the context of tackling
priority areas.

* Are the currently planned measures still the most appropriate?
* Do the planned measures omit any locations that require them?
    * This may feed into preliminary work planning measures for the next 
    improvement cycle

It will also investigate some novel approaches to monitoring
and predicting water quality issues so preventative measures may be taken in
a timely fashion. In a time of pressured budgets, a finer control of water
quality may allow some planned works to be delayed, or other ones,
perhaps in more sensitive areas, be advanced.
Better modeling of the response of water quality to key parameters will 
help choose the most appropriate engineering solution for a given
location. 

## Key data resources

The main sources of data currently identified for this project include the
following RBD-level data for the Severn basin. These datasets come in the 
categories listed below.

* Classification: the status of water bodies (e.g. "Good" or "Does Not 
Support Good")
* Measures: details of planned improvement measures
* Reasons for not achieving good status: why a water body was deemed to be 
below required standard
* Water body / protected area linkage: associations between water bodies and
certain protected areas for prioritizing.

The data files are already available, but some additional work is required
to obtain associated metadata.

## Outcomes

The first set of outcomes from this study will use 
[RBMP-level data](http://environment.data.gov.uk/catchment-planning/RiverBasinDistrict/9)
to

* provide reassurance that the current RBMP is appropriate and on track to 
deliver the expected objectives, and
* highlight any areas of concern and offer remediation steps.

The second set of outcomes will use Environment Agency 
[water quality archive data](http://environment.data.gov.uk/water-quality/view/download#)
for selected regions to obtain a number of water quality parameters over location
and time. The water quality data do not contain common location identifiers with the
RBD data, which will add a hurdle to joining those data. Easting and Northing are
given for both sets, though, meaning proximal common locations can be identified.
These water quality parameters will form input to models used for inference
and prediction.

All of these data are readily available for download as CSV (or zip archives of).

## Deliverables

The client will be provided with all analysis code, modelling code, a final technical
report and an executive summary slide deck.

## Links

[Directive 2000/60/EC of the European Parliament and of the Council of 23
October 2000 establishing a framework for Community action in the field of
water policy](http://eur-lex.europa.eu/legal-content/EN/ALL/?uri=CELEX:32000L0060)
