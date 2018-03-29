# Introduction

This is a note detailing my thought processes in performing, and writing up,
my project data wrangling steps.
The entire data wrangling process is performed in an
[R markdown document](data_wrangling.Rmd) and the knitted result is
[here](data_wrangling.md). Note that my primary focus at this stage
is to have a reproducible, documented process. I'm not worried about
suppressing package messages or tidying up any code just yet.
If I (in the future), or anyone else, wanted to know what I did to the
data I use in the next stages, they would be able to refer to just that
one document.

# Downloading and verifying the data

Don't worry if checking md5sum results seems like overkill and you're just
happy to be able to read in a CSV file! It's worth appreciating the general
steps though:

1. Check whether the data are already downloaded
2. Download them if not
3. Check the integrity of the data.

In this case, I'm checking the integrity of the data against what I got the
first time I downloaded them. This means I can start work on another computer,
clone my github repo to get the code, and then run that code and it will pull
down the data as required to that computer. Some data publishers provide their
own checksum references for you so you can check that what you've downloaded is
exactly what you should have. Also, if your mentor wanted to try to replicate
exactly what you've done (maybe to answer a question you have), they could
just clone your repository and run the code and know they have a copy of your data.

# Initial data view

This is an often overlooked, but really important step. Spend some time having
a look at what data fields you've got and what they look like. Do you have a feel
for what the columns represent? Can you understand the values in those columns?
This is where I found it useful to refer back to the glossary provided by the
publisher of the data. This gives us a better idea of what we have in the data.
Nothing of major interest appears to be missing and we seemed to have read in
the data as the expected types. But we notice we're carrying around a lot of
duplicated columns that mostly pertain to the water body details.

# Data deduplication

It would be ideal to gather all of these water body details into a separate
table. During this process is a good opportunity to check that the water body
ID is unique, as expected. The water body data seem unique within files, but
upon trying to join two files we discover there's a problem: somewhere some IDs
are combined!

We find where those IDs are merged and separate them. We then continue to extract
the water body columns, gathering them all together and checking for consistency.
Finally we remove those columns (except the water body ID of course, which is our
key for looking up the details) from the other tables and save the data to files.

# Overall advice

Students shouldn't feel intimidated at some of the data wrangling steps performed
here. The main point to take away is that at this stage we are not doing any
plotting. We certainly haven't started any modeling! We've simply been vetting
the data and trying to make sure it makes sense and is consistent both with
itself and with our current understanding of it. If the specific steps used here
inspire the student, then that's great, but your data will have its own unique
qualities (issues) likely requiring a different set of techniques anyway.

# Data wrangling report

It should now be realised that [the document](data_wrangling.md) detailing the
data wrangling is rather too long for the submission. It's a great reproducible
document, and we should always strive to produce such a document, but it's not
what we'd want to present to a stakeholder who wished to read a higher level
overview of what has been done to the data so far! We'll create that
report [here](data_wrangling_report.md).
