---
layout: post
title: Visualizing the 1911 Census of Ireland
subtitle: Mapping by Townland with Folium
tags: [test]
comments: true
---

The National Archives of Ireland has made both the 1901 and 1911 Census of Ireland publicly available on their [website](http://www.census.nationalarchives.ie/).  In both censuses, each household return is indexed by townland or street.  By combining census data with townland boundary data available on [townlands.ie](https://www.townlands.ie/), we can create maps that can help us visualize the censuses.

## Getting Census Data for a Particular Region

The census website has a webpage for each household return.  Here is an [example](http://www.census.nationalarchives.ie/pages/1901/Down/Kilkeel/Aughnahoory/1234778/):

![alt text](/img/Household_Return.png "Household Return")

This household return is indexed by house number (1), townland or street (Aughnahoory), district electoral division or DED (Kilkeel), county (Down), and year (1901).  Each household return contains information on all of the residents in a particular household in a particular townland or street.

But what if we wanted a table combining all of the household returns for a particular DED in a particular year?  For instance, what if we wanted a table containing information on all of the residents of Kilkeel in 1901, not just the residents in house 1 in the townland of Aughnahoory?

We can create such a table with [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/).  By feeding [this Jupyter Notebook](https://github.com/nrvanwyck/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/Web-Scraping%201901%20and%201911%20Census%20of%20Ireland%20by%20DED.ipynb) the url for a particular DED in a particular year, we can create a giant csv with all of the census information for that DED in that year.  For instance, when we set the seed url to [http://www.census.nationalarchives.ie/pages/1901/Down/Kilkeel/](http://www.census.nationalarchives.ie/pages/1901/Down/Kilkeel/) and run the entire Jupyter Notebook, we create a csv entitled "1901_Down_Kilkeel_raw.csv" that contains all of the census information for Kilkeel in 1901.

## My Data Set

I chose to work with 1911 census data for three different regions in Northern Ireland.  Using the method described above, I obtained raw csvs for, first, twelve DEDs roughly centered around Mount Slemish between the towns of Ballymena and Larne in County Antrim:

* Red Bay
* Broughshane
* Glenravel
* Newtown Crommelin
* Ballyclug
* Glenwherry
* Slemish
* Ardclinis
* Glenarm
* Glencloy
* Carncastle
* Longmore

second, eleven DEDs covering the coast between the Mourne Mountains and the Irish Sea in County Down:

* Bryansford
* Fofanny
* Maghera
* Newcastle
* Ballykeel
* Kilkeel
* Mullartown
* Greencastle
* Mourne Park
* Killowen
* Rosstrevor

and third, twenty-two DEDs located on the east side of Lower Lough Erne, north of the town of Enniskillen, along the border of County Fermanagh and County Tyrone:

* Ballycassidy
* Ballydoolagh
* Castle Coole
* Lisbellaw
* Ballyreagh
* Clabby
* Imeroo
* Newporton
* Tempo
* Magheraculmoney
* Ballinamallard
* Irvinestown
* Lisnarick
* Milltown
* Rockfield or Killadeas
* Drumharvey
* Kilskeery
* Moorfield
* Trillick
* Dromore
* Tullyclunagh
* Carryglass

After obtaining both 1901 and 1911 census information for each of these DEDs in forty-five raw csvs, I combined the data into two csvs, one for the [1901 census](https://github.com/nrvanwyck/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/census_1901_combined.csv) and another for the [1911 census](https://github.com/nrvanwyck/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/census_1911_combined.csv).

To standardize spelling across the two censuses, I manually cleaned the DED and Townland or Street columns of these two csv files.  In addition, I used images of household returns to personally transcribe all of the 1911 census information for the village of Rostrevor, since the website for the National Archives of Ireland was missing transcriptions of those returns.

## Combining Census Data and Townland Data

Each townland has its own webpage on townlands.ie; here is [an example](https://www.townlands.ie/down/mourne/kilkeel/aughnahoory/):

![alt text](/img/Townland_Example.png "Townland Example")

Boundary data for every townland on townlands.ie can be downloaded directly from the site's [download page](https://www.townlands.ie/page/download/).  From this collection of townland boundary data, I created a [GeoJSON file](https://github.com/nrvanwyck/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/townland_boundaries_from_townlands_ie.geojson) that contains only the boundary data for each townland in the forty-five DEDs listed above.

I made two edits to the GeoJSON file to correct boundary data for the townlands of (i) Drumleckney in the parish of Racavan and (ii) Warren in the parishes of Ardclinis and Layd, both in County Antrim, because each of those townlands had been split into two parts on townlands.ie.

In order to integrate the census data and the townland boundary data, I had to match each townland or street in the census data to a townland on townlands.ie.  There were a number of obstacles to merging these two datasets.  The census website groups the townlands by DED, but not by parish; townlands.ie groups the townlands by parish but not by DED.  Further, multiple townlands within a county can share the same name.  On top of that, there are variations in the spelling of place names across the two datasets.

The biggest difficulty is that while most household returns are categorized by townland, others are categorized by street, village, town, or harbour.  Census records do not always give the townland in which these streets, villages, towns, or harbours are located, and some of these locations span more than one townland.

[This csv](https://github.com/nrvanwyck/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/townland_or_street_to_townlands_ie_urls.csv) contains my attempt to map each standardized place name in the cleaned census data to a townland on townlands.ie.  I had to make some simplifying assumptions to map streets, villages, towns, and harbours onto townlands, and some accuracy is inevitably lost in making those simplifying assumptions.

With the census data, the townland boundary data, and a csv to map the former onto the latter, we are ready to begin visualizing the census data.

## Mapping the Census

By importing both the 1911 census data and the townland boundary data into [this notebook](https://github.com/nrvanwyck/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/Mapping%201911%20Census%20of%20Ireland%20with%20Boundary%20Data%20from%20townlands.ie.ipynb), I created a dataframe with demographic information for each townland.  The demographic information includes data on:

* population density
* average age
* average age by sex
* average household size
* number of children born in each household
* differences in the size of male and female populations
* percentage of the population who belong to a particular religion
* percentage of the population who speak the Irish language
* percentage of the people above age eighteen who are illiterate
* percentage of married or widowed people under age sixty who are widowed
* percentage of children deceased

I created a correlation matrix to look for correlations between each of these demographic features.  The most striking correlations I found all involve the percentage of people above age eighteen who are illiterate.  This feature has a very strong positive correlation with the average age in a townland, suggesting that older generations of people alive in 1911 had received less education than younger generations.

The percentage of the population who identified as Catholic is also positively correlated with adult illiteracy, while the percentages of the population who identified as (i) Presbyterian, (ii) Anglican, or (iii) Methodist are all negatively correlated with adult illiteracy.  This negative correlation is more pronounced in the case of Anglicanism than in the case of the other two Protestant denominations.

[The notebook](https://github.com/nrvanwyck/DS-Unit-1-Sprint-5-Data-Storytelling-Blog-Post/blob/master/Mapping%201911%20Census%20of%20Ireland%20with%20Boundary%20Data%20from%20townlands.ie.ipynb) shows how to create visualizations of this demographic information with [Folium](https://github.com/python-visualization/folium) choropleth maps.  We can see that the correlation between age and illiteracy and the correlations between religion and illiteracy are most pronounced in the area around the Mourne Mountains in County Down, and least pronounced in the area around Mount Slemish in County Antrim:

![alt text](/img/Northern_Ireland_1911_Percentage_of_Adult_Illiteracy_gif.gif "Percentage of Adult Illiteracy gif")

All of this just scratches the surface of how we might use townland boundary data to visualize the 1901 and 1911 censuses of Ireland.  In the future, I would like to use maps to visualize differences in surnames across different regions, and I would like to add more interactive features to the maps.  But that's all for now!
