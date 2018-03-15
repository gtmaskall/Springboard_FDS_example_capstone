# Proposal development notes

## Introduction

This note is auxiliary documentation supporting the development of the
[full project proposal](full_proposal.md). It is not a document expected 
from students doing their project.
The distinction of a full proposal, as opposed to the
initial [proposal](proposal.md), was made when additional information came to light
that started to generate more concrete ideas. Whilst students aren't expected to
go to great lengths in obtaining supplementary information that leads to a professional
style proposal to a client, it seemed worth capturing some of the processes involved
in the journey to develop a really interesting, real-world project. It is entirely
acceptable, indeed likely and encouraged, that an initial proposal will be refined
as the student starts digging into the project.

## Initial idea

We started with a proposal that supposed the client would be the UK Environment Agency
and the nature of the problem would be a predictive, or classification, 
one with water quality the target response.
Open data on water quality is, indeed, readily available for 
[download](http://environment.data.gov.uk/water-quality/view/download#).
The data comprise measurements of a number of water quality parameters for many sections
of water. The parameters include dissolved oxygen, nitrate, and pH, to name but three.
An obvious target metric of interest for aquatic life is dissolved oxygen, but it is not
immediately obvious whether the other parameters are also targets of interest or merely
potential predictors for the target of interest.

## Gaining added context

Initial background research was started with the aim of finding (regulatory) threshold
level(s) for the parameters. A basic question in mind here was 
"What is the minimum level of dissolved oxygen required for sustaining aquatic life?" 
This would be useful knowledge to provide a value to look for in the data that could 
trigger a warning. This quickly lead to a document on 
[implementing the Water Framework Directive](https://www.gov.uk/government/uploads/system/uploads/attachment_data/file/307788/river-basin-planning-standards.pdf) 
and the UK Technical Advisory Group on the Water Framework Directive, as per this report on 
[environmental standards](https://www.wfduk.org/sites/default/files/Media/Environmental%20standards/Environmental%20standards%20phase%201_Finalv2_010408.pdf).

We also encountered the Joint Nature Conservation Committee, JNCC, and its
[relevance](http://jncc.defra.gov.uk/page-1375) to the Water Framework Directive.
This was a useful page for background information:

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
>The Directive requires Member States to establish river basin districts and for 
>each of these a river basin management plan. The Directive envisages a 
>cyclical process where river basin management plans are prepared, implemented 
>and reviewed every six years. There are four distinct elements to the river 
>basin planning cycle: characterisation and assessment of impacts on river 
>basin districts; environmental monitoring; the setting of environmental 
>objectives; and the design and implementation of the programme of measures 
>needed to achieve them.

...

>JNCC's work in relation to the Water Framework Directive is discharged through 
>various groups. These include the Inter-Agency Water Framework Directive 
>Nature Conservation Group  (NCG), and the UK Technical Advisory Group's 
>(UKTAG’s) Marine Task Team (MTT) and its Alien Species Group (ASG).

## Developing the proposal

By skim-reading a few websites and following our noses, as a consultant data 
scientist coming from outside the environmental water quality field, we have 
already started to piece together some very useful background knowledge:

* New regulations were adopted in 2000 (interestingly, and most likely not
coincidentally, this is the year the open WIMS data starts)
* There was a goal for achieving 'good status' by 2015
* There are river basin districts defined (somewhere)
* The river basins have management plans
* These are expected to be reviewed every six years
* There are distinct elements to this cycle
    1. characterisation/assessment of impact
    2. monitoring
    3. setting objectives (maybe standards/limits?)
    4. design/implementation of programmes to achieve the objectives.

This gives us an outline for a highly plausible scenario in which we might be
undertaking a data science project in this field: we might be hired to work for
UKTAG in a periodic review of the river basin management plans. It gives us four
aspects of the task to tackle. It also gives us some more homework to do!

* How are the river basins defined?
* Can we find this information and relate it back to the open WIMS data?
* Do we get any more useful information from the river basins (if we can find them)?

It turns out river basin management plans are quite easy to 
[find](https://www.google.co.uk/search?dcr=0&q=uk+river+basin+management+plans&sa=X&ved=0ahUKEwi9uKC-rc7ZAhUVM8AKHYvnCc0Q7xYIJigA&biw=1600&bih=725). 
The first result brings us to the 
[plans for 2015](https://www.gov.uk/government/collections/river-basin-management-plans-2015).
We get a bit more background information as well:

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

This gives us a very useful figure of £3 billion investment over 6 years. There
will clearly be much interest in how well this is being spent and maximising the
return.

## Data

All this has been incredibly useful and we haven't touched a line of R code or
even any data. We definitely need to remember to have an eye on the data, but a
really key part of being a data scientist is to do background research to scope the
problem. But what data do we have? Well we know we already had located the archive
of water quality measurements. What new, on top of all this context, do we now
have? What do river basins tell us? We can take a look at the 
[River Severn basin](http://environment.data.gov.uk/catchment-planning/RiverBasinDistrict/9).

Well on that page, we get a map, which is nice to tell us the geographic extent of the
basin area (quite big). We have a link for a summary of the basin area. 
Of most interest, however, are the various files available for download. 
Note, lower down, there's a reference to the API. You might have noticed a few 
references to API in all of this. I suggest you don't be overly excited by this.
APIs are great if you're wanting to build an app to do something, but if we're
wanting to do some analysis I'd go for a nice batch download any day. The links
for download are:

* Download Classification Data (the word "classification" always catches my eye)
* Download Measures Data
* Download Reasons for not Achieving Good Data
* Download Objectives Data
* Download water body to protected area links data

Sadly, as is all too common, you'll see little description of what each file
contains. You just have to download and have a look. And then there's no data
dictionary for the columns. It's great to have whizzy UIs for selecting
regions for filtering data (as on their Data downloader link) and cool that they
think of providing an API so developers can programmatically exploit the data.
But for a bulk download to perform an analysis we so often end up with a file
containing varying degrees of descriptively named columns. Let's see if we 
can do some basic categorization of these files

### Classification Data

This file contains data for locations (including water body ID and easting
and northing etc), what water body type it is, its
hydromorphological designation, year and status. The status includes
"Does Not Support Good", "Good", "High" and others. It's not clear whether
"Does Not Support Good" is between Good and Moderate. So on some level this file
would seem to be useful for looking at trends in the status of locations,
but requires some verification of how to interpret the contents.

### Measures Data

This file seems to contain planned improvement measures. It includes the water
body ID, various measure categories and reference codes (undefined), and
estimated start date (presumably of the planned work). It also has a reference
to "Linked RNAGs" which is, as yet, undefined. These data may be useful for
such purpose as verifying planned measures are tackling the biggest problems;
one measure category is "Install nutrient reduction" so we might expect to see
high nutrient measurements in the open WIMS data for this water course.

### Reasons for not Achieving Good Data

Here again we have multiple water course (e.g. lake, river) observations over
time (by year), with water body ID given along with the classiciation status,
level and element. It's not defined what the Classification level refers to
(e.g. "Element") or whether a Classification Status of "Does Not Support Good"
is better than Moderate or Fail? Is Poor a Fail? Finding the ordering of these
statuses is clearly going to be another bit of homework. I have no clue at
the moment what the "Swmi" column refers to or what a Reason type of "RFF" means.
But, essentially, this file could be useful for looking up why a particular bit
of water failed to meet the good standard.

### Objectives Data

I'm really not sure what to make of this file. It's a big one. It has
observations for different water body IDs, a Year column with many dates far
in the future (e.g. 2027), and Classification Level and Classification Item.
There is an ObjectiveType column with values either "Predicted" or "Objective"
(ascertained by eye). This file would seem to contain data about the objective
(or target) to reach for a given body of water, but it's not very clear quite
what to take away from it.

### Water body to protected area links data

This file contains various water body locations, with associated Waterbody ID.
It contains columns for "protected area label" (e.g. "Cardiff Bay") and
protected area type (e.g. "Urban Waste Water Treatment Directive"). This file
may be useful if we wish to prioritize our analysis, for example to sections
of water that relate to protected areas or specific protected categories
such as "Drinking Water Protected Area".

## Summary

Having chosen one of my original project ideas to take forwards and expanding
the paragraph I'd written on it, I've now spent about an evening doing some
research to really firm up what the project proposal could be. This really
feels more like turning the proposal into a brief, but perhaps that's just
semantics. Notice I haven't done any coding whatsoever yet. I could have been
doing this whilst progressing with the initial curriculum material about
getting started with R, RStudio, git, github etc. And none of the above is
in place of a proper description of the data I end up using. Indeed, the
student isn't expected to document this stage of the proceedings. I'm just
doing so in the hope it provides some illumination to the start of your journey.

I believe I now have a nice amount of background material and potential data
sources that I can write up into a new, more formal, proposal. Again, the
student's proposal isn't expected to be pitched quite at this level, but it is,
I think, a nice exemplar to look towards.
