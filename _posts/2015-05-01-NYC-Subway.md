---
published: true
layout: post
date:       2014-06-10 12:31:19
summary: See what the different elements looks like. Your markdown has never looked better. I promise.
categories: jekyll pixyll
---

# NYC Subway Map: A ride to fortune #



On my first visit to New York City in 2006, me and my friends decide to go explore Brooklyn. We entered the underground near Wall Street and twenty minutes later we came up again, this time surrounded by low-income housing. In mere minutes, the subway can virtually *teleport* you between very different settings. From affluence to poverty or vice versa, it can be a ride to fortune.  

I started creating this map after a visit to NYC in 2010. The data is hence a bit old by now and there are a few issues with it (see *Discussion* section below). Nonetheless, I think it depicts the diversity in neighborhoods one can experience when traveling on the NYC Subway.  


** [ The map ] **

*The map is shown here for personal and non-commerical use.*

Another way of looking at this data is to see how the income changes along different subway services. For example riding the Lexington Avenue Express (4 Service) goes from Bronx via Manhattan to Brooklyn and shows two clear peaks, one at the Upper East Side and one in the Financial District. Towards of the end of the line in Bronx the train passes through some of the poorest station in the entire system with average incomes under $25,000 per year.

[[4 Line Graph]]

The same roller-coaster of income can be seen when looking the A Service (8th Avenue Express) which goes from the very north of Manhattan down through Brooklyn to Queens and finally out to the most easterly station in the system.

[[A Line Graph]]



##Technical Discussion##
This map was created using a number of different data sources:


- The MTA Subway map
- [MTA Subway Entrance & Exit GIS Data](http://web.mta.info/developers/)
- [2009 IRS Average Adjusted Gross Income by zip code](http://www.irs.gov/uac/SOI-Tax-Stats-Individual-Income-Tax-Statistics-ZIP-Code-Data-(SOI)) 


To get the income per station the average exit coordinates per station were calculated (which may be different from the actual location of the station itself). To get the coordinates of each zip code the [centroid](http://en.wikipedia.org/wiki/Centroid) was calculated using hte [Yahoo! GeoPlanet API](http://developer.yahoo.com/geo/geoplanet/).

Now I had two sets of data, that basically looked like this:

| Long | Lat | Income |
|-73.996853|40.750325|124797|
| -73.776775 | 40.674832 | 38321 |

| Station | Long | Lat |
|Times Sq-42 St| -73.986680 | 40.754172 |

To get the income "per station" I used bi-linear interpolation of the zip code coordinates to get the income at the stations coordinates. I looked at bi-cubic interpolation but it would overshoot in some instances (ie. the income at a station is higher than any of the incomes in the zip code data).

[Image of interpolation]

The last step was to replace the station names in the map with the average income number. I ended up doing this manually in Inkscape. In hindsight a better solution would of course have been to do this programatically by operating on the XML inside an SVG file (see *Future Work*) and then just doing final touch-up in Inkscape. 

For all the other graphs and visualizations, Python/Numpy/MatPlotLib/Basemap were used.

##Known Issues and Future Work##
When revisiting the data 

If I were to do this project again I'd look into automating the entire work flow, specifically automatically  ## A New Post

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
