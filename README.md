# Twitter Alert: Mapping Natural Disasters with Social Media

## Executive Summary
Social media platforms such as Twitter have become synonymous with everyday life while helping its users to stay connected to the world by virtually reducing the distance from one point to the other to zero.  Twitter can also be considered as an alternate/informal method of communication when all traditional methods are down in case of a natural disaster or unforeseen circumstances. Data available on Twitter can be leveraged to establish communication lines that can help emergency services to react more effectively and efficiently in such cases. Twitter’s infrastructure can support such activities through its robust Application Programming Interface (API). With the help of Twitter’s API and Python’s Natural Language Processing (NLP) capabilities, we can develop pipelines that can streamline information by cutting out the unnecessary noise and delivering key content such as relevant keywords and geolocation to assist recovery efforts.  This project aims to build a prototype that can show the above operation on a smaller scale with web scraped historic tweets related to past incidents and making use of FEMA database. [ADD LINKS TO RESEARCH PAPERS]

## Problem Statement: 
> Design and implement a web-tool or an app for rapid alert and notification about a disastrous event, in close to real time. The tool will alert about the event, similar, for example, to earthquake or tsunami warnings that appear on Google`s home page immediately after a major disaster. While traditional methods for alerting on such events rely on official information derived from official sources (e.g. USGS), this tool will utilize social media activity to identify these events and alert when an event first occurs.

## Contents
In this repo, you will find the following:

A. Twitter Scraping Notebook

B. Data Cleaning / Natural Language Processing / Modeling Notebook

C. Real-Time Twitter Alert Notebook

D. Presentation Slides

[WHAT OTHER THINGS ARE IN THE REPO WE WANT TO ADD / LINK TO?]

This project was developed by Ben Brown, Ryan Dorris, Kate Dowdy, Ryan Fuller, and Sam Singh as part of the Data Science Immersive program at General Assembly in January, 2019. [ADD LINKS TO EVERYONE'S PUBIC GITHUB / LINKEDIN]

Free custom access keys required to run these notebooks: Twitter API ([request access here](https://developer.twitter.com/en/apply-for-access.html)); GeoNames ([request access here](http://www.geonames.org/)).

Software requirements for running these notebooks: Python, Pandas, Scikit Learn, [ADD REST OF PACKAGES]

## Proof of Concept
> To what extent can social media might be trusted to alert on the presence and spread of a natural disaster?

We began the project by asking a few questions to determine the limitations of our solution:

**1. What social media would make sense to use, or would be possible to use, for an alert system?**

At the beginning of the project, we requested API access from Twitter, Facebook, and Instagram. Only Twitter granted us API access and did so with almost no turnaround time. We felt Twitter would be the best platform to use for alerts in realtime given the public nature of Twitter as well as the character limit, and thought it would be less realistic for people in emergency situations to spend the time writing a Facebook post or applying an Instagram filter to their photo of a burning house. 

**2. What disasters would make the most sense for an alert system?**

While weather alerts already exist for major events such as hurricanes and snow storms, the rapid spread of wildfires and unexpected flooding due to severe storms are more difficult to predict and would be an ideal use case for a social media alert system. 

One major limitation of relying on social media to track disasters is the reliance on cell phone towers: in the case of the Woolsey wildfire in November, 2018 in California, towers near Los Angeles and surrounding counties were impacted and as a result, there are no almost no tweets from the first day of the fire in those areas. 

Ultimately, we decided not to filter our data collection based on the type of disaster in order to collect as much data as possible. We limited our research to disasters in the United States and used [FEMA's website](https://www.fema.gov/disasters) to pull a full list of federally declared disasters that had occurred since 2006 (when Twitter was launched), and prioritized collecting data on the most recent disasters.

**3. How would you detect signal from noise to create an effective alert system?**

We pursued two approaches to this part of the project: 

1. The easy way: Guessing words related to disasters and using those to filter real-time tweets. This was one of our strategies for developing our prototype of the realtime alert system.

2. The hard way: Developing a model based on pre-disaster and post-disaster Twitter activity to determine the coefficients associated with post-disaster content. We felt exploring the actual data was important, if for no other reason than to give us an idea of how much noise we would need to filter through to find tweets that might indicate disasters. This part of the project is further explored in our data collection, cleaning/NLP, and modeling sections. Findings from our model supplemented our realtime alert prototype.

# Data Collection
We quickly discovered that Twitter's REST API (for historic tweets) only extends 30 days into the past. Thankfully, as of completing this project, no major natural disasters had occurred in the U.S. in the past month, so we needed to find another way to collect information from past disasters. We wrote code to scrape Twitter with Selenium and BeautifulSoup and collected [TOTAL TWEETS] tweets from 2 days prior and 2 days after (including the day of) the natural disaster for 2000 [CHECK THIS NUMBER] affected counties.  

< insert Sam's Tableau graphic? >

Manually scraping Twitter is an incredibly time-consuming process, and for future reference we would recommend using the API instead if possible.

# Cleaning/NLP

< steps of cleaning process, key decisions made >

< graphics? (comparing top words/phrases from pre-post disaster?) >

# Modeling

< models we tried, best model, baseline comparison, results & confusion matrix >

< graphics? (confusion matrix? ) >

## Product Prototype
> How might a Twitter alert system work in real life?

We secured access to Twitter's Streaming API and built a function to stream live tweets generated from locations within a specified bounding box of geographic coordinates (included in our code is a method to generate the bounding box coordinates using Geopy and GeoNames.) There are several limitations of relying on Twitter's Streaming API that would make this tool a useful supplement to existing disaster alerts but **not** something you would rely on alone. For one: Twitter's Streaming API produces at maximum roughly 1% of tweets generated in realtime. A real alert system would require access to Twitter's Firehose API which is not free. For our prototype, we found using a smaller bounding box (ex. 30 miles around a city) with our limited Streaming API would generate a more concentrated volume of tweets that might better tell a story about a developing situation. Another limitation is that the vast majority of tweets do not have geocodes associated with them (only about 5% of our live tweets did), so the bulk of our location data is an approximation based on the username's hometown. To get specific location information on developing disasters, we would be relying on the user to convey their address in the content of the tweet.

< screenshot/video of demo >

We filtered tweets based on different sets of words associated with different types of disasters [DETAILS TBD] based on intuition and our model coefficients.

## Future Steps
With more time and resources, there are a few extensions to this project we would invest in:

1. Developing our alert prototype into an interactive online dashboard displaying tweets, their frequency, and approximate location.
2. Investing in the Twitter Firehose API to have access to all streaming tweets (which we could use to further train our model)
3. Pulling in official alerts for comparison against our predictions to test our model's accuracy in realtime, improve on classification of tweets related to disasters, and test how much earlier Twitter may be able to alert responders compared to official sources.
